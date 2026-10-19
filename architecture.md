flowchart LR
    Users["Users, tenants<br/>and internal tools"]

    subgraph AWS["AWS · Product Plane"]
        direction TB
        WAF["CloudFront + AWS WAF<br/>managed rules · rate limits"]
        Gateway["APIM Self-hosted Gateway<br/>JWT · rate limits · quotas · schema"]
        TasksAPI["Tasks API<br/>tenant-scoped CRUD"]
        AssistantAPI["AI Assistant API<br/>summarises via LLM"]
        RDS["Amazon RDS<br/>+ pgvector"]
        Egress["Egress Firewall<br/>AI gateway only"]
    end

    subgraph Azure["Azure · AI Plane"]
        direction TB
        Entra["Microsoft Entra ID<br/>tokens · MFA"]
        AIGateway["APIM AI Gateway<br/>token limits · Prompt Shields"]
        Guardrails["Guardrails<br/>PII · Injection"]
        OpenAI["Azure OpenAI<br/>Private Endpoint"]
    end

    Users --> WAF
    WAF --> Gateway
    Gateway --> TasksAPI
    Gateway --> AssistantAPI
    TasksAPI --> RDS
    AssistantAPI --> Egress
    Egress --> AIGateway

    Entra -.-> Gateway
    Entra -.-> AIGateway

    AIGateway --> Guardrails
    Guardrails --> OpenAI
    OpenAI --> Guardrails
    Guardrails --> AIGateway

    classDef policy fill:#d6eaf8,stroke:#2980b9,stroke-width:2px
    class Gateway,AIGateway policy

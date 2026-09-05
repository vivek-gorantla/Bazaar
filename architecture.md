Baazar AI Agent Architecture

Baazar uses a central Agent Orchestrator to understand requests and route them to specialized agents. The architecture supports multimodal merchant input, conversational customer workflows, real-time UI synchronization, and event-driven services.

1. Merchant Architecture

flowchart LR

    subgraph INPUTS["Merchant Inputs"]
        V["Voice"]
        I["Image"]
        T["Text"]
        C["CSV"]
    end

    subgraph PARSER["Parsing Layer"]
        G["Parsing Gateway"]
        VP["Voice Parser"]
        IP["Image Parser"]
        TP["Text Parser"]
        CP["CSV Parser"]
    end

    V --> G
    I --> G
    T --> G
    C --> G

    G --> VP
    G --> IP
    G --> TP
    G --> CP

    VP --> O["Agent Orchestrator"]
    IP --> O
    TP --> O
    CP --> O

    O <--> LLM["Azure OpenAI"]

    O --> PA["Product Agent"]
    O --> IA["Inventory Agent"]
    O --> SA["Supplier Agent"]
    O --> GA["Growth Agent"]
    O --> OA["Onboarding Agent"]

    PA --> S["Business Services"]
    IA --> S
    SA --> S
    GA --> S
    OA --> S

    S --> DB[("PostgreSQL / MongoDB")]
    S --> R[("Redis")]
    S --> K[("Kafka")]

Merchant Request Flow

flowchart LR
    A["Merchant Request"] --> B["Parsing Gateway"]
    B --> C["Agent Orchestrator"]
    C --> D["Intent Detection"]
    D --> E["Specialized Agent"]
    E --> F["Tool / Business Service"]
    F --> G["Database"]
    F --> H["Kafka Event"]

2. Customer Conversational Architecture

flowchart LR

    CUSTOMER["Customer"] --> CCA["Customer Conversational Agent"]

    CCA <--> REDIS[("Redis")]

    CCA --> DISC["Discovery Agent"]
    CCA --> PLAN["Planning Agent"]
    CCA --> PURCHASE["Purchase Agent"]

    DISC --> ORCH["Customer Orchestrator"]
    PLAN --> ORCH
    PURCHASE --> ORCH

    ORCH --> REC["Recommendation Agent"]
    ORCH --> CART["Cart Agent"]
    ORCH --> CHECK["Checkout Agent"]
    ORCH --> PAY["Payment Agent"]
    ORCH --> ORDER["Order Agent"]
    ORCH --> SUPPORT["Support Agent"]

    REC --> SERVICES["Service Layer"]
    CART --> SERVICES
    CHECK --> SERVICES
    PAY --> SERVICES
    ORDER --> SERVICES
    SUPPORT --> SERVICES

    SERVICES --> PRODUCTAPI["Product API"]
    SERVICES --> ORDERAPI["Order API"]
    SERVICES --> PAYMENTAPI["Payment API"]

    SERVICES --> KAFKA[("Kafka")]

3. Dynamic UI & Context Synchronization

The UI Registry keeps track of the current pages and agent-enabled fields. UI context is sent to the backend through WebSockets so the AI can generate structured UI actions.

flowchart LR

    M["Merchant"] --> VOICE["Voice / Text"]

    subgraph FRONTEND["Frontend"]
        FORM["Forms / UI"]
        FIELD["Agent Field Components"]
        REG["UI Registry"]
        EXEC["UI Action Executor"]

        FORM --> FIELD
        FIELD --> REG
        EXEC --> REG
    end

    VOICE --> WS["WebSocket"]
    REG --> WS

    WS --> BACKEND["Backend"]
    BACKEND --> CONTEXT["UI Context"]
    CONTEXT --> LLM["AI Model"]

    LLM --> ACTIONS["Field Actions"]
    ACTIONS --> BACKEND

    BACKEND --> WS
    WS --> EXEC

Example

Merchant:
"My legal name is Ramesh Enterprises and my GST number is XXXXX."

        ↓

UI Context + Voice Input

        ↓

AI Model

        ↓

Fill Fields Action

        ↓

WebSocket

        ↓

UI Action Executor

        ↓

Form Updated

4. Governance & Execution

AI determines what should happen, while deterministic services control how the operation is executed.

flowchart LR

    REQUEST["User Request"]
    INTENT["Intent Detection"]
    POLICY["Policy Engine"]
    APPROVAL["User Approval"]
    TOOL["Agent Tool"]
    SERVICE["Business Service"]
    DB[("Database")]
    EVENT[("Kafka / Audit Log")]
    DENIED["Safe Failure"]

    REQUEST --> INTENT
    INTENT --> POLICY

    POLICY -->|"Allowed"| TOOL
    POLICY -->|"Approval Required"| APPROVAL
    APPROVAL --> TOOL
    POLICY -->|"Denied"| DENIED

    TOOL --> SERVICE
    SERVICE --> DB
    SERVICE --> EVENT

This provides a foundation for:

Authorization

User approval

Spending limits

Explainability

Audit trails

Safe failure handling

5. Core Agents

Agent

Responsibility

Product Agent

Product catalog management

Inventory Agent

Stock tracking and updates

Supplier Agent

Suppliers and purchase orders

Growth Agent

Promotions, upselling, cross-selling and POS

Onboarding Agent

Merchant and store setup

Discovery Agent

Customer product discovery

Planning Agent

Budget and occasion-based shopping

Purchase Agent

Customer purchasing workflows

Recommendation Agent

Recommendations and alternatives

Cart Agent

Conversational cart management

Checkout Agent

Order validation and checkout

Payment Agent

Payment initiation

Order Agent

Order lifecycle

Support Agent

Customer support

6. High-Level System View

flowchart TB

    MERCHANT["Merchant"]
    CUSTOMER["Customer"]

    MERCHANT --> MI["Merchant Interface"]
    CUSTOMER --> CI["Customer Interface"]

    MI --> MG["Merchant Orchestrator"]
    CI --> CG["Customer Conversational Agent"]

    MG --> MA["Merchant Agents"]
    CG --> CA["Customer Agents"]

    MA --> SERVICES["Core Business Services"]
    CA --> SERVICES

    SERVICES --> DATA[("PostgreSQL / MongoDB")]
    SERVICES --> CACHE[("Redis")]
    SERVICES --> EVENTS[("Kafka")]

    SERVICES --> PAYMENT["Razorpay"]

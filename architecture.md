Baazar AI Agent Architecture

Baazar uses a central Merchant Orchestrator to understand user
intent and route requests to specialized agents. The architecture
supports multimodal merchant input, conversational customer workflows,
real-time UI synchronization, and event-driven services.

1. Merchant Agent Orchestration

Merchants can interact using voice, images, text, or CSV. The
Parsing Gateway converts each input into a structured representation
before sending it to the Agent Orchestrator.

graph TD
    subgraph INPUTS["Merchant Inputs"]
        VOICE["Voice Input"]
        IMAGE["Photo Input"]
        TEXT["Text Input"]
        CSV["CSV Input"]
    end

    subgraph PARSING["Parsing Layer"]
        GATEWAY["Parsing Gateway"]
        IMAGE_P["Image Parser"]
        VOICE_P["Voice Parser"]
        TEXT_P["Text Parser"]
        CSV_P["CSV Parser"]
    end

    VOICE --> GATEWAY
    IMAGE --> GATEWAY
    TEXT --> GATEWAY
    CSV --> GATEWAY

    GATEWAY --> IMAGE_P
    GATEWAY --> VOICE_P
    GATEWAY --> TEXT_P
    GATEWAY --> CSV_P

    IMAGE_P --> ORCH["Agent Orchestrator"]
    VOICE_P --> ORCH
    TEXT_P --> ORCH
    CSV_P --> ORCH

    ORCH <--> LLM["Azure OpenAI"]

    ORCH --> PRODUCT["Product Agent"]
    ORCH --> INVENTORY["Inventory Agent"]
    ORCH --> SUPPLIER["Supplier Agent"]

    PRODUCT --> CONTRACT["Product Contract"]
    INVENTORY --> CONTRACT
    SUPPLIER --> CONTRACT

    CONTRACT --> VALIDATE["Zod Validation"]
    VALIDATE --> SERVICES["Business Services"]
    SERVICES --> DB[("Product / Inventory Database")]

Product Contract

The orchestrator converts relevant requests into a validated structured
contract such as:

name
description
category
unit
price
stockQty
attributes
sku

The contract is validated before reaching the business services, keeping
AI-generated data separate from deterministic application logic.

2. Customer Conversational Architecture

Customers interact through a Customer Conversational Agent, which
coordinates discovery, planning, and purchasing workflows.

graph TD
    CUSTOMER["Customer"] --> CCA["Customer Conversational Agent"]

    CCA <--> REDIS[("Redis Cache")]
    CCA --> DISCOVERY["Discovery Agent"]
    CCA --> PLANNING["Planning Agent"]
    CCA --> PURCHASE["Purchase Agent"]

    DISCOVERY --> ORCH["Customer Orchestrator"]
    PLANNING --> ORCH
    PURCHASE --> ORCH

    ORCH <--> RECOMMEND["Recommendation Agent"]
    ORCH <--> CART["Cart Agent"]
    ORCH <--> CHECKOUT["Checkout Agent"]
    ORCH <--> PAYMENT["Payment Agent"]
    ORCH <--> ORDER["Order Agent"]
    ORCH <--> SUPPORT["Support Agent"]

    RECOMMEND --> SERVICES["Service Layer"]
    CART --> SERVICES
    CHECKOUT --> SERVICES
    PAYMENT --> SERVICES
    ORDER --> SERVICES
    SUPPORT --> SERVICES

    SERVICES --> PRODUCT_API["Product API"]
    SERVICES --> ORDER_API["Order API"]
    SERVICES --> PAYMENT_API["Payment API"]

    SERVICES --> KAFKA[("Kafka Events")]

3. Dynamic UI and Context Synchronization

Baazar can synchronize the AI agent with the merchant's current UI
state.

For example, a merchant can say:

"My legal name is Ramesh Enterprises and my GST number is ..."

The frontend sends the current UI context to the backend. The AI
generates structured field actions, which are sent back through
WebSockets and executed by the UI.

graph TD
    subgraph FRONTEND["Frontend / UI Registry"]
        FORM["Form Fields"]
        FIELD["Agent Field Components"]
        REGISTRY["UI Registry"]
        FORM --> FIELD
        FIELD --> REGISTRY
    end

    MERCHANT["Merchant"] --> VOICE["Voice Input"]

    REGISTRY --> WS["WebSocket"]
    VOICE --> WS
    WS --> BACKEND["Backend"]
    BACKEND --> CONTEXT["UI Context"]

    CONTEXT --> LLM["AI Model"]
    LLM --> ACTIONS["Field Actions"]
    ACTIONS --> BACKEND

    BACKEND --> WS
    WS --> EXECUTOR["UI Action Executor"]
    EXECUTOR --> REGISTRY

4. Core Agents

Agent                      Responsibility

Product Agent          Product catalog management
Inventory Agent        Stock tracking and updates
Supplier Agent         Suppliers and purchase orders
Growth Agent           Promotions, upselling, cross-selling and POS
Onboarding Agent       Merchant and store setup
Discovery Agent        Customer product discovery
Planning Agent         Budget, occasion and quantity-based shopping
Purchase Agent         Customer purchasing workflows
Recommendation Agent   Product recommendations and alternatives
Cart Agent             Conversational cart management
Checkout Agent         Order validation and checkout
Payment Agent          Payment initiation and handling
Order Agent            Order lifecycle management
Support Agent          Customer support workflows

5. Execution and Governance

AI agents determine intent and actions, while deterministic services
perform business operations.

graph TD
    REQUEST["User Request"] --> INTENT["Intent Detection"]
    INTENT --> POLICY["Policy / Authorization"]
    POLICY -->|Allowed| TOOL["Agent Tool"]
    POLICY -->|Approval Required| APPROVAL["User Approval"]
    APPROVAL --> TOOL
    POLICY -->|Denied| SAFE["Safe Failure"]

    TOOL --> SERVICE["Business Service"]
    SERVICE --> DATABASE[("Database")]
    SERVICE --> EVENT["Kafka Event / Audit Log"]

This separation provides a foundation for authorization, approvals,
explainability, audit trails, and safe failure handling.

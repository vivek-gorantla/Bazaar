# Baazar AI Agent Architecture

Baazar uses a central **Merchant Orchestrator** that understands user intent and routes requests to specialized agents. The system is designed to handle multiple input modalities, coordinate complex multi-agent interactions on both the merchant and customer sides, and keep the user interface synchronized in real-time.

---

## 1. Merchant Agent Orchestration

Merchants can interact with the system using Voice, Photo, Text, or CSV inputs. These inputs are parsed by a dedicated Parsing Layer before being handed off to the Agent Orchestrator. The Orchestrator uses Azure OpenAI to identify intent, formulate a structured "Product Contract", and delegate execution to the appropriate specialized agent.

```mermaid
graph TD
    subgraph Inputs[Merchant Inventory Inputs]
        VI[Voice Input]
        PI[Photo Based Input]
        NI[NLP Text Input]
        CI[CSV Input]
    end

    subgraph Parsing[Parsing Layer]
        PL[Parsing Gateway]
        IP[Image Parser]
        VP[Voice Parser]
        TP[Text Parser]
        CP[CSV Parser]
    end

    VI --> PL
    PI --> PL
    NI --> PL
    CI --> PL

    PL -->|What the image model can see| IP
    PL -->|Convert voice to text| VP
    PL -->|Basic text normalization| TP
    PL -->|Convert CSV to objects| CP

    IP --> AO[Agent Orchestrator]
    VP --> AO
    TP --> AO
    CP --> AO

    AO <-->|Identify intent call tools| LLM((Azure OpenAI LLM))
    LLM -->|Generate contract / Send agents data| PC[Product Contract<br/>name, description, category, unit, price, stockQty, attributes, sku]

    PC --> JV{Zod Validation}
    JV --> IS[Inventory Service]
    IS --> DB[(Product Catalog Database)]

    AO --> PA{Product Agent}
    AO --> IA{Inventory Agent}
    AO --> SA{Supplier Agent}

    PA -->|Understand merchant input & convert it into a Product Contract| QC[Query Catalog Commands]
    IA -->|Its job is understanding inventory| QC
    SA -->|This is the general-purpose conversational agent| QC
    QC --> DB
```

---

## 2. Customer Conversational Architecture

On the customer side, the interactions are managed by a **Customer Conversational Agent** which caches data via Redis and coordinates distributed events via Kafka. The conversational agent fans out to discovery, planning, and purchase agents, which then connect back to the core orchestrator.

```mermaid
graph TD
    C((Customer)) --> CCA[Customer Conversational Agent]
    
    CCA <--> Redis[(Redis Caching)]
    Redis <--> Kafka((Kafka))

    CCA --> DA[Discovery Agent]
    CCA --> PA1[Planning Agent]
    CCA --> PuA[Purchase Agent]

    DA --> O((Orchestrator))
    PA1 --> O
    PuA --> O

    O <--> RA[Recommendation Agent]
    O <--> PayA[Payment Agent]
    O <--> CA[Cart Agent]
    O <--> ChA[Checkout Agent]
    O <--> SupA[Support Agent]
    O <--> OA[Order Agent]

    RA --> SL[Service Layer]
    PayA --> SL
    CA --> SL
    ChA --> SL
    SupA --> SL
    OA --> SL

    SL --> APIs[APIs]
    APIs --> ProdAPI{Product API}
    APIs --> OrdAPI{Order API}
    APIs --> PayAPI{Payment API}
```

---

## 3. Dynamic UI & Context Synchronization

Baazar heavily relies on a dynamic, AI-driven UI. The UI Registry on the frontend stores all pages and fields visited by the user. When a merchant provides an instruction (like filling out a form via voice), the UI context is sent over WebSockets to the backend, enabling the AI to directly fill in the corresponding fields on the merchant's screen.

```mermaid
graph TD
    subgraph Frontend [Frontend / UI Registry]
        FF[Form Fields: Store Details] --> AF[Agent Field Component]
        AF --> UIR[UI Registry: Stores all pages and fields as visited by user]
        UIR --> |Creates a file to register the component| Ex[Agent Field Input Example]
    end

    subgraph Merchant Interaction
        M((Merchant)) -->|Voice Input: 'My legal name is Ramesh Enterprises and my GST number is...'| LLM2((LLM))
    end
    
    subgraph Backend & Real-time
        UIR -->|Send pages to backend as context| WS[WebSocket]
        WS --> B[Backend]
        B --> UC[UI Context]
        UC --> LLM2
        
        LLM2 -->|AI produces actions| FFT{Fill Fields Tool}
        FFT --> B
        B -->|Sends actions payload back| WS
        WS -->|Creates executor to fill fields in UI| UIR
    end
```

---

## Core Agents Breakdown

| Agent                | Responsibility              |
| -------------------- | --------------------------- |
| **Product Agent**    | Product catalog management  |
| **Inventory Agent**  | Stock tracking & updates    |
| **Supplier Agent**   | Suppliers & purchase orders |
| **Growth Agent**     | Promotions, upselling & POS |
| **Onboarding Agent** | Merchant/store setup        |

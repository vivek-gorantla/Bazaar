# Baazar

> **AI-powered infrastructure for local retail.**

Baazar is an AI-powered hyper local commerce platform designed to help local merchants digitize and manage their businesses through a conversational interface.

Merchants can manage their **products, inventory, suppliers, procurement, promotions, and checkout** using text, voice, images, or CSV inputs.

For customers, Baazar provides **AI-powered product discovery, recommendations, conversational carts, checkout, and order support**.

---

# Problem Statement: AI Growth & Agentic Commerce

Baazar directly tackles the challenge of **growing merchant revenue and making stores transactable by AI buyers end-to-end**. In the emerging era of agent-to-agent commerce (fueled by NPCI's UAP and global protocol races), local merchants need intelligent infrastructure to participate.

Baazar aligns with this vision through:
* **Conversational In-App Checkout:** Customers and AI buyers can build carts, validate stock, and checkout entirely through a natural language interface.
* **Agent-Readable Catalog:** The Parsing Gateway converts unstructured inputs (voice, photos, text) into strict, Zod-validated "Product Contracts", ensuring the merchant's inventory is perfectly formatted for AI consumption.
* **Upsell, Cross-Sell & Campaign Orchestrator:** A dedicated `Growth Agent` actively runs discount campaigns and configures intelligent cross-selling at the Point of Sale to maximize revenue.
* **Bounded & Gated Money Actions:** Every financial transaction (handled via Razorpay test-mode APIs) is strictly explainable and bounded. The `Payment Agent` requires explicit customer approval before any money moves. Furthermore, all agent actions are streamed to Kafka to provide a complete, immutable audit trail and allow for graceful failure handling.

---

## Core Features

### Merchant

* Conversational store management
* Voice, image, text & CSV inputs
* Product & inventory management
* Supplier & procurement management
* Discounts, upselling & cross-selling
* Conversational POS & checkout
* Automated merchant onboarding

### Customer

* Intelligent product discovery
* Personalized recommendations
* Conversational shopping & cart
* Budget/occasion-based shopping plans
* AI-assisted checkout & payments
* Order tracking, cancellations & support

---

# AI Agent Architecture

Baazar relies on a sophisticated multi-agent orchestration system to handle interactions for both merchants and customers, including dynamic UI synchronization and multimodal inputs.

For a deep dive into the system flows and detailed architecture diagrams, please see the [Architecture Overview](architecture.md).

---

# Multi-Modal Interaction

Baazar supports multiple merchant input formats:

```text
Text ──────┐
Voice ─────┤
Image ─────┼──► Parsing Gateway ──► AI Orchestrator
CSV ───────┘
```

Example:

> **"Add 50 packets of Tata Salt to inventory."**

The orchestrator identifies the intent and routes it to the **Inventory Agent**, which performs the appropriate operation.

---

# Tech Stack

### Frontend

* Next.js
* React 19
* TypeScript
* Tailwind CSS v4
* shadcn/ui
* Framer Motion

### Backend

* Node.js
* Express.js
* TypeScript
* OpenAI SDK
* ElevenLabs
* WebSockets
* SSE

### Infrastructure

* PostgreSQL + Prisma
* MongoDB + Mongoose
* Redis
* Kafka
* Razorpay
* Multer

---

# Project Structure

```text
baazar/
├── frontend/
│   ├── app/
│   ├── components/
│   └── ...
│
├── backend/
│   ├── src/
│   │   ├── agents/
│   │   │   ├── product-agent/
│   │   │   ├── inventory-agent/
│   │   │   ├── supplier-agent/
│   │   │   ├── growth-agent/
│   │   │   └── onboardingAgent.ts
│   │   ├── orchestrator/
│   │   ├── parsing/
│   │   ├── routes/
│   │   ├── controllers/
│   │   └── services/
│   ├── prisma/
│   └── package.json
│
└── README.md
```

---

# Getting Started

### Prerequisites

* Node.js 20+
* PostgreSQL
* MongoDB
* Redis
* Kafka *(optional depending on configuration)*

### Backend

```bash
cd backend
npm install
npm run dev
```

### Frontend

```bash
cd frontend
npm install
npm run dev
```

Frontend runs on:

```text
http://localhost:3001
```

### Environment Variables

```env
DATABASE_URL=
MONGODB_URL=
REDIS_URL=
KAFKA_BROKERS=

OPENAI_API_KEY=
ELEVENLABS_API_KEY=

RAZORPAY_KEY_ID=
RAZORPAY_KEY_SECRET=
```

# Vision

Baazar aims to make **every local store digital, discoverable, and intelligent**.

Instead of merchants learning complicated retail software, Baazar lets them simply **talk to their business**.

> **Baazar — AI-powered retail infrastructure for the next generation of local commerce.**

# Low-Code AI Workflow Automation Platform - Project Design

## 1. Project Overview

This project is a **low-code / no-code AI workflow automation platform** designed to allow non-technical users to visually design, execute, and deploy **generative AI pipelines**. It emphasizes orchestration over simple prompting, enabling complex, repeatable workflows.

### Key Features
- **Visual Builder:** Drag-and-drop interface creating Directed Acyclic Graphs (DAGs) of AI operations.
- **Reusable Templates:** Save successful pipelines as templates.
- **Flexible Execution:** Run on-demand, schedule as background jobs, or expose as REST APIs.
- **Zero Cost Architecture:** Built entirely on free-tier services and open-source components.

---

## 2. Core Problem Solved

The platform addresses the limitations of current AI tools which are often:
- Single-purpose and hard-coded.
- Lacking in reusability and composability.
- Difficult for non-technical users to chain into complex workflows.

**Solution:**
- **Dynamic Orchestration:** Users define the flow logic visually.
- **Separation of Concerns:** Decoupling logic definition from execution and deployment.
- **Democratized Access:** Making complex multi-step agents accessible without code.

---

## 3. High-Level Architecture

The system is composed of several logical layers:
1.  **Frontend (Low-Code Builder):** React-based visual editor.
2.  **Pipeline Definition Layer:** JSON schema defining the DAG structure.
3.  **Pipeline Execution Engine:** Core logic processor (Django).
4.  **Integration Layer:** Connectors for external services.
5.  **Deployment & Trigger Layer:** API endpoints and background workers.
6.  **Knowledge Base (RAG):** Storage and retrieval for context.
7.  **Persistence & Templates:** Database for storing definitions and results.

---

## 4. Tech Stack (Free-Only Constraint)

### Frontend
- **Framework:** React (Vite)
- **State Management:** Zustand or Redux Toolkit
- **Visual Editor:** React Flow
- **Styling:** Tailwind CSS

### Backend
- **Framework:** Django & Django REST Framework (DRF)
- **Real-time:** Django Channels
- **Task Queue:** Celery
- **Database:** PostgreSQL (Free Tier, e.g., Railway/Render) / SQLite (Dev)
- **Broker/Cache:** Redis (Upstash Free Tier)

### AI / Intelligence
- **LLM Inference:** Local LLM via **Ollama** (LLaMA-3, Mistral, Phi-3).
- **Embeddings:** Sentence Transformers.
- **Vector Store:** FAISS or ChromaDB.

### Integrations
- **APIs:** Gmail, Discord Webhooks, Notion, Google Sheets.

### Development & Deployment
- **Backend Hosting:** Railway / Render.
- **Frontend Hosting:** Vercel / Netlify.
- **Auth:** JWT (SimpleJWT).

---

## 5. Core Concepts & Data Structures

### 5.1 Pipeline Definition
Stored as a JSON object representing the DAG structure.
```json
{
  "id": "pipeline_123",
  "name": "Summary Generator",
  "nodes": [
    { "id": "1", "type": "input_text", "config": {} },
    { "id": "2", "type": "llm_chat", "config": { "model": "llama3" } }
  ],
  "edges": [
    { "source": "1", "target": "2" }
  ]
}
```

### 5.2 Block Types (Nodes)
- **Input:** Text, File, JSON, Variables.
- **LLM:** Prompt templates, Model selection, System/User prompts.
- **Knowledge Base (RAG):** Document retrieval and context injection.
- **Logic:** Conditionals, Fan-out/Fan-in, Iteration.
- **Integration:** API calls to Gmail, Discord, Notion, etc.
- **Output:** Final result format (JSON, File, API Response).

### 5.3 Execution Engine
The engine processes the DAG:
1.  **Validation:** Check for cycles and configuration errors.
2.  **Topological Sort:** Determine the execution order.
3.  **Execution:** Run nodes, passing outputs as inputs to downstream nodes.
4.  **Context Management:** Maintain a `context` object with all intermediate data.

---

## 6. Execution & Deployment Strategies

### 6.1 Interactive Runs (WebSocket)
Real-time feedback during pipeline creation and testing via Django Channels.

### 6.2 Background Workers (Celery)
For long-running tasks, scheduled jobs, or high-volume processing. Relies on Redis as the broker.

### 6.3 API Exposure
Every pipeline can be triggered via a REST endpoint:
`POST /api/pipelines/{id}/run`
With payload validation based on the pipeline's defined inputs.

---

## 7. Knowledge Base (RAG)
(Optional but powerful feature)
- **Ingestion:** Upload documents (PDF, Text).
- **Processing:** Chunk text and generate embeddings.
- **Retrieval:** Semantic search against user queries to augment LLM prompts.

---

## 8. Data Models (Simplified)

- **User:** Authentication and ownership.
- **Pipeline:** Stores the DAG definition and metadata.
- **Execution:** Tracks a specific run, status (Pending, Running, Completed, Failed), logs, and outputs.
- **Template:** Versioned, parameterized pipeline definitions for reuse.
- **KnowledgeDocument:** Stores raw text and vector references.

---

## 9. Future Extensions
- Webhook triggers for external events.
- Advanced Agent Memory.
- Public Template Marketplace.
- Multi-tenant organization support.

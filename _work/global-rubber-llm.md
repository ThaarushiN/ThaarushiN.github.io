---
title: "LLM-Based Document Analyzer and Chatbot System"
category: "Professional"
role: "Intern AI Developer, Global Rubber Industries Pvt Ltd"
date: 2025-08-01
excerpt: "Developed a document analysis and conversational AI platform leveraging Azure Document Intelligence and Azure OpenAI, optimizing internal knowledge access."
header:
  overlay_image: /assets/images/RAG.png
  overlay_filter: 0.3
tags: [LLM, Azure, FastAPI, AI Engineering]
layout: single
author_profile: true
read_time: true
share: true
related: true
---

At **Global Rubber Industries**, I engineered a production-grade Retrieval-Augmented Generation (RAG) system for querying confidential business documents — reports, contracts, and spreadsheets — through natural language. Designed for multi-user, high-concurrency environments where data isolation and reliability are critical.



### System Design

#### **Session Isolation & Security**

Each user session maintains a fully independent data scope. Documents, vectors, and chat history are automatically purged on expiry with no manual intervention.

* **Redis** provides shared session state across FastAPI workers, enabling horizontal scaling.
* **HMAC-SHA256 cookie signing** (`itsdangerous`) prevents session tampering at the transport layer.
* **Redis TTL** drives automatic cleanup of all session-scoped data.

#### **Scalable Document Ingestion**

* **Azure Document Intelligence Async Clients** eliminate thread-blocking during OCR-heavy document reads.
* A **Redis-based Global Semaphore** enforces distributed rate limiting across workers, preventing external API quota exhaustion.
* **Parallel page windowing** processes large PDFs concurrently, reducing end-user wait times.

#### **Structured Tabular Data Handling**

Standard RAG pipelines lose table structure on ingestion. A custom pipeline preserves it:

* A **heuristic header detection algorithm** identifies header rows in unformatted Excel files using non-null density, uppercase ratio, and numeric distribution scoring.
* Tables are stored as **annotated DataFrames** with full metadata (filename, sheet, table name), enabling the LLM to reason about specific cells and relationships accurately.

#### **LLM-Driven Visualization**

* **Tool Calling** enables the LLM to extract quantitative data into structured JSON and trigger a chart generation engine automatically.
* Supports bar, line, scatter, and pie charts with context-aware labels and titles.



### Outcomes

* Resolved a **deadlock under load** caused by semaphore tokens being acquired at worker startup but never released — traced and fixed by redesigning async resource lifecycle management.
* Achieved stable multi-worker operation with zero cross-session data leakage in testing.



### Deployment

**Stack:** FastAPI · ReactJS · Redis · ChromaDB · Azure Document Intelligence · OpenAI API

A conversational interface supports file upload, multi-turn querying, and live chart generation — packaged for deployment as an internal analyst tool.

---

### Infrastructure

| Layer | Technology |
|---|---|
| Backend | Python, FastAPI |
| Session Store | Redis (TTL, Semaphore) |
| Document OCR | Azure Document Intelligence (Async) |
| Vector Store | ChromaDB (session-scoped) |
| LLM | OpenAI API (Tool Calling) |
| Frontend | ReactJS |
| Security | `itsdangerous`, HMAC-SHA256 |

**Impact:**  
Empowered employees to retrieve critical operational data through natural language queries, significantly improving internal knowledge accessibility and productivity.

---

<div style="margin-top: 3rem; padding-top: 2rem; border-top: 1px solid #ddd; text-align: center;">
  <a href="/projects/#professional-projects" style="font-weight: 500; color: #0d341c; text-decoration: none;">← See All Projects</a>
</div>

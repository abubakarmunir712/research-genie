# Research Genie Architecture

> **Goal:** Outline the microservice architecture and data flow for the Research Genie project, using Backend to orchestrate Scraper and AI/RAG.

---

## Microservices Overview

| Microservice | Role                                                                                  |
| ------------ | ------------------------------------------------------------------------------------- |
| Frontend     | UI for user queries; sends domain/topic to backend and displays results.              |
| Backend      | Orchestrates workflow, calls Scraper and AI services, manages database interactions.  |
| Scraper      | Fetches papers from APIs / websites, preprocesses PDFs, stores raw text & embeddings. |
| AI / RAG     | Generates final research gap summary using papers received from Backend.              |

---

## Data Stores

| Store                                   | Purpose                                                             |
| --------------------------------------- | ------------------------------------------------------------------- |
| Database (PostgreSQL / MongoDB)         | Stores metadata, raw text chunks, processed summaries.              |
| Vector DB (Pinecone / FAISS / Weaviate) | Stores embeddings of text chunks for similarity search and caching. |
| Storage (S3 / local)                    | Stores original PDFs for archival / future processing.              |

---

## Architecture Flow

```
User
  │
Frontend (React / Next.js)
  │  --POST query--> Backend (FastAPI)
  │
Backend
  ├─> Vector DB (check embeddings for cached summaries)
  │       │
  │   If found → return summary → Frontend
  │
  └─> Scraper Microservice (fetch & preprocess new papers)
          │
          ├─> External APIs / Websites (arXiv, CORE, Unpaywall, PMC, PLOS, MDPI)
          │
          └─> Database / Vector DB (store raw text + metadata + embeddings)

Backend → Sends all newly scraped papers → AI / RAG Microservice
          │
          └─> LLM generates final research gap summary

AI → Backend → Frontend (display final gaps and insights)
```

---

## Notes on Flow

1. **Vector DB caching:** Backend checks for previous embeddings to quickly return cached results.
2. **Single Scraper call:** Backend orchestrates; scraped papers are passed directly to AI.
3. **Async tasks:** Scraper and preprocessing can run asynchronously to improve performance.
4. **Final output:** Structured JSON summarizing research gaps is sent back to Frontend.

---

*This Markdown diagram reflects the agreed-upon architecture using the approach where Backend passes scraped papers to AI.*

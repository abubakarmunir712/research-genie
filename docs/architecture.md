# Research Genie Architecture

## Microservices Overview

| Microservice | Role |
| ------------- | ---- |
| **Frontend** | Provides user interface for submitting queries and displaying AI-generated insights. |
| **Backend** | Acts as the central orchestrator — manages workflow, caching, communication between services, and database interactions. |
| **Scraper** | Fetches research papers from APIs/websites, processes PDFs using Grobid, extracts structured text, generates embeddings, and stores data in databases. |
| **AI / RAG** | Uses LLM (e.g., Gemini) to analyze retrieved content and generate summaries, research gaps, and insights. |

---

## Data Stores

| Store | Purpose |
| ------ | -------- |
| **Database (PostgreSQL / MongoDB)** | Stores metadata, structured text chunks, and processed outputs. |
| **Vector DB (Pinecone / FAISS / Weaviate)** | Stores embeddings of text chunks for semantic search and retrieval. |
| **Storage (S3 / Local)** | Stores original PDFs and other raw files for future processing. |

---

## Architecture Flow

```mermaid
graph TD

%% Frontend
subgraph Frontend
    A1[User Interface]
    A2[Query Input Form]
    A3[Display AI Results]
end

%% Backend
subgraph Backend
    B1[Request Router API]
    B2[Cache Check Vector DB]
    B3[Orchestration and Workflow]
    B4[Send Data to Scraper or AI]
end

%% Scraper
subgraph Scraper
    S1[Discovery Engine: OpenAlex arXiv CORE]
    S2[PDF Downloader]
    S3[Grobid Processor: Extract structured sections]
    S4[Chunking and Embedding Generation]
    S5[Store Metadata Text Embeddings in DB and Vector DB]
end

%% AI/RAG
subgraph AI
    AI1[Receive Relevant Chunks]
    AI2[Process with LLM Gemini]
    AI3[Generate Summaries Research Gaps Insights]
end

%% Connections
A1 --> A2 --> A3
A2 --> B1
B1 --> B2
B2 --> B3
B3 -->|Cache Miss| S1
S1 --> S2
S2 --> S3
S3 --> S4
S4 --> S5
S5 --> B4
B4 --> AI1
AI1 --> AI2 --> AI3
AI3 --> B3
B3 --> A3

```

---

## Notes

1.  **Backend Orchestration:** Controls data flow between all microservices.
2.  **Scraper Preprocessing:** Handles downloading, text extraction, and embedding generation.
3.  **Vector DB Caching:** Reduces redundant work and improves response time.
4.  **AI Integration:** Works on retrieved or newly processed data to create responses.
5.  **Asynchronous Execution:** Scraping and embedding tasks can run in background workers for scalability.
6.  **Modular Design:** Each microservice (Scraper, Backend, AI) is independent and scalable.
# ProductionGradeRAGPythonApp

A production-ready Retrieval Augmented Generation (RAG) web application to interact with your PDFs using AI. Ask questions in natural language and receive answers sourced directly and precisely from your documents—with robust infrastructure to ensure reliability, cost control, and observability.

---

## 🚀 What Does This Project Do?

- **Web-based Chatbot:** Upload a PDF (resume, contract, invoice, etc.) and ask questions about its content.
- **Cited Answers:** Get relevant, concise answers to your questions, sourced directly from the document, and cited for transparency.

---

## 🛠 Why Is This Different?

Many "AI demos" fail in real-world conditions. This app is built for reliability (“Day 2” DevOps and Operations):

- **Observability:** Every step is logged and visualized to help you locate issues quickly.
- **Automatic Retries:** If a network/API fails, the system retries instead of crashing.
- **Rate Limiting & Throttling:** Prevents spamming and costly API overuse.
- **Event-Driven Workers:** Handles long, slow PDF tasks without freezing the UI.

---

## 🏗 Architecture Overview

### Tech Stack

- **Python**: Core backend logic
- **Streamlit**: User interface (frontend)
- **Qdrant (Docker)**: Vector database for retrieving document semantics
- **OpenAI (GPT-4o-mini)**: LLM for generating accurate answers
- **LlamaIndex**: Reads and chunks PDF files
- **Inngest**: Orchestrates event flow, steps, and reliability

### High-Level Workflow

#### Workflow A: **Ingestion**

1. **Upload**: User uploads PDF via Streamlit.
2. **Inngest Event**: Signals the backend to start processing.
3. **Chunking**: PDF is split into manageable text chunks.
4. **Embedding**: Each chunk is vectorized using OpenAI.
5. **Upsert**: Vectors and texts are stored in Qdrant.

#### Workflow B: **Querying**

1. **Ask**: User asks a question.
2. **Inngest Event**: Starts a query workflow.
3. **Vector Search**: Your question is vectorized; Qdrant retrieves related document chunks.
4. **LLM Generation**: GPT-4o uses ONLY the retrieved context to answer—minimizing hallucinations.
5. **Result:** Answer is presented with source citations.

### Why Inngest?

Rather than writing one monolithic script, Inngest breaks logic into steps (e.g., `load-file`, `embed-data`, `save-to-db`). If a step fails (like saving to DB), only that part is retried—making the app crash-proof for network errors.

---

## ⚡️ Getting Started

### Prerequisites

- Python 3.10+ with pip
- Docker (to run Qdrant locally)
- OpenAI API key
- (Recommended) Inngest account for production orchestration

### Installation

1. **Clone repository:**
    ```sh
    git clone https://github.com/tusharrajj/Rag-Ai-agent.git
    cd Rag-Ai-agent/ProductionGradeRAGPythonApp-main
    ```

2. **Install dependencies:**
    ```sh
    pip install -r requirements.txt
    # or use pyproject.toml/uv depending on your tooling
    ```

3. **Run Qdrant (in a new terminal):**
    ```sh
    docker run -p 6333:6333 -p 6334:6334 qdrant/qdrant
    ```

4. **Configure environment variables:**  
   Create a `.env` and add:
    ```
    OPENAI_API_KEY=your-key-here
    INNGEST_API_BASE=http://127.0.0.1:8288/v1
    ```

5. **Run the Streamlit frontend:**
    ```sh
    streamlit run streamlit_app.py
    ```

6. **Run the backend FastAPI app:**
    ```sh
    uvicorn main:app --reload
    ```

---

## 📄 Usage

1. **Upload your PDF** via the UI.
2. **Wait for ingestion** (the status is visualized).
3. **Ask any question** about your document (“What’s the candidate’s experience?”).
4. **Get fast, accurate, and sourced answers.**

---

## 🏛️ File/Directory Structure

- `main.py`: Inngest-powered FastAPI backend—handles ingestion and querying workflows.
- `streamlit_app.py`: Streamlit UI for uploads and chat interface.
- `data_loader.py`: PDF reading, chunking, vectorization logic.
- `vector_db.py`: Qdrant storage abstraction (vector database).
- `custom_types.py`: Data type definitions for strong typing.
- `pyproject.toml`, `uv.lock`: Package/environment management.

---

# AI Document Research (Chat with PDFs)

A modern full-stack web application that allows users to upload PDF documents (research papers, legal contracts, financial reports, technical documentation) and chat with them using semantic search, vector embeddings, and an LLM with precise page citations.

---

## 🌟 Key Features

1. **PDF Ingestion & Text Processing**:
   - Extracts text page-by-page preserving 1-indexed page numbers.
   - Splits text into overlapping semantic chunks with LangChain's `RecursiveCharacterTextSplitter`.
   - Asynchronous background processing pipeline with live status tracking (`uploading`, `processing`, `ready`, `failed`).

2. **Vector Embeddings & Search**:
   - OpenAI `text-embedding-3-small` / `text-embedding-3-large` embedding support.
   - Unified vector store abstraction supporting **Local FAISS / NumPy vector index** (with disk persistence) and **Pinecone Cloud Vector Database**.
   - Resilient fallback embeddings for offline/testing mode.

3. **Grounded RAG Pipeline & Citations**:
   - Strict system prompt that guarantees answers are grounded strictly in the uploaded document context.
   - Exact source citations showing document name, page number, and chunk snippet.
   - Real-time Server-Sent Events (SSE) token streaming.

4. **Interactive Glassmorphic UI**:
   - React + Tailwind CSS with dark mode aesthetics.
   - Drag & drop PDF uploader with live upload progress.
   - Document Library dashboard with metrics (total pages, chunks, ready status).
   - Document Viewer Modal (inspect extracted chunks or preview raw PDF).
   - Multi-turn conversational research workspace with copy, starter prompts, and top-k retrieval slider.
   - Runtime Settings modal to update OpenAI API Key, LLM models, and chunking parameters.

---

## 📁 Repository Structure

```
.
├── backend/
│   ├── app/
│   │   ├── main.py                  # FastAPI application entrypoint & CORS
│   │   ├── core/
│   │   │   └── config.py            # Pydantic Settings configuration
│   │   ├── api/
│   │   │   └── routes/
│   │   │       ├── documents.py     # Upload, list, detail, preview, delete
│   │   │       ├── chat.py          # RAG query, SSE streaming, sessions
│   │   │       └── health.py        # Health check & runtime settings
│   │   ├── services/
│   │   │   ├── pdf_processor.py     # Text extraction & chunking (pypdf/pdfplumber)
│   │   │   ├── embeddings.py        # OpenAI embeddings & fallback generator
│   │   │   ├── vector_store.py      # Local FAISS/NumPy & Pinecone vector stores
│   │   │   └── rag_chain.py         # LangChain RAG pipeline & token streamer
│   │   ├── models/
│   │   │   ├── db_models.py         # SQLAlchemy models (Document, Chunk, Session, Message)
│   │   │   └── schemas.py           # Pydantic request/response schemas
│   │   └── db/
│   │       ├── session.py           # SQLite database session
│   │       └── init_db.py           # Table initialization
│   ├── storage/                     # Uploaded files & vector database index
│   ├── requirements.txt
│   ├── .env.example
│   ├── .env
│   └── Dockerfile
│
├── frontend/
│   ├── src/
│   │   ├── components/
│   │   │   ├── layout/
│   │   │   │   ├── Header.jsx       # Top navigation & system status
│   │   │   │   └── Sidebar.jsx      # Navigation, sessions & document list
│   │   │   ├── documents/
│   │   │   │   ├── DocumentUpload.jsx
│   │   │   │   ├── DocumentCard.jsx
│   │   │   │   ├── DocumentList.jsx
│   │   │   │   └── DocumentViewerModal.jsx
│   │   │   ├── chat/
│   │   │   │   ├── ChatWindow.jsx
│   │   │   │   ├── ChatInput.jsx
│   │   │   │   ├── MessageBubble.jsx
│   │   │   │   └── SourceCitation.jsx
│   │   │   └── common/
│   │   │       └── SettingsModal.jsx
│   │   ├── hooks/
│   │   │   ├── useDocuments.js      # Polling, upload & management hook
│   │   │   └── useChat.js           # SSE streaming, session & citations hook
│   │   ├── services/
│   │   │   └── api.js               # API service layer
│   │   ├── pages/
│   │   │   ├── Dashboard.jsx        # Document library view
│   │   │   └── DocumentChat.jsx     # Research chat workspace
│   │   ├── App.jsx
│   │   ├── index.css
│   │   └── main.jsx
│   ├── package.json
│   ├── tailwind.config.js
│   └── vite.config.js
└── README.md
```

---

## 🚀 Quick Start Guide

### 1. Start Backend

```bash
cd backend
# Create and activate virtual environment
python -m venv .venv
# On Windows:
.\.venv\Scripts\activate
# On macOS/Linux:
# source .venv/bin/activate

# Install dependencies
pip install -r requirements.txt

# Run the FastAPI server
uvicorn app.main:app --reload --port 8000
```

Backend will be available at: `http://localhost:8000` (API documentation at `http://localhost:8000/docs`).

### 2. Start Frontend

```bash
cd frontend
# Install dependencies
npm install

# Run the Vite development server
npm run dev
```

Frontend will be available at: `http://localhost:5173`.

---

## ⚙️ Environment Variables (`backend/.env`)

| Variable | Default | Description |
|---|---|---|
| `OPENAI_API_KEY` | `""` | OpenAI API Key (or set at runtime via UI settings) |
| `CHAT_MODEL` | `gpt-4o-mini` | Model for answering questions |
| `EMBEDDING_MODEL` | `text-embedding-3-small` | Model for embedding chunks & queries |
| `VECTOR_BACKEND` | `faiss` | Vector store backend (`faiss` or `pinecone`) |
| `PINECONE_API_KEY` | `""` | Pinecone API key (if using Pinecone) |
| `PINECONE_INDEX_NAME`| `ai-doc-research` | Pinecone index name |
| `CHUNK_SIZE` | `1000` | Target chunk size in characters |
| `CHUNK_OVERLAP` | `150` | Overlap between adjacent chunks |

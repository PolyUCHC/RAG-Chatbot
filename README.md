# PDF RAG Chatbot (FastAPI + Chroma + Ollama)

[![Python](https://img.shields.io/badge/Python-3.10%2B-blue)](https://www.python.org/)
[![FastAPI](https://img.shields.io/badge/FastAPI-ready-009688)](https://fastapi.tiangolo.com/)
[![License](https://img.shields.io/badge/License-MIT-informational)](#license)

Upload a PDF → index it into a local vector store → ask questions with grounded context.

## UI
![App Screenshot](docs/screenshot_ui.png)

## Upload

![Upload & Reindex](docs/screenshot_upload.png)

## Chat

![Chat & Sources](docs/screenshot_chat.png)

---

## ✨ Highlights

- **Simple flow**: Upload → Index → Chat
- **Local-first**: embeddings from a local HuggingFace model folder, answers from **Ollama**
- **Fast retrieval**: **ChromaDB** vector store (persisted on disk)
- **Optional OCR** for scanned PDFs (Tesseract + Poppler)
- **Clean UI**: sidebar actions + full-height chat

---

## 🧱 Tech Stack

- Backend: **FastAPI**
- Vector store: **ChromaDB**
- Embeddings: **HuggingFace embeddings** (local folder)
- LLM: **Ollama**
- PDF loading: **PyMuPDF**
- Frontend: static HTML + JS + CSS (served by FastAPI)

---

## 📁 Project Structure

```text
.
├─ app.py                 # FastAPI backend (if yours is named "app", rename to app.py)
├─ static/                # Frontend
│  ├─ index.html
│  ├─ app.js
│  └─ style.css
├─ requirements.txt
├─ requirements-ocr.txt   # optional
├─ .env.example
└─ .gitignore
```

---


## 🚀 Quickstart

### 1) Python deps
```
pip install -r requirements.txt
```

### 2) Ollama

Install Ollama, pull your model, and ensure Ollama is running.
Set `OLLAMA_MODEL` if you use a different model.
```
ollama pull deepseek-r1:14b
```

### 3) Configure environment

Copy `.env.example` → `.env`, then set the local embedding folder path:
```
EMBED_MODEL_PATH=./bge-large-zh-v1.5
OLLAMA_MODEL=deepseek-r1:14b
```

### 4) Optional OCR (for scanned PDFs)

Install:
- Tesseract (and language data)
- Poppler (pdftoppm)
Then:
```
pip install -r requirements-ocr.txt
```
Run:
```
uvicorn app:app --reload --port 8000
```
Open:
- `http://127.0.0.1:8000/`

---

# 🤖 OpenAI API Version

This repo includes an **OpenAI-powered backend** that replaces the local LLM with an OpenAI model.

- ✅ File: `app_openai.py`
- 🔁 Same workflow: **Upload PDF → Retrieve chunks → Ask questions**
- 🔐 Uses `OPENAI_API_KEY` from environment variables

## 🚀 Quick Start

### 1) Install dependencies
```bash
pip install -U openai python-dotenv
```

### 2) Create a .env file (recommended)

Create a file named `.env` in the same folder as `app_openai.py`:
```env
OPENAI_API_KEY=sk-your_key_here
OPENAI_MODEL=gpt-4o-mini
```
Add `.env` to `.gitignore` to avoid leaking your key.

### 3) Run the server

```bash
uvicorn app_openai:app --reload --port 8000
```
## 🧪 Verify it’s working

When you send a chat request, you should see:

- ✅ 200 OK responses

- a model-generated answer

- no “local model” logs

## 🌍 Network Notes

Some networks/regions may block OpenAI requests.

- 🛜 Try a different network (e.g., mobile hotspot)

- 🌐 Try use VPN

- 🧩 If your traffic is routed through restricted locations, requests may fail

---

## ⚙️ Configuration

### Create a `.env` file (see `.env.example`):
| Variable           | Purpose                              | Example                                        |
| ------------------ | ------------------------------------ | ---------------------------------------------- |
| `EMBED_MODEL_PATH` | Path to local embedding model folder | `./bge-large-zh-v1.5`                          |
| `OLLAMA_MODEL`     | Ollama model name                    | `deepseek-r1:14b`                              |
| `CHUNK_SIZE`       | Text chunk size                      | `600`                                          |
| `CHUNK_OVERLAP`    | Chunk overlap                        | `100`                                          |
| `TOP_K`            | Retrieved chunks per query           | `5`                                            |
| `OCR_LANG`         | Tesseract language                   | `chi_tra`                                      |
| `OCR_DPI`          | PDF raster DPI                       | `200`                                          |
| `POPPLER_PATH`     | Poppler bin path (if needed)         | `./poppler-25.12.0/Library/bin`                |
| `TESSERACT_CMD`    | Path to `tesseract.exe` (Windows)    | `C:\Program Files\Tesseract-OCR\tesseract.exe` |
| `TESSDATA_PREFIX`  | Path to tessdata folder (Windows)    | `C:\Program Files\Tesseract-OCR\tessdata`      |

---

## 🔌 API Endpoints

| Method | Path      | What it does                                      |
| ------ | --------- | ------------------------------------------------- |
| `POST` | `/upload` | Upload PDF and build an index                     |
| `POST` | `/chat`   | Ask a question using retrieved context            |
| `POST` | `/clear`  | Clear chat + remove current uploaded/indexed data |
| `GET`  | `/`       | Frontend page                                     |

---

## 🧠 How it works (at a glance)

``` text
PDF → (PyMuPDF) text extraction
   → split into chunks
   → embed chunks (HF embeddings from local folder)
   → store vectors (Chroma on disk)
Query → retrieve Top-K chunks
     → send context + question to Ollama
     → return answer
```

---

## 🧾 OCR (Optional for scanned PDFs)

If your PDFs are image-only (scanned), OCR is needed.
1) Install OCR Python deps:
```
pip install -r requirements-ocr.txt
```

2) Install system tools:
- Tesseract (with Chinese language packs if needed)
- Poppler (for `pdftoppm` used by `pdf2image`)

3) Set env vars if required (Windows commonly needs these):
```
TESSERACT_CMD=C:\Program Files\Tesseract-OCR\tesseract.exe
TESSDATA_PREFIX=C:\Program Files\Tesseract-OCR\tessdata
POPPLER_PATH=.\poppler-25.12.0\Library\bin
```

---

# 🧼 GitHub Notes
These folders are typically large or generated at runtime:
- `bge-large-zh-v1.5/` (embedding model)
- `poppler-25.12.0/` (binaries)
- `uploads/` (user files)
- `chroma_dbs/` (vector store)
- `__pycache__/`

















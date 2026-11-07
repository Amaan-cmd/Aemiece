# Aemiece: AI-Powered Medical Assistant 🩺🤖

[![Python 3.10+](https://img.shields.io/badge/python-3.10+-blue.svg)](https://www.python.org/downloads/)
[![LangChain](https://img.shields.io/badge/LangChain-v0.2+-orange.svg)](https://www.langchain.com/)
[![FAISS](https://img.shields.io/badge/FAISS-Vector%20Store-green.svg)](https://github.com/facebookresearch/faiss)
[![LLaMA-2](https://img.shields.io/badge/LLaMA--2-7B--Chat-purple.svg)](https://ai.meta.com/llama/)
[![Chainlit](https://img.shields.io/badge/Chainlit-UI-red.svg)](https://docs.chainlit.io/)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)

**Aemiece** is an intelligent, privacy-preserving medical question-answering system powered by **Retrieval-Augmented Generation (RAG)**, **LangChain**, and quantized **LLaMA-2**. It grounds conversational responses in authoritative clinical literature, minimizing hallucination while providing traceable citations for every medical query.

---

## 📌 Key Highlights

- **Retrieval-Augmented Generation (RAG):** Contextualizes queries by retrieving semantically similar passages from comprehensive medical encyclopedias before generation.
- **Quantized Local Inference:** Runs quantized LLaMA-2 (`llama-2-7b-chat.ggmlv3.q8_0.bin`) on consumer hardware without requiring costly cloud API calls or transmitting sensitive healthcare queries to third parties.
- **Semantic Vector Search:** Employs Hugging Face's `sentence-transformers/all-MiniLM-L6-v2` dense embeddings combined with a **FAISS** index for millisecond-latency nearest-neighbor retrieval.
- **Source Attribution & Hallucination Safeguards:** Engineered system prompts enforce strict reliance on retrieved context; unverified or out-of-scope medical claims are explicitly flagged.
- **Interactive Chat Interface:** Built with **Chainlit** for asynchronous streaming responses and clean source document inspection.

---

## 🏗️ System Architecture

```mermaid
flowchart TD
    subgraph Offline Ingestion Pipeline
        A["Medical PDFs (data/)"] --> B["PyPDF Document Loader"]
        B --> C["Recursive Character Text Splitter<br/>(chunk_size=500, overlap=50)"]
        C --> D["HuggingFace Embeddings<br/>(all-MiniLM-L6-v2)"]
        D --> E["FAISS Vector Store<br/>(vectorstores/db_faiss)"]
    end

    subgraph Online RAG Inference Pipeline
        F["User Query"] --> G["Embed Query<br/>(MiniLM-L6-v2)"]
        E -.->|Similarity Search (Top k=2)| H["Retrieved Context Excerpts"]
        G -.->|Vector Query| E
        H --> I["Custom Prompt Template"]
        F --> I
        I --> J["Quantized LLaMA-2 7B<br/>(CTransformers)"]
        J --> K["Streamed Response + Sources"]
        K --> L["Chainlit UI"]
    end
```

---

## 🛠️ Tech Stack

| Component | Technology | Description |
| :--- | :--- | :--- |
| **LLM Backend** | LLaMA-2-7B-Chat (GGML Q8) | 8-bit quantized large language model for CPU/GPU inference |
| **LLM Inference** | CTransformers | Python bindings for GGML models |
| **Embeddings** | `all-MiniLM-L6-v2` | 384-dimensional dense sentence transformer |
| **Vector Store** | FAISS (Facebook AI Similarity Search) | High-throughput local vector similarity search |
| **Orchestration** | LangChain / LangChain Community | Chains, retrieval pipelines, prompt templates, and loaders |
| **Frontend UI** | Chainlit | Real-time asynchronous conversational web interface |

---

## 📂 Project Structure

```text
Aemiece/
├── data/                                 # Raw clinical reference documents (PDFs)
│   └── 71763-gale-encyclopedia-of-medicine...pdf
├── vectorstores/
│   └── db_faiss/                         # Pre-indexed FAISS vector store
│       ├── index.faiss                   # FAISS similarity index
│       └── index.pkl                     # Document metadata & chunks
├── .gitattributes                        # Git LF normalization rules
├── .gitignore                            # Comprehensive gitignore for Python, weights, & venvs
├── app.ipynb                             # Similarity benchmarking & text-cleaning notebook
├── chainlit.md                           # Branded UI welcome screen
├── ingest.py                             # Document loader, chunker, & FAISS indexer
├── model.py                              # Core RAG pipeline & Chainlit application server
├── README.md                             # Documentation & setup guide
└── requirements.txt                      # Production runtime dependencies
```

---

## 🚀 Quickstart Guide

### 1. Prerequisites
- Python 3.10 or 3.11 recommended
- Git
- 8GB+ RAM (16GB recommended for smooth local 7B Q8 model inference)

### 2. Clone the Repository
```bash
git clone https://github.com/Amaan-cmd/Aemiece.git
cd Aemiece
```

### 3. Create & Activate Virtual Environment
```bash
# On Linux / macOS
python3 -m venv venv
source venv/bin/activate

# On Windows (PowerShell)
python -m venv venv
.\venv\Scripts\Activate.ps1
```

### 4. Install Dependencies
```bash
pip install --upgrade pip
pip install -r requirements.txt
```

### 5. Download the LLaMA-2 Model Weights
Download the quantized LLaMA-2 7B Chat GGML model (`llama-2-7b-chat.ggmlv3.q8_0.bin`) from Hugging Face into the root directory of this repository:

- **Hugging Face Model Link:** [TheBloke/Llama-2-7B-Chat-GGML](https://huggingface.co/TheBloke/Llama-2-7B-Chat-GGML/blob/main/llama-2-7b-chat.ggmlv3.q8_0.bin)

```bash
# Direct download with curl (or download via browser and place in root)
curl -L -o llama-2-7b-chat.ggmlv3.q8_0.bin https://huggingface.co/TheBloke/Llama-2-7B-Chat-GGML/resolve/main/llama-2-7b-chat.ggmlv3.q8_0.bin
```

### 6. Build / Refresh Vector Database (Optional)
A pre-indexed FAISS database is already included in `vectorstores/db_faiss`. If you add new medical documents or change chunking parameters:
```bash
python ingest.py
```

### 7. Run the Application
Launch the interactive web UI:
```bash
chainlit run model.py -w
```
Open your browser at `http://localhost:8000` to interact with Aemiece.

---

## 💬 Example Queries

- *"What are the primary symptoms and diagnostic indicators of type 2 diabetes?"*
- *"Explain the pathophysiology and clinical management of asthma."*
- *"What adverse interactions can occur with chronic NSAID administration?"*

---

## ⚠️ Medical Disclaimer

> **Important:** Aemiece is an experimental AI application developed for educational and research demonstration purposes. It is **not** a diagnostic device, clinical decision support system, or medical service. The information generated should not be relied upon as a substitute for certified medical consultation, diagnosis, or treatment.

---

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

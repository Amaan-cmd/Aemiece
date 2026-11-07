# Welcome to Aemiece 🩺🤖

**Aemiece** is an AI-powered Medical Question-Answering Assistant built using **Retrieval-Augmented Generation (RAG)**, **LLaMA-2**, and **LangChain**.

---

### 💡 What Can I Ask?
You can inquire about symptoms, diseases, medical conditions, and treatments documented in clinical encyclopedias. For example:
- *"What causes sudden unexplained dizziness and what are the associated risk factors?"*
- *"Explain the pathophysiology and common treatments for asthma."*
- *"What are the complications of acute hypertension?"*

---

### ⚙️ How It Works
1. **Semantic Search:** Your query is embedded using `sentence-transformers/all-MiniLM-L6-v2` and matched against a high-dimensional FAISS index of verified medical literature.
2. **Context Augmentation:** Relevant clinical document excerpts are retrieved and passed to the prompt.
3. **Reasoning & Generation:** The quantized **LLaMA-2** model synthesizes a factual, context-grounded response and cites sources.

---

> ⚠️ **Disclaimer:** *Aemiece is an AI research and educational assistant. It is not intended to provide clinical diagnosis or substitute for professional medical consultation. Always consult a certified healthcare provider for medical advice.*

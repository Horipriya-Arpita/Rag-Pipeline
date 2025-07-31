# RAG Pipeline for Financial Data QA

This project is a Retrieval-Augmented Generation (RAG) system designed to answer queries about financial reports — specifically Meta’s Q1 2024 financial report. The project is structured in **three progressive steps**, each enhancing the system’s capabilities.

Each step includes a dedicated README file linked below, explaining the methods, tools used, challenges faced, and outcomes.

---

## 🧩 Project Steps Overview

### ✅ Step 1: Basic RAG Pipeline
> Objective: Build a simple RAG pipeline for factual question answering from a single PDF report.

- PDF text extraction
- Text cleaning and chunking
- Embedding generation using an open-source model
- Vector-based retrieval
- Open-source LLM-based generation

🔗 [Read Step 1 Details →](./step1_basic_rag/step1_basic_rag_README.md)  
🔗 [Read Step 1 Improved version →](./step1_basic_rag/README.md)

---

### ✅ Step 2: Structured Data Integration
> Objective: Incorporate structured data (e.g., tables) into the RAG system with hybrid retrieval.

- Table extraction and conversion to DataFrame/JSON
- Combined retrieval (vector + structured)
- Prompt engineering to combine unstructured and structured sources

🔗 [Read Step 2 Details →](./step2_structured_data/README.md)

---

### ✅ Step 3: Query Optimization & Advanced RAG
> Objective: Optimize the query pipeline and improve retrieval/generation performance.

- Query rewriting using LLM
- Reranking with cross-encoders or relevance models
- Chunk size experiments
- Evaluation metrics: Precision@k, Recall@k, MRR, BLEU/ROUGE
- Failure analysis and ablation study
- Improvement proposals

🔗 [Read Step 3 Details →](./step3_advanced_rag/README.md)

---

## 📁 Deliverables Structure
```
> project-root/  
│
├── step1_basic_rag/  
│ └── README.md  
│
├── step2_structured_data/  
│ └── README.md  
│
├── step3_advanced_rag/  
│ └── README.md  
│
├── sample_outputs/  
│ └── (outputs from each step for given test queries)  
│
├── src/  
│ └── (source code notebooks/scripts)  
│
└── README.md (this file)  
```
## Final Notes

This RAG pipeline showcases how hybrid retrieval, structured data integration, and query optimization can be combined to enable high-quality financial question answering from documents. Each stage improves upon the last — leading to better accuracy, usability, and explainability.

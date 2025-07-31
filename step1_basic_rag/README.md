
# 🧠 Step 1 (Improved): Table-Aware RAG Pipeline for Financial QA

This is an improved version of the **Retrieval-Augmented Generation (RAG)** pipeline for financial report QA. It addresses a key limitation of the basic version: **loss of table structure and semantics during chunking**.

By explicitly extracting tables and tagging them with page-level metadata, this version ensures better retrieval of numerical data — especially from financial statements such as balance sheets and cash flow reports.

---

## 📌 Objectives

- Retain both unstructured text and structured table data during extraction.
- Chunk each page’s content distinctly to preserve context.
- Improve retrieval performance for queries targeting specific table rows.
- Use Gemini Pro for natural language answer generation.

---

## ⚙️ Tools & Libraries Used

- `pdfplumber` – extract both text and tables from PDF
- `sentence-transformers` – for text embedding (`all-MiniLM-L6-v2`)
- `faiss` – vector similarity search
- `google-generativeai` – Gemini 2.5 Pro for QA

---

## 🧩 Pipeline Process

### 1. PDF Text + Table Extraction

Each page is scanned for both text blocks and tables.

- Text is labeled like: `Text from Page N:`
- Tables are extracted and converted into tab-separated format, labeled: `Table from Page N:`
- Header and rows are retained to preserve structure.

Example chunk:

```
Table from Page 5:
Assets	Liabilities	Equity
1000	500	500
```

---

### 2. Embedding and Indexing

Each chunk (text or table) is embedded using `all-MiniLM-L6-v2`, then indexed in FAISS.

```python
embeddings = embed_model.encode(chunks)
index = faiss.IndexFlatL2(dimension)
```

---

### 3. Retrieval

Given a user query, the top-k most similar chunks are retrieved using FAISS.

```python
indices = index.search(query_embedding, k)
```

These chunks now include well-formatted tables for improved context coverage.

---

### 4. Answer Generation (Gemini Pro)

Prompt template passed to Gemini:

```text
You're a helpful financial analyst assistant.

Based on the following extracted context from a financial report (text and tables), answer the question clearly:

Context:
{retrieved_chunks}

Question:
{query}
```

---

## 💬 Example Query

```python
ask_query("What was Net cash used in financing activities in 2024")
```

Other examples:

- `"What was Goodwill in March 31, 2024?"`
- `"What was Total liabilities in March 31, 2024?"`
- `"What was Meta’s revenue in Q1 2024?"`

---

## ✅ Output Samples

Expected outputs include:

- Top 3 most relevant chunks (including full tables)
- Gemini-generated answer

These are stored in:

```
sample_outputs/step1_v2/
```

---

## 🧪 Improvements Over Basic Version

| Feature                      | Basic Version       | Improved Version           |
|-----------------------------|---------------------|----------------------------|
| Table Support               | ❌ Incomplete chunks | ✅ Fully extracted & tagged |
| Page Metadata               | ❌ None              | ✅ Page numbers added       |
| Gemini Prompt Quality       | ❌ Basic prompt      | ✅ Task-specific prompt     |
| Retrieval Accuracy (Tables) | ⚠️ Partial           | ✅ Improved with structure  |

---

<!-- ## 📁 Folder Structure

```
step1_basic_rag_v2/
├── README.md                   ← This file
├── rag_step1_improved.ipynb    ← Main notebook/script
├── extracted_chunks.txt        ← Saved chunks for debug (optional)
├── sample_outputs/             ← Query result samples
``` -->

## 💡 Issue Solved 
### Now look at the following chunk: 
```
--- Chunk 1 ---
Text from Page 6:
META PLATFORMS, INC.
CONDENSED CONSOLIDATED BALANCE SHEETS
(In millions)
(Unaudited)
March 31, 2024 December 31, 2023
Assets
Current assets:
Cash and cash equivalents $ 32,307 $ 41,862
Marketable securities 25,813 23,541
Accounts receivable, net 13,430 16,169
Prepaid expenses and other current assets 3,780 3,793
Total current assets 75,330 85,365
Non-marketable equity securities 6,218 6,141
Property and equipment, net 98,908 96,587
Operating lease right-of-use assets 13,555 13,294
Goodwill 20,654 20,654
Other assets 8,179 7,582
Total assets $ 222,844 $ 229,623
Liabilities and stockholders' equity
Current liabilities:
Accounts payable $ 3,785 $ 4,849
Operating lease liabilities, current 1,676 1,623
Accrued expenses and other current liabilities 22,640 25,488
Total current liabilities 28,101 31,960
Operating lease liabilities, non-current 17,570 17,226
Long-term debt 18,387 18,385
Long-term income taxes 7,795 7,514
Other liabilities 1,462 1,370
Total liabilities 73,315 76,455
Commitments and contingencies
Stockholders' equity:
Common stock and additional paid-in capital 75,391 73,253
Accumulated other comprehensive loss (2,655) (2,155)
Retained earnings 76,793 82,070
Total stockholders' equity 149,529 153,168
Total liabilities and stockholders' equity $ 222,844 $ 229,623
6
```

### It captures the entire table from the PDF, not just a portion. This allows the LLM to retain the complete details of the table

## 📎 References

- [pdfplumber](https://github.com/jsvine/pdfplumber)
- [Sentence Transformers](https://www.sbert.net/)
- [FAISS](https://github.com/facebookresearch/faiss)
- [Gemini by Google](https://ai.google.dev/)

---

## 🚀 Status

✅ Stable and significantly more reliable for table-based queries.  
📈 Ready for integration with Step 2 (Structured Data + Hybrid Retrieval).


# Step 1: Basic RAG Pipeline – Financial QA from PDF

This step implements a basic **Retrieval-Augmented Generation (RAG)** system to answer factual questions from a financial report (e.g., Meta's Q1 2024). It involves extracting raw text from a PDF, converting it into vector embeddings, retrieving the most relevant text chunks, and using an LLM (Gemini Pro) to answer user queries based on those chunks.

---

## 📌 Objectives

- Extract and clean text from a single financial report PDF.
- Chunk the text into manageable, semantically meaningful sections.
- Generate embeddings using a sentence-transformer.
- Use FAISS for fast vector similarity search.
- Use Gemini Pro to generate answers based on the retrieved context.

---

## ⚙️ Tools & Libraries Used

- `pdfplumber` – PDF text extraction
- `langchain.text_splitter.RecursiveCharacterTextSplitter` – chunking
- `sentence-transformers` – sentence embeddings (model: `all-MiniLM-L6-v2`)
- `faiss` – similarity search index
- `google-generativeai` – Gemini 2.5 Pro LLM for QA

---

## 🧩 Pipeline Process

### 1. PDF Text Extraction

The system uses `pdfplumber` to extract text from each page of the uploaded financial report.

```python
with pdfplumber.open(file_path) as pdf:
    for page in pdf.pages:
        text += page.extract_text() + "\n"
```

---

### 2. Chunking

The extracted text is split using `RecursiveCharacterTextSplitter` with the following config:

- `chunk_size = 500`
- `chunk_overlap = 100`
- `separators = ["\n\n", "\n", ".", " "]`

**🔴 Issue Noted:**  
This method works well for regular paragraphs but fails with tabular data. Specifically, **tables that span multiple pages** are split across different chunks, breaking their semantic structure.

This causes the model to miss rows from the lower half of tables, resulting in failed or inaccurate answers when queried for such data.

### Look at the following chunk:
```
--- Chunk 1 ---
5
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
```
#### It's only capturing the first few rows of the full table and not retaining the entire content—specifically, the lower portion of the table is missing.


---

### 3. Embedding and Indexing

Each chunk is embedded using the `all-MiniLM-L6-v2` model and stored in a FAISS index.

```python
embeddings = embed_model.encode(chunks)
index = faiss.IndexFlatL2(embeddings.shape[1])
index.add(np.array(embeddings))
```

---

### 4. Retrieval

For a user query, the top `k` chunks with the most similar embeddings are retrieved.

```python
query_embedding = embed_model.encode([query])
distances, indices = index.search(np.array(query_embedding), k)
```

---

### 5. Answer Generation

The retrieved context chunks are combined into a single prompt and passed to Gemini Pro to generate an answer:

```text
Based on the following financial report context, answer the question:

Context:
{retrieved_chunks}

Question:
{query}
```

---

## 💬 Example Query

```python
ask_query("What was Meta’s revenue in Q1 2024?")
```

Or a harder table-based one:

```python
ask_query("In META PLATFORMS, INC. CONDENSED CONSOLIDATED BALANCE SHEETS, What was Operating lease right-of-use assets in March 31, 2024?")
```

---

## 🧪 Issues & Challenges

### ❌ Chunking Issue with Tables

- Large tables were **split mid-way**, leading to semantic breakage.
- As a result, **partial context retrieval** caused incorrect or empty answers.
- This was especially problematic when querying rows from the **bottom portion** of long tables.

```python
ask_query("what was Net cash used in financing activities in 2024")
```
```
💡 Gemini Answer:
Based on the provided context, the Net cash used in financing activities for the three months ended March 31, 2024, was **$(19,767) million**.
```

#### Fails to provide information from the lower half of tables  

```python
ask_query("In META PLATFORMS, INC. CONDENSED CONSOLIDATED BALANCE SHEETS, What was Goodwill in March 31, 2024?")
```
```
💡 Gemini Answer:
Sorry, There is no information about Goodwill in March 31, 2024.
```
---

## 🧠 Suggested Improvements

A better version was developed that addressed this limitation by improving:

- Table-preserving chunking (e.g., using bounding boxes or table detection)
- Embedding structured tables separately
- Attaching metadata (e.g., table title or page number) for better context mapping

📌 **Check out the improved version here:**  
[🔗 Step 1 Improved Version →](../step1_basic_rag/README.md) *(Update with actual path)*

---
<!-- 
## ✅ Output Samples

All output examples for queries (including failures and successes) are stored in:

```
sample_outputs/step1/
``` -->

---

<!-- ## 📁 Folder Structure

```
step1_basic_rag/
├── README.md                   ← This file
├── rag_step1.ipynb             ← Main notebook/script
├── extracted_chunks.txt        ← (optional) Saved text chunks
├── sample_outputs/             ← Sample query results
``` -->

---

## 📎 References

- [LangChain – Text Splitter](https://docs.langchain.com/docs/modules/data_connection/document_transformers/text_splitter)
- [Sentence Transformers](https://www.sbert.net/)
- [FAISS by Facebook](https://github.com/facebookresearch/faiss)
- [Gemini by Google](https://ai.google.dev/)
- [pdfplumber](https://github.com/jsvine/pdfplumber)

---

## 🚀 Status

✅ Completed, but limitations noted in handling financial tables  
🔜 Enhanced version addresses these issues

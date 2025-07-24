## Document Analyzer Agent

## Introduction

The **Document Analyzer Agent** is an intelligent, interactive application designed to extract meaningful metadata from research papers in PDF format using state-of-the-art language models. Built with **Streamlit** for the UI, **LangChain** for retrieval-augmented generation (RAG), and **OpenAI’s GPT models**, the tool automates the summarization and extraction of essential elements.

This project solves a real-world pain point in academia: **quickly understanding the core of a research paper without reading the entire document**. It’s especially useful for students, researchers, or professionals who need to review a large volume of academic material efficiently.

---

## Working

The system follows a **modular pipeline architecture**, consisting of the following stages:

### 1. Frontend Interface
- Built using **Streamlit** to provide a minimal, clean two-column layout.
- The **left column** allows the user to:
  - Securely enter an OpenAI API key.
  - Upload a PDF document (supports academic papers).
  - Trigger analysis via the “Generate Table” button.
- The **right column**:
  - Displays the uploaded PDF inline for visual reference.
  - Outputs structured results once processing is complete.

---

### 2. Document Processing Pipeline

#### a. PDF Upload & Conversion
- The uploaded PDF is read into memory and saved as a temporary file.
- `PyPDFLoader` (from LangChain) parses the file and extracts its text content into structured `Document` objects.

#### b. Text Chunking
- The extracted text is split into manageable chunks using `RecursiveCharacterTextSplitter`, with:
  - `chunk_size = 1000`
  - `chunk_overlap = 200`
- This helps preserve contextual continuity across splits and avoids data loss.

#### c. Embedding Generation
- Each chunk is converted into a high-dimensional vector using **OpenAI’s `text-embedding-ada-002` model**.
- This semantic embedding allows the model to understand the meaning behind the content, not just keywords.

#### d. Vector Store Creation
- The vector representations are stored in a **Chroma** vector database.
- Document deduplication is handled via hashing to avoid redundant chunks.
- The collection is indexed using a cleaned version of the original filename.

---

### 3. Query & Response Generation

#### a. Retrieval Augmented Generation (RAG)
- The app uses a **predefined query**:
  > “Give me the title, summary, publication date, and authors of the research paper.”
- This query is passed into a **retriever pipeline** that:
  - Pulls the most relevant document chunks from the vector store.
  - Formats them for the prompt.

#### b. LLM Inference
- The retrieved context is passed to **GPT-4o-mini** via `ChatOpenAI`.
- A custom prompt template ensures answers are:
  - Based only on retrieved context.
  - Structured into a Pydantic schema for:
    - `answer`
    - `sources`
    - `reasoning`

#### c. Tabulated Output
- The results are presented as a table with three sections for each field:
  - Final Answer
  - Context Source
  - Explanation / Reasoning

---

## Analysis

### Strengths
- **Highly Accurate**: Combines semantic retrieval with powerful language generation for precise extraction.
- **Modular Architecture**: Each stage (loading, splitting, embedding, querying) is cleanly separated, improving readability and maintainability.
- **Transparent Reasoning**: The model not only answers but justifies its reasoning and shows source context.
- **Fast Prototyping**: Built with Streamlit, this app can be deployed quickly on local or cloud platforms.

### Limitations
- Only supports **machine-readable PDFs** — OCR support is not implemented.
- Requires a **valid OpenAI API key** for operation.
- Best results on English-language documents with formal structure.
- Accuracy can vary on unstructured or heavily visual papers.

---

## Technologies Used

| Tool/Library        | Purpose                                 |
|---------------------|-----------------------------------------|
| **Streamlit**       | Frontend interface                      |
| **LangChain**       | Document loading, splitting, RAG chain |
| **Chroma DB**       | Vector database for semantic retrieval  |
| **OpenAI API**      | Embeddings & language generation        |
| **Pandas**          | Structuring and tabulating results      |

---

## Potential Improvements
- Add **OCR support** for scanned papers.
- Enable **custom query input** for flexible metadata extraction.
- Support **batch processing** of multiple PDFs.
- Provide **downloadable summaries** in PDF or CSV format.

---

## How to Run the App

> Ensure you have a valid OpenAI API key and Python environment with required dependencies.

```bash
# Step 1: Install requirements
pip install -r requirements.txt

# Step 2: Launch Streamlit app
streamlit run streamlit_app.py

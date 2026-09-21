# RESEARCH-PAPER-QUESTION-ANSWERING-SYSTEM

A Retrieval-Augmented Generation (RAG) application that lets users upload research-paper PDFs and ask questions about their content. The system retrieves relevant passages from the uploaded papers and uses Google Gemini to generate context-grounded answers with paper and page citations.

## Project Details

| Field | Details |
|---|---|
| Name | V.Gowtham |
| Registration Number | 231FA18056 |
| Section | IV AIML-E |
| Platform | Google Colab |
| Generation Model | Google Gemini |
| Embedding Model | `all-MiniLM-L6-v2` |

## Problem Statement

Research papers are long and information-dense. Finding a paper's objective, methodology, dataset, major findings, or limitations manually takes time. Directly asking a language model can produce answers that are not supported by the uploaded paper.

This project solves that problem with RAG. It retrieves relevant content from uploaded PDF papers before asking the language model to generate an answer. This makes the response more relevant, traceable, and easier to verify.

## Features

- Upload one or more research-paper PDFs.
- Extract text page by page.
- Split text into overlapping chunks.
- Create semantic embeddings for every chunk.
- Retrieve relevant chunks using cosine similarity.
- Generate answers using Google Gemini.
- Provide paper-name and page-number source citations.
- Evaluate retrieval with Precision@K and Recall@K.
- Evaluate answer grounding with a faithfulness test.

## System Architecture

```text
Research Paper PDFs
        |
        v
PDF Text Extraction using PyPDF
        |
        v
Chunking with Overlap
        |
        v
Sentence Transformer Embeddings
        |
        v
Semantic Retrieval using Cosine Similarity
        |
        v
Top K Relevant Chunks
        |
        v
Google Gemini
        |
        v
Context-Grounded Answer with Citations
```

## Technologies Used

| Technology | Purpose |
|---|---|
| Python | Core programming language |
| Google Colab | Cloud notebook execution environment |
| PyPDF | PDF text extraction |
| Sentence Transformers | Semantic embedding generation |
| `all-MiniLM-L6-v2` | Embedding model |
| NumPy | Vector operations and cosine similarity |
| Google Gemini API | Context-grounded answer generation |
| Google Gen AI SDK | Gemini API integration |

## How the System Works

1. The user uploads one or more PDF research papers in Google Colab.
2. The system extracts text from every page and stores the paper name and page number.
3. Extracted text is split into chunks of approximately 220 words.
4. Consecutive chunks share 40 words of overlap to preserve context at chunk boundaries.
5. Each chunk is converted into a normalized semantic embedding.
6. The user question is also converted into an embedding.
7. Cosine similarity finds the top five chunks most relevant to the question.
8. The question and retrieved chunks are sent to Google Gemini.
9. Gemini generates an answer only from the supplied context and includes citations.

## Installation in Google Colab

Run the following cell:

```python
!pip -q install pypdf sentence-transformers google-genai scikit-learn
```

## Gemini API Configuration

Create a Gemini API key from [Google AI Studio](https://aistudio.google.com/app/apikey). Do not hard-code the key in the notebook or commit it to GitHub.

```python
import getpass
from google import genai
from google.genai import types

GEMINI_API_KEY = getpass.getpass("Enter your Google Gemini API key: ")
client = genai.Client(api_key=GEMINI_API_KEY)

MODEL = "gemini-2.5-flash"
```

## Example Questions

```text
What is the objective of the paper?
What methodology was used?
Which datasets were used?
What are the major findings?
What limitations are mentioned by the authors?
Compare the methodology used in the uploaded papers.
```

## Example Output

```text
The paper evaluates its proposed method on the CIFAR-10 dataset [S1].
The authors report improved classification accuracy compared with the baseline [S2].

Sources:
[S1] research_paper.pdf, Page 4
[S2] research_paper.pdf, Page 7
```

## Evaluation Metrics

### Precision at K

Precision@K measures how many retrieved chunks in the top K are actually relevant.

```text
Precision@K = Relevant retrieved chunks / K
```

Example: If 4 of the top 5 retrieved chunks are relevant, Precision@5 is `4 / 5 = 0.80`.

### Recall at K

Recall@K measures how many of all relevant chunks are found in the top K results.

```text
Recall@K = Relevant retrieved chunks / Total relevant chunks
```

Example: If a paper has 5 relevant chunks and the system retrieves 4 of them, Recall@5 is `4 / 5 = 0.80`.

### Faithfulness

Faithfulness checks whether the generated answer is supported by the retrieved paper context.

```text
Faithfulness = Supported factual claims / Total factual claims
```

A high faithfulness score means the answer is grounded in the retrieved research-paper content instead of unsupported model-generated information.

## Why RAG?

Directly asking a language model may result in an answer based on its internal knowledge, which may be incomplete, outdated, or unrelated to the uploaded paper. This is known as hallucination.

RAG first retrieves relevant evidence from the actual uploaded PDF and then provides that evidence to Gemini. Therefore, answers are grounded in the paper and can include page citations.

## Important Design Choices

### Chunk Size: 220 Words

The chunk size must balance context and precision. Very small chunks may not contain enough meaning, while very large chunks can contain unrelated information. Around 220 words is a practical starting point for research papers.

### Chunk Overlap: 40 Words

Overlap preserves important sentences or ideas that occur at the boundary between two chunks.

### Retrieval Depth: Top 5

The system retrieves the five most relevant chunks. Too few chunks may miss important evidence, while too many chunks can introduce noise and increase API cost.

## Limitations

- Scanned PDFs and image-only PDFs may require OCR before text extraction.
- Complex tables, equations, and multi-column layouts may not extract perfectly.
- Retrieval quality depends on chunk size, overlap, embedding model, and paper text quality.
- Gemini generation requires a valid API key and internet connection.
- The system cannot reliably answer questions whose information is not present in the uploaded papers.

## Future Enhancements

- Add OCR support for scanned research papers.
- Add keyword search such as BM25 for hybrid retrieval.
- Store embeddings in a vector database such as FAISS or ChromaDB.
- Add a Streamlit web interface.
- Add chat history and session persistence.
- Support figures, tables, and image-based question answering.

## Viva Summary

> This project uses Retrieval-Augmented Generation to answer questions from uploaded research papers. It extracts PDF text, splits it into overlapping chunks, converts chunks into semantic embeddings, retrieves the top relevant chunks using cosine similarity, and uses Google Gemini to generate answers with source citations. Precision and Recall measure retrieval quality, while faithfulness checks whether the generated answer is supported by the retrieved context.

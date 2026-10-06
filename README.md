# Document Processing for RAG — Academic Rules PDF

A complete document processing pipeline that transforms a raw academic rules PDF into a clean, chunked, metadata-rich dataset ready for Retrieval-Augmented Generation (RAG).

![Python](https://img.shields.io/badge/Python-3.10%2B-blue)
![Jupyter](https://img.shields.io/badge/Jupyter-Notebook-orange)
![License](https://img.shields.io/badge/License-MIT-green)

---

## Overview

This project demonstrates **context preparation** for RAG systems — the step before embedding and retrieval. It shows that RAG quality depends heavily on how documents are extracted, cleaned, chunked, and annotated.

**Pipeline:**

PDF → Extract → Clean → Preserve Structure → Chunk → Add Metadata → Validate

---

## Why This Matters

Feeding raw PDF text to an LLM is bad practice. PDFs contain headers, footers, page numbers, broken lines, and tables that corrupt embeddings. This notebook shows a structured approach to removing noise while **preserving semantic boundaries** (BAB, Pasal, ayat).

The result is a dataset where each chunk:

- Contains a complete, self-contained rule
- Carries explicit `bab` and `pasal` metadata
- Is traceable back to the source document
- Is ready to be embedded in the next stage of the RAG pipeline

---

## What's Inside

| Section                     | What It Does                        |
| --------------------------- | ----------------------------------- |
| 1. Setup                    | Install dependencies and imports    |
| 2. Extract                  | Read the PDF with `pypdf`           |
| 3. Clean                    | Remove noise, preserve structure    |
| 4. Fixed-size chunking      | Character-based baseline (300, 800) |
| 5. Structure-aware chunking | Split by `Pasal N` boundary         |
| 6. Parse metadata           | Attach BAB, Pasal, chunk_id         |
| 7. Build context_text       | Merge metadata into embeddable text |
| 8. Validate                 | Flag short/long/missing chunks      |
| 9. Experiment comparison    | Compare 3 chunking strategies       |
| 10. Conclusion              | Pick the best strategy              |

---

## Results

### Chunking Strategy Comparison

|          Strategy           | Chunk Count | Rules Cut? |   Clear Metadata?    | Notes                                          |
| :-------------------------: | :---------: | :--------: | :------------------: | :--------------------------------------------- |
| A — Fixed 300 / overlap 50  |     81      |    Yes     |          No          | Narrow context, many rules cut mid-sentence    |
| B — Fixed 800 / overlap 150 |     32      | Sometimes  |          No          | Wider context, but some articles may be merged |
|   **C — Structure-aware**   |   **41**    |   **No**   | **Yes (BAB, Pasal)** | **Each rule stays intact and traceable**       |

### Final Dataset Quality

| Metric            | Value                 |
| ----------------- | --------------------- |
| Total chunks      | 41                    |
| Valid chunks      | 40 / 41 (97.6%)       |
| Avg chunk size    | 432 chars             |
| Median chunk size | 384 chars             |
| Largest chunk     | 2027 chars (Pasal 41) |

**Winner: Structure-aware chunking.** Each Pasal stays intact with clear BAB metadata, making every chunk self-contained and directly usable for embedding.

**Known limitation:** Pasal 41 is 2027 chars (above the 1500-char threshold). It's a single coherent rule but may exceed embedding model token limits. A future improvement is sub-chunking by ayat when a Pasal exceeds ~1500 chars.

---

## Getting Started

### 1. Clone the repository

```bash
git clone https://github.com/huzaifaakmal/llm-document-processing-rag.git
cd llm-document-processing-rag
```

### 2. Install dependencies

```bash
pip install -r requirements.txt
```

If you prefer to work interactively in Jupyter:

```python
%pip install pypdf pandas
```

### 3. Run the notebook

Open `notebook/document_processing.ipynb` and run all cells from top to bottom.

The notebook expects:
- Input PDF: `data/Aturan_Akademik.pdf`
- Output directory: `output/`

### 4. Generated outputs

The pipeline produces these files:

- `output/raw_text.txt` — raw extracted text from the PDF
- `output/clean_text.txt` — cleaned text with noise removed
- `output/chunks_aturan_akademik.csv` — final chunked dataset with metadata

---

## Project Structure

```text
.
├── data/
│   └── Aturan_Akademik.pdf
├── notebook/
│   └── document_processing.ipynb
├── output/
│   ├── raw_text.txt
│   ├── clean_text.txt
│   └── chunks_aturan_akademik.csv
├── .gitignore
├── README.md
├── requirements.txt
└── .ipynb_checkpoints/
```

---

## Pipeline Summary

This project follows a practical RAG preparation workflow:

1. Extract text from a PDF using `pypdf`
2. Clean noisy raw text while preserving document structure
3. Compare fixed-size chunking strategies against structure-aware chunking
4. Detect semantic boundaries such as `BAB` and `Pasal`
5. Attach metadata like `bab`, `pasal`, and `chunk_id`
6. Validate chunk quality before embedding

---

## Why This Project Is Useful

A common mistake in RAG pipelines is feeding raw PDF text directly into embeddings without cleaning or chunking. Academic regulations contain:

- page numbers and headers
- repeated footer text
- broken line wraps
- mixed formatting and table artifacts
- structural sections that should remain intact

This notebook demonstrates how to transform a messy PDF into a clean, traceable dataset that is far more suitable for retrieval and semantic search.

---

## Dataset Quality Outcome

The best-performing method in this project is the structure-aware strategy, which preserves each `Pasal` as an independent chunk while retaining its `BAB` and article metadata.

This makes the final dataset:

- easier to validate
- easier to trace back to source content
- better for retrieval and embedding
- more semantically coherent for downstream LLM use

---

## Requirements

- Python 3.10+
- `pypdf`
- `pandas`
- Jupyter Notebook / VS Code Notebook support

---

## License

This project is licensed under the MIT License.

---

## Notes

This project is intended as a document-processing foundation for a larger RAG system. The final output is not the LLM itself, but the cleaned, structured, retrieval-ready context dataset that supports it.

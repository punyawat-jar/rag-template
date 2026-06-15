# RAG Learning Repo

This repository contains two notebook-based Retrieval-Augmented Generation (RAG) workflows built with OpenAI and document inputs such as PDFs and images.

- `rag.ipynb` is a lightweight prototype that performs retrieval in memory.

## What This Repo Does

At a high level, the notebooks follow this pipeline:

```text
documents/images
    -> text extraction
    -> chunking
    -> embeddings
    -> retrieval
    -> answer generation
```

Depending on the notebook, retrieval happens either:

- in memory with cosine similarity, or
- in MongoDB Atlas with `$vectorSearch`

## Repository Structure

```text
.
├── rag.ipynb
├── input/
│   └── CV_Punyawat.pdf
└── .env
```

## Notebook Overview

### `rag.ipynb`

This notebook is the simpler prototype. It:

- loads environment variables from `.env`
- reads `.pdf` and `.png` files from `input/`
- extracts PDF text with `PyPDF2`
- extracts image text with the OpenAI Responses API
- splits text into overlapping chunks
- embeds chunks with OpenAI
- retrieves relevant chunks with cosine similarity
- sends retrieved context to an OpenAI chat model for the final answer

This flow does create a MongoDB connection, but the retrieval shown in the notebook is currently done from an in-memory Python list, not from MongoDB Vector Search.

## Requirements

- Python 3.10+
- Jupyter Notebook or JupyterLab
- an OpenAI API key
- a MongoDB Atlas cluster if you want to run the MongoDB tutorial notebook

## Installation

Create a virtual environment and install the dependencies used across the notebooks:

```bash
python -m venv .venv
source .venv/bin/activate
pip install -U openai "pymongo[srv]" python-dotenv pymupdf tiktoken pillow PyPDF2 numpy requests jupyter
```

## Environment Variables

The repo currently uses slightly different environment variable names across the two notebooks.

### Minimum for `rag.ipynb`

```env
OPENAI_API_KEY=your_openai_api_key
OPENAI_CORE_MODEL=gpt-4.1-mini
OPENAI_EMBEDDEDING_MODEL=text-embedding-3-small
MONGODB_URI=your_mongodb_uri
MONGODB_URI=mongodb+srv://USERNAME:PASSWORD@CLUSTER.mongodb.net/?retryWrites=true&w=majority

```

Notes:

- `OPENAI_EMBEDDEDING_MODEL` is spelled with an extra `ED` in `rag.ipynb` because that is how the notebook currently reads it.
- the embedding function in `rag.ipynb` currently hardcodes `text-embedding-3-small`
- `OPENROUTER_API_KEY` appears in the local `.env`, but it is not used by the notebooks shown in this repo

## How To Run

1. Put your input files in `input/`.
2. Open `rag.ipynb`.
3. Run the notebook from top to bottom.
4. Update the final question cell to query your document set.

## Current Repo Notes

- The sample document currently committed to the repo is `input/*.pdf`.
- The repo is notebook-first; there is no committed standalone `rag.py` file in the current project tree.
- `rag.ipynb` uses `input/`, while the tutorial notebook uses `data/`.
- If you want one consistent workflow, the first cleanup step would be to standardize folder names and environment variable names across both notebooks.

## Good Next Improvements

If you want to evolve this repo beyond notebook experimentation, the next useful steps would be:

- move shared logic into Python modules
- create a single consistent config format
- add a CLI or script entry point
- add retry/error handling for API calls
- cache embeddings to avoid repeated ingestion costs
- add metadata filters for multi-document collections


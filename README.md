# RAG with Salesforce Releases using OpenAI

A Retrieval Augmented Generation (RAG) pipeline for querying Salesforce release documentation using OpenAI's GPT models and embeddings.

## Overview

This project extracts structured information from Salesforce release notes PDFs and creates a searchable knowledge base using:
- **Text Extraction**: Direct PDF text extraction with `pdfplumber` (with image OCR fallback)
- **Structured Extraction**: GPT-4o-mini for extracting structured release notes
- **Embeddings**: OpenAI's `text-embedding-3-large` model for semantic search
- **Vector Database**: FAISS for efficient similarity search
- **RAG Pipeline**: GPT-powered Q&A over the extracted release notes

## Features

- **Intelligent PDF Processing**: Automatically detects if PDF has extractable text and uses fast text extraction, falling back to image OCR if needed
- **Parallel Processing**: Concurrent processing for both text extraction and embedding generation
- **Comprehensive Data Extraction**: Extracts all key fields including:
  - Feature names, categories, product areas
  - Change types (New Feature, Enhancement, Breaking Change, etc.)
  - API versions, timelines, affected components
  - Breaking changes and security notes
- **Semantic Search**: Query release notes using natural language
- **Structured Responses**: Get detailed, context-aware answers about Salesforce releases

## Requirements

- Python 3.8+
- OpenAI API key
- Jupyter Notebook

## Installation

1. Clone this repository:
```bash
git clone https://github.com/jackson-dev-606/RAG-with-Salesforce-Releases-using-OpenAI.git
cd RAG-with-Salesforce-Releases-using-OpenAI
```

2. Create a virtual environment:
```bash
python -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate
```

3. Install dependencies:
```bash
pip install pdfplumber pypdf openai faiss-cpu numpy python-dotenv pdf2image pillow
```

4. Set up your OpenAI API key:
   - Create a `.env` file in the project root
   - Add your OpenAI API key:
   ```
   OPENAI_API_KEY=your_api_key_here
   ```

## Usage

1. **Prepare your PDF**:
   - Place your Salesforce release notes PDF in the `pdfs/` folder
   - Update the `pdf_path` variable in Cell 1 if needed

2. **Run the Notebook**:
   - Open `RAG using SF Release Documents with OpenAI.ipynb` in Jupyter
   - Run all cells sequentially:
     - **Cell 0**: Setup and dependencies
     - **Cell 1**: Check PDF for extractable text
     - **Cell 2**: Extract text with structure
     - **Cell 3**: Process text chunks with GPT-4o-mini (parallel processing)
     - **Cell 4**: Generate embeddings (parallel processing)
     - **Cell 5**: Create FAISS index
     - **Cell 6**: Query function
     - **Cell 7**: RAG pipeline
     - **Cell 8**: Demo queries

3. **Query the Release Notes**:
   ```python
   # Example query
   query = "What are the new features in Sales Cloud?"
   response = rag_query(query, index, metadata, client, k=5)
   print(response)
   ```

## Project Structure

```
.
├── RAG using SF Release Documents with OpenAI.ipynb  # Main notebook
├── pdfs/                                             # PDF files (gitignored)
├── images/                                           # Generated images (gitignored)
├── filtered_release_notes.json                       # Extracted release notes
├── metadata_json.json                                # FAISS metadata
├── filtered_sf_release_notes_index.index             # FAISS vector index
├── .env                                              # API keys (gitignored)
├── .gitignore                                        # Git ignore rules
└── README.md                                         # This file
```

## How It Works

1. **PDF Processing**: 
   - Checks if PDF has extractable text
   - If yes: Uses fast text extraction with `pdfplumber`
   - If no: Falls back to image-based OCR with `pdf2image`

2. **Text Extraction**:
   - Extracts structured text chunks preserving headings and sections
   - Filters out headers, footers, and navigation pages

3. **Structured Extraction**:
   - Processes text chunks in parallel with GPT-4o-mini
   - Extracts structured release notes with all key fields
   - Filters out index pages and non-meaningful content

4. **Embedding Generation**:
   - Generates embeddings for all release notes using `text-embedding-3-large`
   - Processes embeddings in parallel for speed

5. **Vector Search**:
   - Creates FAISS index for efficient similarity search
   - Saves index and metadata for future use

6. **RAG Pipeline**:
   - Converts user query to embedding
   - Retrieves top-k similar release notes
   - Combines context and sends to GPT for answer generation

## Configuration

- **Parallel Workers**: Adjust `max_workers` parameter in:
  - Text processing: Cell 3 (default: 10)
  - Embedding generation: Cell 4 (default: 10)

- **Retrieval Count**: Change `k` parameter in query functions (default: 5)

- **GPT Model**: Modify `model` parameter in RAG pipeline (default: "gpt-4o-mini")

## Notes

- **API Keys**: Never commit your `.env` file. It's already in `.gitignore`
- **Large Files**: The FAISS index and JSON files may be large. Consider Git LFS if needed
- **Processing Time**: 
  - Text extraction: ~5-15 minutes for 800+ pages
  - Embedding generation: ~30-50 seconds for 1000 notes (with 10 workers)

## Example Queries

- "What are the new features in Sales Cloud?"
- "What changes were made to Flow Builder?"
- "Are there any API changes in this release?"
- "What security features were added?"
- "What improvements were made to reporting?"
- "Are there any breaking changes I should know about?"

## License

This project is for educational and personal use.

## Contributing

Feel free to submit issues or pull requests!

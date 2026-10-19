# Architecture Overview

This document describes the main components of the system and how data flows between them.

## High-Level Design

The system is a retrieval-augmented generation (RAG) pipeline. Instead of relying only on what a language model memorised during training, it looks up relevant passages from a document collection and supplies them as context when answering a question.

There are two phases:

1. **Indexing** – documents are loaded, split, embedded, and stored.
2. **Querying** – a question is embedded, similar chunks are retrieved, and the model generates an answer grounded in those chunks.

## Components

### Document Loader

The loader scans the `docs/` directory for Markdown files and reads their contents as plain text. It keeps the file path as metadata so that answers can cite their sources.

### Chunker

Long documents do not fit well into a single embedding, and retrieving an entire file is wasteful. The chunker splits text into segments of roughly 500 to 1000 characters, with a small overlap between neighbouring chunks. The overlap prevents a sentence that straddles a boundary from losing its meaning.

Splitting on Markdown headings first, then on paragraphs, tends to produce chunks that match natural topic boundaries.

### Embedding Model

Each chunk is converted into a fixed-length numeric vector. Chunks with similar meaning end up close together in vector space, which is what makes semantic search possible. The same model must be used for indexing and for queries, otherwise the vectors are not comparable.

### Vector Store

Vectors and their metadata are stored in a vector database running in Docker. It supports nearest-neighbour search using cosine similarity. Each record contains:

- the chunk text
- the embedding vector
- the source file name
- the chunk position within the file

### Retriever

At query time the question is embedded and the store returns the top-k most similar chunks. A typical value for k is between 3 and 8. Too few chunks may miss the answer; too many dilute the context and raise cost.

### Generator

The retrieved chunks are inserted into a prompt along with the question. The language model is instructed to answer using only the provided context and to say so when the context is insufficient.

## Data Flow

```
docs/*.md -> loader -> chunker -> embedder -> vector store
                                                  |
question -> embedder -> retriever <---------------+
                           |
                    prompt builder -> LLM -> answer
```

## Design Decisions

- **Local vector store**: keeps the project self-contained and avoids external accounts.
- **Overlapping chunks**: slightly increases storage but noticeably improves recall.
- **Source metadata**: enables citations and makes wrong answers easier to debug.

## Limitations

The pipeline does not currently re-rank results, handle tables or images, or detect when documents have been deleted. Stale entries stay in the store until the index is rebuilt.

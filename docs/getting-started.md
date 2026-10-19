# Getting Started with the Project

This guide walks through setting up a local development environment, running the services, and making your first change.

## Prerequisites

Before you begin, make sure the following tools are installed:

- **Python 3.12 or newer** – the project relies on modern typing features.
- **uv** – used for dependency management and virtual environments.
- **Docker and Docker Compose** – used to run supporting services such as the vector database.
- **Git** – for version control.

You can verify the installation with:

```bash
python --version
uv --version
docker --version
```

## Installing Dependencies

Clone the repository and sync the dependencies:

```bash
git clone <repository-url>
cd coding-agent
uv sync
```

`uv sync` creates a `.venv` directory and installs everything declared in `pyproject.toml`, pinned by `uv.lock`. To add a new dependency, use `uv add <package>` rather than editing the file by hand, so the lock file stays consistent.

## Starting Supporting Services

The project uses a vector database for retrieval. Start it with Docker Compose:

```bash
docker compose up -d
```

Check that the container is healthy:

```bash
docker compose ps
```

To stop the services, run `docker compose down`. Add the `-v` flag if you also want to delete the stored data.

## Indexing Documents

Documents placed in the `docs/` folder can be indexed so the agent can search them. Run:

```bash
uv run python index_docs.py
```

The script reads each Markdown file, splits it into chunks, generates embeddings, and stores them in the vector database. Re-run it whenever you add or edit documents.

## Asking Questions

Once indexing is complete, start the question-answering script:

```bash
uv run python rag.py
```

Type a question and the system retrieves the most relevant chunks, then passes them to the language model as context for the answer.

## Common Problems

| Symptom | Likely cause | Fix |
| --- | --- | --- |
| Connection refused on startup | Vector database not running | Run `docker compose up -d` |
| Empty answers | No documents indexed | Run the indexing script |
| Import errors | Wrong environment | Use `uv run` or activate `.venv` |

## Next Steps

Read the architecture overview to understand how the pieces fit together, then look at the troubleshooting guide for deeper debugging tips.

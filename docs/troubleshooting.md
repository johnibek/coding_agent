# Troubleshooting Guide

This guide covers the most common problems encountered when running the indexing and question-answering scripts, along with ways to diagnose them.

## Services Will Not Start

### Port already in use

If `docker compose up` fails with a message about a port being allocated, another process is using it. Find the process:

```bash
lsof -i :6333
```

Either stop that process or change the published port in `docker-compose.yml`. Remember to update any connection settings in the scripts to match.

### Container exits immediately

Check the logs:

```bash
docker compose logs --tail 50
```

Common causes are a corrupted data volume or insufficient disk space. If the data is disposable, remove the volume with `docker compose down -v` and start again.

## Indexing Problems

### No documents found

The indexer only reads files with a `.md` extension inside `docs/`. Confirm the files exist and the script is run from the project root, since relative paths depend on the working directory.

### Indexing is very slow

Embedding generation dominates the runtime. Ways to speed it up:

- Embed chunks in batches instead of one at a time.
- Skip files whose content has not changed since the last run.
- Use a smaller embedding model if quality is acceptable.

### Duplicate results

Running the indexer twice without clearing the store inserts every chunk again. Either delete the collection before re-indexing or use deterministic IDs, for example a hash of the file path and chunk position, so repeated runs overwrite rather than duplicate.

## Poor Answer Quality

### The answer ignores the documents

Check what was actually retrieved. Print the chunks and their similarity scores before they are sent to the model. If the relevant text is missing, the problem is in retrieval, not generation.

### Relevant text exists but is not retrieved

Possible fixes:

1. Increase the number of retrieved chunks.
2. Adjust chunk size; very small chunks lose context, very large ones blur meaning.
3. Rephrase the question to use vocabulary that appears in the documents.
4. Add a re-ranking step after the initial retrieval.

### The model invents details

Strengthen the prompt: instruct the model to answer only from the supplied context and to reply that it does not know when the context lacks the answer. Lowering the temperature also reduces speculation.

## Environment Issues

### Missing API key

The scripts read credentials from environment variables. If you see an authentication error, confirm the variable is set in the shell that launches the script:

```bash
echo $ANTHROPIC_API_KEY
```

Never commit keys to the repository. Store them in a local `.env` file that is listed in `.gitignore`.

### Module not found

This usually means the script ran outside the project environment. Use `uv run python script.py`, or activate the virtual environment first. If a package is genuinely missing, add it with `uv add`.

## Getting More Help

When reporting a problem, include the exact command you ran, the full error output, your Python version, and the output of `docker compose ps`. This makes the cause much quicker to identify.

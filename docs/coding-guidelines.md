# Coding Guidelines

These conventions keep the codebase consistent and easy to review. They are guidelines rather than strict rules; use judgement, and explain any deviation in the pull request.

## General Principles

- Prefer clear code over clever code. Someone unfamiliar with the module should be able to follow it.
- Keep functions small and focused on a single responsibility.
- Avoid premature abstraction. Wait until a pattern appears at least three times before extracting it.
- Fail loudly on programmer errors and handle expected failures gracefully.

## Python Style

### Formatting

Follow PEP 8 and let an automatic formatter handle layout. Lines should stay under 100 characters. Use double quotes for strings unless the string itself contains a double quote.

### Naming

| Item | Convention | Example |
| --- | --- | --- |
| Modules | snake_case | `index_docs.py` |
| Functions and variables | snake_case | `split_into_chunks` |
| Classes | PascalCase | `DocumentLoader` |
| Constants | UPPER_SNAKE_CASE | `DEFAULT_CHUNK_SIZE` |

### Type Hints

Annotate all public function signatures. Use built-in generics such as `list[str]` and `dict[str, int]`, and the `X | None` syntax for optional values.

```python
def split_text(text: str, size: int = 800, overlap: int = 100) -> list[str]:
    ...
```

### Error Handling

Catch specific exceptions rather than a bare `except`. Do not silence errors without logging them. When re-raising, preserve the original cause with `raise ... from err`.

## Project Structure

- Top-level scripts are thin entry points that parse arguments and call into reusable functions.
- Configuration such as model names, chunk sizes, and connection details belongs in one place rather than scattered across files.
- Documents for retrieval live in `docs/` and nothing else should be stored there.

## Testing

Write tests for behaviour that is easy to get wrong, such as chunking boundaries and metadata handling. Tests should be fast and must not require network access; replace the embedding model and language model with small fakes.

Name test files `test_<module>.py` and keep each test focused on a single behaviour.

## Dependencies

Add dependencies sparingly. Before adding a package, check whether the standard library already provides what you need. Always use `uv add` so that `pyproject.toml` and `uv.lock` are updated together.

## Git Workflow

- Create a branch for each change instead of committing directly to `main`.
- Write commit messages in the imperative mood, such as "Add chunk overlap option".
- Keep commits small and logically complete.
- Describe what changed and why in the pull request, and note how it was tested.

## Secrets and Security

Never commit credentials, tokens, or private data. Load secrets from environment variables, and keep local configuration files out of version control. Treat any downloaded file as untrusted input.

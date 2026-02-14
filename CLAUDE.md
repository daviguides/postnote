# CLAUDE.md

Postman-like HTTP client library for Jupyter notebooks with Polars DataFrame support.

## Project Overview

**postnote** - A lightweight HTTP client library optimized for Jupyter notebooks and Python projects. Make requests, preview curl commands, and display JSON or Polars DataFrames inline.

**Type**: Library (PyPI)
**Purpose**: Simplify HTTP API exploration and testing in Jupyter
**Status**: Production (v0.5.1)

### Key Features

- Simple API for GET, POST, PUT, PATCH, DELETE requests
- Inline curl command preview for copy/paste
- Pretty Markdown + JSON display in Jupyter cells
- Convert list responses directly to Polars DataFrames
- Support for JSON payloads, query params, multipart file uploads
- Easy auth helpers (Bearer, Basic)

---

## Project Structure

```
postnote/
├── README.md                           # Project documentation
├── LICENSE                             # MIT License
├── pyproject.toml                      # Project config
├── uv.lock                             # Dependency lock file
│
├── postnote/                           # Main package
│   ├── __init__.py                     # Package exports
│   ├── client.py                       # Request client class
│   ├── base.py                         # Base request logic
│   ├── settings.py                     # Request settings
│   ├── display.py                      # Jupyter display utilities
│   └── request.py                      # HTTP request handling
│
└── postnote.egg-info/                  # Package metadata
```

---

## Dependencies

**Core Dependencies**:
- **requests** (>=2.32.0) - HTTP client
- **polars** (>=1.0.0) - DataFrame manipulation
- **ipython** (>=8.36.0) - Jupyter support
- **ipykernel** (>=6.29.5) - Kernel support

**Development Dependencies**:
- **pytest** (>=8.3.5) - Testing
- **pytest-cov** (>=6.1.1) - Coverage reporting
- **ruff** (>=0.1.14) - Linting
- **pre-commit** (>=4.2.0) - Git hooks
- **twine** (>=6.1.0) - PyPI publishing
- **build** (>=1.2.2) - Build backend

**Dependency Manager**: uv
**Python Version**: 3.11+

---

## Common Workflows

### Setup / Installation

```bash
# Install from PyPI
pip install postnote

# Or install from local repo for development
uv sync
uv pip install -e .
```

### Using in Jupyter

```python
from postnote import Request, RequestSettings

# Configure
settings = RequestSettings(
    base_url="https://api.example.com",
    api_port=443,
    api_version="v1",
    resource_name="users",
    headers={
        "Content-Type": "application/json",
        "X-Client-Key": "your-key",
    },
)

# Create client
client = Request(settings)

# Make requests
resp = client.get(params={"page": 1, "limit": 10})
resp = client.post(payload={"name": "John", "email": "john@example.com"})
resp = client.put(params={"id": 1}, payload={"name": "Jane"})
```

### Converting to Polars DataFrame

```python
# If response is a list, convert to DataFrame
resp = client.get(to_polars=True)
```

### File Upload

```python
files = [("file", open("avatar.png", "rb"))]
resp = client.post(payload={"note": "upload"}, files=files, endpoint="upload")
```

### Authentication

```python
# Bearer token
client.set_bearer("your_token_here")

# Basic auth
client.set_basic("username", "password")
```

### Testing

```bash
# Run tests
uv run pytest

# With coverage
uv run pytest --cov=postnote
```

---

## Important Conventions

- **Settings-based configuration** - RequestSettings drives all request behavior
- **Request class** - primary interface for all operations
- **Base URL + resource pattern** - compose requests from base_url + api_version + resource_name + endpoint
- **Jupyter-friendly display** - uses IPython display utilities
- **Polars support** - list responses can auto-convert to DataFrames

---

## Special Considerations

- **Jupyter-first design** - optimized for interactive notebooks, works everywhere
- **API-agnostic** - works with any REST API (simple or complex)
- **No heavy dependencies** - minimal footprint, requests + polars only
- **Display utilities** - markdown + JSON rendering specific to Jupyter cells
- **Multipart uploads** - file handling via requests library

---

## Troubleshooting

### Imports fail in Jupyter

**Problem**: `from postnote import Request` fails
**Solution**: Ensure package is installed:
```bash
pip install postnote
# or from repo:
pip install -e .
```

### DataFrame conversion fails

**Problem**: `to_polars=True` doesn't create DataFrame
**Solution**: Verify response is a list/array structure:
```python
print(type(resp.json()))  # Should be list, not dict
```

### Auth headers not being sent

**Problem**: Bearer/Basic auth not appearing in requests
**Solution**: Call auth setter before requests:
```python
client.set_bearer("token")
resp = client.get()  # Now includes Authorization header
```

---

## Publishing

Update to PyPI:

```bash
# Build distribution
uv build

# Upload (requires PyPI token)
twine upload dist/*
```

---

## Notes for Claude Code

When working on this project:

1. **Library-first mindset** - this is a published PyPI library, maintain backward compatibility
2. **Jupyter integration** - test in actual Jupyter notebooks, not just pytest
3. **RequestSettings** - all configuration flows through this class
4. **Polars conversion** - understand response structure before to_polars conversion
5. **Simple API** - keep the public API small and focused
6. **Display helpers** - Markdown/JSON rendering is Jupyter-specific, test both environments
7. **Version bumps** - update pyproject.toml version and tag releases on git


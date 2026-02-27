# 🐳 Docker MCP Service

A production-ready, Dockerized **MCP (Model Context Protocol)** service with Claude Desktop integration, modular tool architecture, structured logging, and enterprise-grade CI/CD tooling.

---

## ✨ Features

- **Dockerized** — fully containerized, ready to build and run anywhere
- **Claude Desktop ready** — pre-configured JSON integration out of the box
- **MCP-compatible** — structured for seamless integration with MCP-compatible tools
- **Modular architecture** — clean separation between tools, services, and server entrypoint
- **UV-based** — fast dependency management with `uv` and `pyproject.toml`
- **Structured logging** — via `custom-python-logger`
- **Pre-commit hooks** — black, ruff, pylint enforced on every commit
- **CI/CD ready** — GitHub Actions for lint, tests, and docker build

---

## 🗂️ Project Structure

```
docker-mcp-service/
├── .config/                   # Linter & formatter configs
│   ├── black.toml
│   ├── ruff.toml
│   ├── pylintrc
│   └── pylintrc_tests
├── .github/
│   ├── workflows/
│   │   ├── lint.yml
│   │   ├── tests.yml
│   │   └── docker-build.yml
│   ├── ISSUE_TEMPLATE/
│   │   └── bug_report.yml
│   ├── CODEOWNERS
│   └── pull_request_template.md
├── docker_mcp_service/        # Main package
│   ├── __init__.py
│   ├── tools/                 # MCP tool definitions
│   │   ├── __init__.py
│   │   └── ping_tool.py
│   └── services/              # Business logic
│       ├── __init__.py
│       └── ping_service.py
├── tests/
│   ├── __init__.py
│   └── test_ping.py
├── .dockerignore
├── .env.example
├── .gitignore
├── .pre-commit-config.yaml
├── CHANGELOG.md
├── Dockerfile
├── LICENSE
├── MANIFEST.in
├── Taskfile.yml
├── docker-compose.yml
├── pyproject.toml
├── pytest.ini
└── server.py
```

---

## 🚀 Getting Started

### Local Development

```bash
# Install uv
pip install uv

# Install dependencies
uv sync --extra dev

# Copy and fill in env vars
cp .env.example .env

# Run the MCP server
uv run python server.py
```

### Docker

```bash
# Build the image
docker build -t docker-mcp-service .

# Run the container
docker run --rm --env-file .env -i docker-mcp-service
```

```bash
# Or with docker-compose
docker-compose up --build
```

---

## 🔌 Connecting to Claude Desktop

Add the following to your Claude Desktop configuration (`~/Library/Application Support/Claude/claude_desktop_config.json` on macOS):

```json
{
  "mcpServers": {
    "docker-mcp-service": {
      "command": "docker",
      "args": [
        "run", "--rm",
        "-e", "SERVICE_NAME=docker-mcp-service",
        "-i", "docker-mcp-service"
      ]
    }
  }
}
```

---

## ➕ Adding New Tools

1. Create a new service in `docker_mcp_service/services/`
2. Create a new tool file in `docker_mcp_service/tools/`
3. Register the tool in `server.py` via `register_*_tool(mcp)`

Example:

```python
# docker_mcp_service/services/hello_service.py
class HelloService:
    def greet(self, name: str) -> str:
        return f"Hello, {name}!"
```

```python
# docker_mcp_service/tools/hello_tool.py
def register_hello_tool(mcp: FastMCP) -> None:
    hello_service = HelloService()

    @mcp.tool()
    def greet(name: str) -> str:
        """Greet someone by name."""
        return hello_service.greet(name)
```

```python
# server.py — add one line
from docker_mcp_service.tools.hello_tool import register_hello_tool
register_hello_tool(mcp)
```

---

## 🧪 Running Tests

```bash
uv run pytest -v
```

---

## 🔧 Pre-commit Hooks

```bash
# Install pre-commit hooks
uv run pre-commit install

# Run all hooks manually
uv run pre-commit run --all-files
```

---

## 🤝 Contributing

Fork the repo, create a feature branch, and submit a pull request. Please follow the existing code style and include tests for any new tools.

---

## 📄 License

MIT License — see [LICENSE](LICENSE) for details.

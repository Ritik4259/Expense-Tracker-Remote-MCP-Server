# Expense Tracker Remote MCP Server

A lightweight remote [Model Context Protocol (MCP)](https://modelcontextprotocol.io/) server for recording, querying, and summarizing expenses. The server is written in Python, uses [FastMCP](https://gofastmcp.com/), and stores expenses in SQLite.

## Features

- Add expenses with a date, amount, category, subcategory, and note.
- List expenses for an inclusive date range.
- Summarize spending by category for a date range.
- Optionally filter summaries to a single category.
- Expose a JSON resource containing supported expense categories.
- Run locally or as an HTTP service for remote MCP clients and hosted deployments.

## Available MCP operations

### Tools

| Tool | Description | Parameters |
| --- | --- | --- |
| `add_expense` | Add an expense to the database. | `date`, `amount`, `category`, `subcategory` (optional), `note` (optional) |
| `list_expenses` | Return expenses within an inclusive date range. | `start_date`, `end_date` |
| `summarize` | Group expenses by category and calculate totals and counts. | `start_date`, `end_date`, `category` (optional) |

### Resource

- `expense:///categories` — JSON containing the available expense categories.

## Requirements

- Python 3.12 or newer
- [uv](https://docs.astral.sh/uv/) recommended for dependency and environment management

The project dependencies are declared in `pyproject.toml` and include `fastmcp` and `aiosqlite`.

## Run locally with uv

Clone the repository and install its dependencies:

```bash
git clone https://github.com/Ritik4259/Expense-Tracker-Remote-MCP-Server.git
cd Expense-Tracker-Remote-MCP-Server
uv sync
```

Start the HTTP MCP server:

```bash
uv run expense-tracker-remote-mcp
```

By default, the server listens on all interfaces at port `8000`:

```text
http://localhost:8000
```

To use another port, set the `PORT` environment variable:

```bash
PORT=9000 uv run expense-tracker-remote-mcp
```

The SQLite database is created automatically in the system temporary directory as `expenses.db`. The server initializes the database and creates the `expenses` table on startup.

## Connect from an MCP client

The deployed MCP server is available at:

```text
https://funny-lime-muskox.fastmcp.app/mcp
```

Configure an MCP-compatible client with the deployment URL:

```json
{
  "mcpServers": {
    "expense-tracker": {
      "url": "https://funny-lime-muskox.fastmcp.app/mcp"
    }
  }
}
```

For local development, use:

```text
http://localhost:8000/mcp
```

Use the endpoint format required by the MCP client or hosting platform you are using.

## Connect through FastMCP

This project is deployed using FastMCP's hosted HTTP endpoint. Add the following URL to your MCP client:

```text
https://funny-lime-muskox.fastmcp.app/mcp
```

After connecting, verify that the client can discover the `add_expense`, `list_expenses`, and `summarize` tools and read the `expense:///categories` resource.

> **Important:** The SQLite database is stored in the host's temporary directory. For a production deployment, confirm that the hosting environment provides persistent storage, or replace SQLite with a persistent database.

## Example tool inputs

Add an expense:

```json
{
  "date": "2026-09-19",
  "amount": 24.5,
  "category": "Food & Dining",
  "subcategory": "Lunch",
  "note": "Lunch with the team"
}
```

List expenses:

```json
{
  "start_date": "2026-09-01",
  "end_date": "2026-09-30"
}
```

Summarize one category:

```json
{
  "start_date": "2026-09-01",
  "end_date": "2026-09-30",
  "category": "Transportation"
}
```

## Project layout

```text
.
├── pyproject.toml
├── uv.lock
├── README.md
└── src/
    └── expense_tracker_remote_mcp/
        ├── __init__.py
        └── categories.json
```

## Development

Run the server directly from the package when needed:

```bash
uv run python -m expense_tracker_remote_mcp
```

Keep expense dates in `YYYY-MM-DD` format so SQLite's text-based date range queries sort correctly.

## License

No license has been declared for this repository yet. Add a license before distributing or deploying the project publicly.

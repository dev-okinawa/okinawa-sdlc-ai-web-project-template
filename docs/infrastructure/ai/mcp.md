# MCP Servers & Discovery Tools

The AI assistant combines an MCP server for code navigation, a docs/plugin marketplace tool for external library docs, and a CLI knowledge-graph tool for fast codebase orientation.

| Tool | Type | Use for | Required |
| ---- | ---- | ------- | -------- |
| **Serena** | MCP server | Symbol-level code navigation and edits (`get_symbols_overview` → `find_symbol`) | yes |
| **Context7** | Plugin (`context7@claude-plugins-official`) | Up-to-date docs for external libraries/frameworks | no |
| **Graphify** | CLI tool | Knowledge graph of code + docs (`graphify-out/`) — fast orientation before reading files | no, but recommended |

Priority per `.claude/rules/context.md`: **Graphify** to orient → **`docs/`** (source of truth, always `Read` the real file) → **Serena** for code symbols → **Grep/Glob** as last resort.

---

## Setup

### Serena

Copy `.claude/mcp.example.json` to your MCP client config and replace `/home/user/dev/project` with your actual project root:

```json
{
  "mcpServers": {
    "serena": {
      "command": "uvx",
      "args": ["--from", "git+https://github.com/oraios/serena", "serena", "start-mcp-server", "--project", "/home/user/dev/project"]
    }
  }
}
```

### Context7

Enable via `.claude/settings.json` → `enabledPlugins`. No project-local setup required.

### Graphify

Not an MCP server — a CLI tool run directly by the AI (see `.claude/rules/context.md` for the command reference: `graphify query`, `graphify explain`, `graphify path`, `graphify update`). Install and index generation are project-specific; the output lives in `graphify-out/` and should be regenerated (`graphify update .`) after code changes.

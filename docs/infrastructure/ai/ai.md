# AI

This folder documents the AI assistant configuration — the rules, commands, skills, agents, and MCP servers that shape how the AI works in this project.

## Pages

| Page | Contents |
|------|----------|
| [commands](commands.md) | Slash commands — the docs-first pipeline |
| [rules](rules.md) | Always-loaded standards |
| [skills](skills.md) | Context-triggered skills |
| [agents](agents.md) | Sub-agents used inside pipelines |
| [mcp](mcp.md) | MCP servers, setup and usage priority |

## Source files

All configuration lives under `.claude/` in the project root and in `CLAUDE.md`:

```
CLAUDE.md            ← entry point: loaded automatically by the AI on every session
.claude/
├── commands/         ← slash command definitions (business, specification, implement, feature, improvement)
├── rules/            ← always-loaded rules (workflow, context, documentation, architecture, coding-standards, environment, verification)
├── skills/           ← context-triggered skills (git, implementation, quality, review, security, test)
├── agents/           ← sub-agent definitions (7 agents)
├── settings.json     ← project AI settings (enabled plugins)
└── mcp.example.json  ← MCP server config template
```

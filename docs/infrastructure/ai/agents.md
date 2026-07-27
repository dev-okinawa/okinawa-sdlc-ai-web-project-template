# AI Sub-Agents

Sub-agents are specialized AI instances launched inside pipelines. Each agent has a focused role, its own tool set, and returns a structured report to the orchestrator.

Source files: `.claude/agents/`

---

## Agent Reference

| Agent | Role | Used in |
| ----- | ---- | ------- |
| [`code-explorer`](.claude/agents/code-explorer.md) | Explores one assigned angle of the codebase (structure, patterns, integration points, dependents, test coverage, spec verification). Returns an evidence-based report with `file:line` references and an essential-files list. | `/specification`, `/feature`, `/improvement` |
| [`code-architect`](.claude/agents/code-architect.md) | Designs one committed feature architecture under an assigned lens (minimal / clean / pragmatic). Returns a comparable blueprint — decision, component design, code map, dependency-ordered sub-tasks. | `/specification`, `/feature` |
| [`code-implementer`](.claude/agents/code-implementer.md) | Implements exactly one focused brief (a spec sub-task or a fix brief) in isolation — edits only files in scope, checks lints, returns a `DONE` / `DONE WITH DEVIATIONS` / `BLOCKED` report. | `/implement`, `/feature`, `/improvement` |
| [`code-reviewer`](.claude/agents/code-reviewer.md) | Reviews a code change under one assigned focus (simplicity, correctness, conventions, or security) against project rules. Filters by confidence so only high-priority real issues surface. | `/implement`, `/feature`, `/improvement` (via the `quality` skill) |
| [`test-author`](.claude/agents/test-author.md) | Authors automated tests for exactly one brief (a sub-task's new behavior, or a whole-feature gap-fill) following the project's test policy — writes test classes/fixtures, runs them targeted. | `/implement`, `/feature`, `/improvement` |
| [`docs-author`](.claude/agents/docs-author.md) | Works on `docs/` in one of three modes — mapping (inventory relevant pages), update (edit specs per an approved brief), or verification (compare as-built code against documented behavior). | `/specification`, `/implement`, `/feature`, `/improvement` |
| [`api-docs-author`](.claude/agents/api-docs-author.md) | Reconciles the public OpenAPI spec with implemented HTTP endpoints — compares controllers, DTOs, routes, responses. Returns a clean N/A when the feature touched no API. | `/implement`, `/feature`, `/improvement` (via the `quality` skill) |

---

## How agents are invoked

Agents are not called directly — they are launched by slash commands, or by the `implementation`/`quality` skills that those commands delegate to, as parallel or sequential sub-tasks.

To understand which agent handles what, see [commands.md](commands.md) and [skills.md](skills.md).

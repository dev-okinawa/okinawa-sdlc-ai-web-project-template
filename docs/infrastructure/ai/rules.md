# AI Rules

Rules are always loaded by the AI assistant to enforce project conventions — no trigger needed, unlike skills.

Source files: `.claude/rules/`

---

## Rules

| File | Title | Purpose |
| ---- | ----- | ------- |
| [`workflow.md`](.claude/rules/workflow.md) | Development Workflow | Docs-first task pipeline (BRD → Spec → Branch → Code → Verification → MR) and where each phase's source of truth lives. |
| [`context.md`](.claude/rules/context.md) | Context Gathering | Source priority for exploring the codebase — Graphify first, then `docs/`, then Serena for symbols, Grep/Glob as last resort. Stop criteria and anti-patterns. |
| [`documentation.md`](.claude/rules/documentation.md) | Documentation (`docs/`) | Canonical `docs/` layout and where each document type belongs (business, specification, architecture, infrastructure, testing, templates). |
| [`architecture.md`](.claude/rules/architecture.md) | Architecture | Modular structure, module anatomy (controller/service/repository/etc. layers), caching, database conventions. Placeholders — fill in with your stack. |
| [`coding-standards.md`](.claude/rules/coding-standards.md) | Coding Standards | Strict typing, dependency injection, naming conventions, code style. Placeholders — fill in with your stack's tools and versions. |
| [`environment.md`](.claude/rules/environment.md) | Execution Environment | Detect host vs. containerized execution before running any project command; route stack-specific commands accordingly. |
| [`verification.md`](.claude/rules/verification.md) | Verification | Checklist to run before committing — lint, migrations, targeted tests, docs-first check. Placeholders — fill in with your stack's commands. |

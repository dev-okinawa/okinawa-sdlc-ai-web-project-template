# AI Skills

Skills are specialized instruction sets that the AI loads automatically when a task matches their trigger — unlike rules, which are always loaded.

Source files: `.claude/skills/`

---

## Project Skills

| Skill | Trigger | Description |
| ----- | ------- | ------------ |
| [`git`](.claude/skills/git/SKILL.md) | "create branch", "commit", "save changes", "push", "create MR/PR", "merge request" | Full Git lifecycle — branch → commit → Merge/Pull Request. Naming rules, pre-commit checks, one-commit-at-the-end policy, MR/PR templates. |
| [`implementation`](.claude/skills/implementation/SKILL.md) | Driving the implementation phase of `/implement` or `/improvement` | Gated sub-task delivery loop — per sub-task: implement (`code-implementer`) → test (`test-author`) → accumulated suite green → mandatory user gate. |
| [`quality`](.claude/skills/quality/SKILL.md) | After the gated delivery loop completes | Post-implementation pipeline — parallel focused code review (4 `code-reviewer` agents) → final test verification → docs & API reconciliation. |
| [`review`](.claude/skills/review/SKILL.md) | "review code", "check my code", "review PR", pull requests | Code review checklist — types, architecture, database, security, API, performance, frontend, testing, docs-first alignment. Confidence-scored findings. |
| [`security`](.claude/skills/security/SKILL.md) | Implementing endpoints, handling sensitive data, modifying permissions | Security checklist — two-layer authorization (role + resource), authentication, injection prevention, XSS, serialization leaks, input validation, race conditions. |
| [`test`](.claude/skills/test/SKILL.md) | Writing or planning tests, or deciding whether a sub-task needs tests | Test-authoring policy — which functional/frontend test types to write per sub-task, when to skip (no testable surface), fixtures, how to run the suite. |

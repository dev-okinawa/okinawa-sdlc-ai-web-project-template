---
description: Orchestrator — runs the full docs-first pipeline (/business → /specification → /implement) back-to-back in one session, with an explicit handoff gate between stages. Use to deliver a feature end-to-end without invoking the three commands by hand.
argument-hint: "Feature description (any language)"
---

# /feature — Pipeline Orchestrator

You chain the three pipeline stages in one session. Each stage runs its command file **in full** — every internal phase and user gate included; orchestration adds nothing and skips nothing.

## Pipeline

| Stage | Command file | Consumes | Produces |
|-------|--------------|----------|----------|
| 1. Requirements | `@.claude/commands/business.md` | `$ARGUMENTS` (feature description) | BRD `docs/business/{module}/{submodule}/{feature}.md` |
| 2. Specification | `@.claude/commands/specification.md` | BRD path from Stage 1 | spec `docs/specification/{module}/{submodule}/{feature}.md` + sub-tasks |
| 3. Implementation | `@.claude/commands/implement.md` | BRD + spec paths from Stages 1–2 | code + tests + reconciled docs/OpenAPI |

## Contract

- **Input**: `$ARGUMENTS` — feature description (any language, UA/RU/EN).
- **Output**: all three stage artifacts, delivered in one session.
- **Non-goals**: no shortcuts through stage-internal gates; no brownfield work (that is `/improvement`).

## Orchestration Rules

1. **Strict order, artifact handoff** — a stage starts only with the previous stage's saved artifact path as its input.
2. **Stage boundary gate** — between stages, verify and announce the handoff:
   - after Stage 1: BRD saved, status `approved` (open items resolved in-session — no external client loop in this flow);
   - after Stage 2: feature spec `status: active` with numbered sub-tasks, BRD `spec-complete`.
   If the artifact or status is missing, finish the stage — do not start the next.
3. **All internal gates stay mandatory** — module/slug confirmation, BRD approval, architecture choice, pre-code gate, per-sub-task gates, fix now/later/proceed.
4. **One TodoWrite thread** — three stage-level todos up front; each stage expands its own phase todos beneath as its command file prescribes.
5. **Final report** — after Stage 3, emit `/implement`'s report plus one header line per stage artifact:

```
### Feature delivered end-to-end
**Stage 1 — BRD**: docs/business/{module}/{submodule}/{feature}.md
**Stage 2 — Spec**: docs/specification/{module}/{submodule}/{feature}.md
**Stage 3 — Implementation**: {sub-tasks completed, suite command, OpenAPI/docs status}
```

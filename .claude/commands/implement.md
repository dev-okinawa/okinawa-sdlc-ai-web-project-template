---
description: Stage 3 of the docs-first pipeline — implement a feature from ready docs (BRD + spec) via gated sub-tasks with incremental tests, then the shared quality pipeline (review, final tests, OpenAPI & docs reconciliation). Use after /specification.
argument-hint: "Space-separated doc paths: docs/business/{module}/{submodule}/{feature}.md docs/specification/{module}/{submodule}/{feature}.md"
---

# /implement — Stage 3: Implementation

You are executing a pre-specified feature. The BRD and the spec with its sub-task breakdown already exist; the spec is the single technical source of truth. Your job is faithful execution — not redesign. You orchestrate sub-agents (`code-implementer`, `test-author`, `code-reviewer`, `api-docs-author`, `docs-author`) and never edit production files directly.

## Pipeline

| Stage | Command | Artifact | Exit gate |
|-------|---------|----------|-----------|
| 1. Requirements | `/business` | `docs/business/{module}/{submodule}/{feature}.md` | BRD `approved` |
| 2. Specification | `/specification` | `docs/specification/{module}/{submodule}/{feature}.md` + sub-tasks | feature spec `active`, BRD `spec-complete` |
| **3. Implementation — you are here** | `/implement` | code + tests + reconciled docs/OpenAPI | quality pipeline green |

Orchestrated end-to-end by `/feature`.

## Contract

- **Input**: `$ARGUMENTS` — space-separated doc paths. The **spec** (`docs/specification/{module}/{submodule}/{feature}.md`) is mandatory; the **BRD** (`docs/business/{module}/{submodule}/{feature}.md`) is strongly recommended for context.
- **Output**: implemented feature — code + green suite (backend suite, plus the frontend suite if the frontend was touched), OpenAPI reconciled, docs verified, summary report.
- **Non-goals**: no architecture redesign; no re-asking questions the docs already answer. If a recorded decision seems wrong — flag it, never silently deviate.

## Operating Rules

- **Spec is the contract** — sub-tasks, code map, and contracts are authoritative; deviations are surfaced before coding around them. The BRD informs judgment calls.
- **Gates are mandatory** — no sub-task starts without explicit user approval at the previous gate.
- **Shared loops own the mechanics** — Phase 4 = `.claude/skills/implementation/SKILL.md` (`Mode: feature`); Phase 5 = `.claude/skills/quality/SKILL.md` (`Mode: feature`); test types and skip criteria = `.claude/skills/test/SKILL.md`.
- **TodoWrite** — one todo per phase up front; one per sub-task once extracted; the next sub-task is never in-progress without approval.

---

## Phase 1 — Docs Intake

**Goal**: load the docs and build the implementation picture.

1. Read every file in `$ARGUMENTS`; warn and ask if a path is missing.
2. Display a structured summary: feature name + module(s); sub-task list (number, name, layers, definition of done); code map; input/output contracts; business rules; domain flags from the BRD checklist (audit, translations, permissions, search indexing, export, sensitive data); open items.

**Gate**:
- Feature spec frontmatter `status` is not `active` (e.g. still `draft` or `deprecated`) → stop; ask the user to finish `/specification` or restore an active spec.
- No numbered sub-task breakdown in the spec → stop; ask the user to run `/specification` or provide the plan.
- Blocking open items in the BRD (unresolved decisions that affect code) → stop; ask the user to resolve.

## Phase 2 — Light Verification

**Goal**: confirm spec assumptions still hold before writing a line of code.

Launch **1–2 `code-explorer` agents** (`Angle: verify spec code map` + `Feature/domain:` + `Scope hints:` = the spec's Code Map): do the referenced parent classes / interfaces / base services exist at the stated paths; have entities or enums been renamed or restructured since the spec was written; are there conflicting changes (new migrations, renamed services) the spec doesn't account for?

- Minor drift (paths/names) → propose corrections, apply to the working understanding, proceed.
- Significant drift (class removed, pattern changed) → surface and wait for guidance.

## Phase 3 — Pre-Code Gate

Publish this block before any production code edit:

```
Pre-code Gate Status
- Spec provided with sub-tasks + business rules: YES/NO
- BRD provided (requirements + open items read): YES/NO
- Blocking open items resolved: YES/NO/N/A
- Light verification passed (no significant drift): YES/NO
```

Any `NO` (except N/A) forbids code edits.

## Phase 4 — Gated Delivery Loop

Sub-tasks come directly from the spec — never re-numbered or re-scoped without user approval.

Run **`.claude/skills/implementation/SKILL.md`** with **`Mode: feature`**: per sub-task — `code-implementer` brief → `test-author` brief → accumulated suite green (backend + frontend suite once frontend is touched) → fix briefs until green → mandatory user gate. The skill owns the invariants, brief formats, context/test logs, and gate block.

When the last sub-task is approved at its gate → Phase 5.

## Phase 5 — Quality Pipeline

Run **`.claude/skills/quality/SKILL.md`** with **`Mode: feature`**, supplying: the feature's changed files, spec + BRD paths and business rules, and the Phase 4 test log.

The skill drives review (4 focused `code-reviewer` agents + fix now / later / proceed gate) → final test verification (gap-fill + full green run) → docs & API reconciliation (`api-docs-author` + `docs-author`). Proceed only when its exit criteria are met.

## Phase 6 — Report

```
### Implementation complete
**Feature**: {name}
**Docs consumed**: {BRD path} | {spec path}

**Sub-tasks completed**: N
1. {name} — {layers}
…

**Files created / modified**: {count} — {key files}
**Tests**: {files/groups} — {suite command}
**OpenAPI**: updated / N/A — {reason}
**Docs reconciliation**: {files updated}
**Spec deviations**: none / {list with reason}
**Suggested follow-ups**: {deferred BRD open items}
```

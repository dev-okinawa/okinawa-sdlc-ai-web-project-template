# AI Slash Commands

These slash commands drive the docs-first pipeline. Each is a full standalone workflow with its own phases and user gates — `/feature` chains three of them; `/improvement` compresses the same pipeline for brownfield changes.

Source files: `.claude/commands/`

---

## Pipeline

| Stage | Command | Artifact | Exit gate |
| ----- | ------- | -------- | --------- |
| 1. Requirements | `/business` | `docs/business/{module}/{submodule}/{feature}.md` | BRD `approved` |
| 2. Specification | `/specification` | `docs/specification/{module}/{submodule}/{feature}.md` + numbered sub-tasks | spec approved, BRD `spec-complete` |
| 3. Implementation | `/implement` | code + tests + reconciled docs/OpenAPI | quality pipeline green |

### New feature — separate approval stages

```
/business  →  /specification  →  /implement
```

Use when the client must review and approve the BRD before design begins, or when explicit gates between analysis, design, and implementation are wanted.

### New feature — end-to-end in one session

```
/feature
```

Orchestrator that runs the three stages above back-to-back with the same internal phases and gates — nothing skipped, no separate client sign-off between stages.

### Change to existing functionality

```
/improvement
```

Brownfield track: analyzes the delta against current docs and code, checks backward compatibility, updates existing docs in place, then runs the same gated implementation and quality pipeline.

---

## Command Reference

| Command | Input | Output |
| ------- | ----- | ------ |
| `/business <description>` | Client description (any language) | `docs/business/{module}/{submodule}/{feature}.md`, status `draft` (Create Mode) |
| `/business <slug or path>` | Path/slug of an existing BRD | BRD updated in place with recorded answers + follow-ups (Update Mode) |
| `/specification <BRD path>` | Approved BRD | `docs/specification/{module}/{submodule}/{feature}.md` with numbered sub-tasks; BRD → `spec-complete` |
| `/implement <BRD path> <spec path>` | BRD + spec paths | Code + tests + OpenAPI update + docs reconciliation |
| `/feature <description>` | Client description (any language) | All of the above, in one session |
| `/improvement <description>` | Improvement description, optional module/path hint | Updated existing docs + code + tests + OpenAPI (if contract changed) |

---

## `/business` — Business Requirements Document (Dual Mode)

Turns an ambiguous client request into a BRD grounded in the actual codebase, then iterates it with client answers until every open question is closed.

**Create Mode** (`/business <client description>`) produces:
- Impact analysis across affected modules/submodules
- Numbered stakeholder questions with Why / Default / Status / Answer
- Functional and non-functional requirements, edge cases, risk matrix, acceptance criteria

**Update Mode** (`/business <slug or path>`) records answers (`Q1: answer`, `Q3: answer`, any subset), analyzes them for follow-ups, updates FR/NFR/risks/edge cases in place, rebuilds the Open Items checklist, and offers to advance status to `approved` once everything is closed.

Source: `.claude/commands/business.md`

---

## `/specification` — Technical Feature Spec from BRD

Transforms an approved BRD into the feature spec — the single technical source of truth (business rules, decisions, contracts) — under `docs/specification/{module}/{submodule}/{feature}.md`.

What it produces:
- Deep codebase exploration of affected modules/submodules (parallel `code-explorer` agents)
- Architecture comparison via parallel `code-architect` agents, with a user approval gate committing to one approach
- Feature spec following `docs/_templates/specification.md`
- Numbered, dependency-ordered sub-task breakdown with a testable definition of done per task

Source: `.claude/commands/specification.md`

---

## `/implement` — Feature Implementation from Docs

Executes a pre-specified feature. The spec (mandatory) and BRD (recommended) already exist; the spec is the technical contract — this command implements it faithfully, it does not redesign.

What it does:
- Reads the docs, extracts sub-tasks/contracts/business rules
- Light verification that spec assumptions still hold in the current codebase (`code-explorer`)
- Gated sub-task loop — see the `implementation` skill (`Mode: feature`)
- Post-implementation quality pipeline — see the `quality` skill (`Mode: feature`): review, final tests, OpenAPI sync, docs reconciliation

Source: `.claude/commands/implement.md`

---

## `/feature` — Full Pipeline Orchestrator

Runs `/business` → `/specification` → `/implement` in one session. Each stage runs its command file in full — every internal phase and gate included; the orchestrator only enforces the artifact handoff between stages (BRD `approved` → spec with sub-tasks and BRD `spec-complete` → implementation).

Source: `.claude/commands/feature.md`

---

## `/improvement` — Change to Existing Functionality

For changing, extending, or fixing **existing** functionality — not building something new. Compresses the three-stage pipeline into one command because the baseline (current code + docs) already exists.

Key differences from `/feature`:
- Reads and updates **existing** docs in place — never creates parallel spec files
- Analyzes the **delta** only, with a per-layer Delta Checklist and mandatory backward-compatibility assessment for every modified/removed behavior
- Runs the affected module's existing test suite as a green baseline before any code change
- Implementation and quality run in `Mode: delta` — see the `implementation` and `quality` skills

Source: `.claude/commands/improvement.md`

---
description: Brownfield track of the docs-first pipeline — change existing functionality via delta analysis against current docs and code, backward compatibility assessment, in-place doc updates, gated implementation with regression baseline, and the shared quality pipeline. Use for changes to existing features, not new ones.
argument-hint: "Improvement description (any language). Optionally add a path hint: 'Add column docs/specification/commerce/orders/'"
---

# /improvement — Brownfield: Delta on an Existing Feature

You are changing **existing functionality**. Unlike `/feature` or `/implement`, you do not build from scratch: the current code and docs are the baseline, you analyze only the delta, update the existing documentation in place, and implement with full backward compatibility awareness.

## Pipeline

Compresses Stages 1–3 (`/business` → `/specification` → `/implement`) into one command, because the feature already exists and its docs are the baseline: analysis (Phases 1–3) → plan + in-place doc updates (Phases 4–5) → implementation + quality (Phases 6–7).

## Contract

- **Input**: `$ARGUMENTS` — improvement description (any language), optionally with a module/path hint to narrow the search.
- **Output**: delta implemented — existing docs updated in place, code + green full module suite (backend suite, plus the frontend suite if the frontend was touched), backward compatibility confirmed, OpenAPI reconciled, summary report.
- **Non-goals**: no new BRD files; no new spec files from scratch (extend existing ones); no redesign of what isn't changing.

## Operating Rules

- **Delta mindset** — analyze, question, plan, and document only what changes; the existing behavior is the baseline.
- **Backward compatibility is mandatory** — every modified or removed behavior gets an explicit "who depends on it / what breaks" assessment.
- **In-place doc updates** — existing spec files are edited, never paralleled by new documents.
- **Completeness** — every entity/data change is checked against all consumer layers (listings, analytics/reporting, export, API, search indexing, translations, UI).
- **Shared loops own the mechanics** — Phase 6 = `.claude/skills/implementation/SKILL.md` (`Mode: delta`, green baseline first); Phase 7 = `.claude/skills/quality/SKILL.md` (`Mode: delta`); test types and skip criteria = `.claude/skills/test/SKILL.md`.
- **TodoWrite** — one todo per phase up front; one per sub-task once extracted in Phase 4; the next sub-task is never in-progress without approval.

---

## Phase 1 — Current State Snapshot

**Goal**: capture the documented and implemented baseline before analyzing the change.

1. Parse `$ARGUMENTS`: feature domain, keywords, module/path hints.
2. Find and read the relevant docs: `docs/specification/{module}/{submodule}/{submodule}.md` (overview), `docs/specification/{module}/{submodule}/{feature}.md` (specs), `docs/business/` (prior BRD if any).
3. Display the snapshot: existing entities and fields, documented services and methods, API endpoints and contracts, business rules, documented edge cases.

**Gate**: if no relevant docs exist, warn — the change may need `/specification` (new spec) instead of `/improvement` — and ask before proceeding.

## Phase 2 — Delta Analysis

**Goal**: identify exactly what changes and what might break.

Launch **2–3 `code-explorer` agents in parallel** (`Angle:` + `Feature/domain:` + `Scope hints:`):

- **Angle — current implementation**: the actual code — entity fields, service methods, controller actions, DTOs, repository queries, frontend surface. Note code-vs-docs drift.
- **Angle — consumers & dependents**: everything depending on what changes — listing/table definitions, analytics/reporting configs, export handlers, async subscribers, search index mappings, API clients, serialization groups, fixtures, frontend consumers.
- **Angle — test coverage**: existing backend test classes and frontend specs, fixtures, data providers covering the affected behavior — the test baseline.

Produce the **Delta Checklist** — per layer: `unchanged` / `added` / `modified` / `removed` / `unknown (→ question)`:

| Layer | Status | Backward compatible | Notes |
|-------|--------|--------------------:|-------|
| Entity fields | | YES / NO / N/A | |
| Repository queries | | | |
| Service logic | | | |
| API endpoints | | YES / NO | breaking → migration plan |
| API response shape | | YES / NO | breaking → versioning decision |
| DTO fields | | | |
| Serialization (exposed fields) | | | |
| Listing / table columns / filters | | | |
| Analytics / reporting fields | | | |
| Export columns / handlers | | | |
| Async messages / handlers | | | |
| Search index mappings *(if applicable)* | | YES / NO | NO → re-index needed |
| Permissions | | | |
| Audit trail | | | |
| Translations / localization | | | |
| Sensitive data handling | | | |
| Server-rendered views / templates | | | |
| UI components | | | |
| Page controllers | | | |
| Frontend tests | | YES / NO | NO → which specs break |
| Existing tests | | YES / NO | NO → which tests break |
| Existing fixtures | | YES / NO | NO → which need update |
| Schema / migrations | | | |

For every `modified` / `removed` row: who depends on it (specific classes/files), self-contained or cascading, and — if breaking — the migration/deprecation strategy.

Finish with the **regression risk list**: specific test classes and methods likely to break.

## Phase 3 — Clarifying Questions

**Goal**: close delta ambiguities and completeness gaps. **Do not skip.** Questions are scoped to the delta — never re-ask about unchanged behavior.

Always run the completeness checklist for data/entity changes:

- Field **added** → does it appear in listings? analytics/reporting? export output? API response? search index? UI? Needs a translation string?
- Field **removed/renamed** → what breaks, who must migrate, which UI components reference it?
- **Service signature changes** → which callers are affected?
- **UI changes** → which view/component files, which frontend specs to add or update?
- **Business rules change** → does the spec's Business Rules section need updating; any conflict with other documented rules?
- **Permissions change** → new permission values; which roles get what?

**Gate**: present all questions at once and wait for answers. If there are genuinely none, state that explicitly and proceed.

## Phase 4 — Plan: In-place Doc Updates + Sub-tasks

**Goal**: update existing documentation, then plan the delta as numbered sub-tasks.

**4a. Doc updates** — edit existing files; a new doc file is allowed only for a genuinely new, never-documented feature slice (then also add it to the submodule's Features table). Per affected `docs/specification/{module}/{submodule}/{feature}.md`: code map rows, contracts, business rules, edge cases, decisions, implementation notes.

**4b. Sub-task breakdown** — delta only, strict dependency order (migration → entity → repository → service → controller/API → UI → tests):

```
## Improvement Sub-tasks

### Sub-task 1: {Name}
**Goal**: {one sentence}
**Layers**: {…}
**Files to modify**:
- `{module path}/{Module}/…` — {what}
**Existing tests to update**: `{TestClass}::{method}` — {reason}   ← critical, never omit
**Definition of done**: {testable condition}
```

**Gate**:
> **Approve doc updates and sub-tasks?** `approve` → pre-code gate | `adjust: [changes]` → revise and re-present.

## Phase 5 — Pre-Code Gate

Publish this block before any production code edit:

```
Improvement Pre-code Gate
- Current state docs read and understood: YES/NO
- Delta analysis complete (all layers checked): YES/NO
- Backward compatibility assessed: YES/NO/no breaking changes
- Completeness check passed (listings/analytics/export/API/search/translations/UI): YES/NO
- Existing docs updated in place: YES/NO
- Sub-tasks approved by user: YES/NO
```

Any `NO` forbids code edits.

## Phase 6 — Gated Delivery Loop (delta)

Run **`.claude/skills/implementation/SKILL.md`** with **`Mode: delta`**: it baselines the affected module's suite (must be green) before sub-task 1, then per sub-task — `code-implementer` brief → `test-author` brief (carrying the sub-task's **"existing tests to update"** and fixture updates for schema/data changes) → accumulated suite green (baseline + all delta tests, frontend suite included when frontend is touched) → fix briefs until green → mandatory user gate.

When the last sub-task is approved → Phase 7.

## Phase 7 — Quality Pipeline (delta)

Run **`.claude/skills/quality/SKILL.md`** with **`Mode: delta`**, supplying: the delta's changed files, affected spec paths, the Phase 2 regression-risk list and backward-compatibility scenarios, the docs updated in Phase 4, and the Phase 6 test log.

The skill drives review (4 focused `code-reviewer` agents + fix now / later / proceed gate) → final test verification (gap-fill + full module suite green) → docs & API reconciliation (`api-docs-author` + `docs-author`, scoped to the Phase 4 docs). Proceed only when its exit criteria are met.

## Phase 8 — Report

```
### Improvement complete
**Task**: {original description}
**Module**: {affected module(s)}

**Delta**: layers changed {list} | breaking changes: none / {list + migration notes} | backward compatibility: confirmed / {exceptions}

**Sub-tasks completed**: N
1. {name} — {layers}
…

**Files modified**: {count} (no new files / N new: {list})
**Tests**: existing updated {list + reason} | new {list} | command: {suite command}
**OpenAPI**: updated / N/A
**Docs updated in place**: {list}
**Completeness**: Listings ✓/✗ | Analytics ✓/✗ | Export ✓/✗ | API ✓/✗ | Search ✓/✗ | Translations ✓/✗ | UI ✓/✗
**Suggested follow-ups**: {deferred items}
```

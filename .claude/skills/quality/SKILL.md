---
name: quality
description: >-
  Post-implementation quality pipeline shared by /implement and /improvement: parallel focused code review (4 code-reviewer agents) → final test verification (gap-fill + full green run) → docs & API reconciliation (api-docs-author + docs-author). Covers the feature vs delta modes, fix-brief loops, and the fix now / later / proceed gate. Use after the gated delivery loop completes.
---

# Post-implementation Quality Pipeline

Runs after the **last sub-task** of the gated delivery loop (`.claude/skills/implementation/SKILL.md`) is approved at its gate. Three stages, strictly in order: **review → final tests → reconciliation**. The orchestrator (you) composes briefs and consolidates reports — fixes go through `code-implementer` fix briefs, never direct edits.

---

## Modes

| Mode        | Used by        | Review emphasis                                          | Test emphasis                                                   | Reconciliation emphasis                                    |
|-------------|----------------|-----------------------------------------------------------|------------------------------------------------------------------|-------------------------------------------------------------|
| **feature** | `/implement`   | BRD edge cases + spec business rules                      | whole-feature coverage, cross-sub-task integration               | feature spec vs code; OpenAPI for new endpoints             |
| **delta**   | `/improvement` | backward compatibility + the regression-risk list         | delta coverage + backward-compat scenarios, full module suite    | docs updated during planning; OpenAPI only if contract changed |

The command invoking this skill supplies: **changed files**, **spec / BRD / doc paths**, the **test log** from the delivery loop, and (delta mode) the **regression-risk list**.

---

## Stage 1: Quality Review

Launch **4 `code-reviewer` agents in parallel**, one per `Focus:`. Each brief carries `Scope:` = the changed files and `Context:` = spec + BRD paths, business rules (delta mode: + the regression-risk list):

- `Focus: simplicity / DRY / elegance` — delta mode: the delta only, not the entire module
- `Focus: bugs / correctness / edge cases` — feature mode: cross-check against BRD edge cases and spec business rules; delta mode: cross-check against the regression-risk list (focus phrased as `bugs / correctness / backward compatibility`)
- `Focus: conventions / architecture patterns` — architecture, coding standards, and frontend conventions match the surrounding code
- `Focus: security` — two-layer permissions on every new/changed action, injection prevention, serialization data leaks, PII exposure for unverified users, audit trail coverage, input validation

Consolidate findings and present to the user. Ask: **fix now / later / proceed**.

If "fix now" — compose a fix brief from reviewer findings and launch `code-implementer`:

```
Fix: <what is broken>
Evidence: <reviewer finding with file:line>
Scope: <files allowed to change>
```

Re-run reviewers on affected files if needed. If a fix touched code, re-run the accumulated suite to confirm it stays green before Stage 2.

---

## Stage 2: Final Test Verification

**Goal**: confirm coverage and a final green run. Most tests already exist — they were written and passed per sub-task in the delivery loop. This stage closes **remaining gaps** and runs everything one last time.

1. **Gap check** — review the test log against the spec (feature mode) or the delta + backward-compatibility scenarios (delta mode): is every business rule, contract, and edge case reachable through a **testable surface** (API / command / datatable / message handler / filter / UI component / page controller) covered? Foundational sub-tasks whose tests were skipped must be exercised indirectly by the surfaces that consume them — confirm this. If real gaps remain, launch **1–2 `test-author` agents** (`Mode: gap-fill`) to fill them.
2. **Final run** — execute the full suite end-to-end: feature mode — all sub-tasks' tests together; delta mode — the **full module suite** (baseline + delta). Include the **frontend suite** if the work touched the frontend. If any test reveals a bug, compose a fix brief and launch `code-implementer`; re-run until green before Stage 3.

---

## Stage 3: Docs & API Reconciliation

**Goal**: verify implementation matches the docs; update docs only for approved deviations. Docs are the source of truth — a code-vs-doc divergence is flagged, not silently absorbed.

Launch **in parallel**:

- **`api-docs-author`** with `Mode: verify`, `API-relevant: yes|no — <reason>`, `Scope:` = paths/modules touched and pointers to final controllers and DTOs. It reconciles the project's OpenAPI spec with the implemented endpoints against `docs/architecture/cross-cutting/api.md`, or returns N/A when no HTTP endpoint contract changed.
- **`docs-author`** with `Mode: verification`, `Scope:` = the feature's submodule docs (feature mode) or the docs updated during planning (delta mode), `Brief:` = compare final code + tests to those docs and classify each mismatch. It updates docs only where a deviation was **explicitly approved at a delivery-loop gate** — never to match arbitrary code behavior.

Constraints:
- **Do not modify BRD files** (`docs/business/`) — they are frozen artifacts.
- **Do not create new doc files** unless the command's planning phase determined a new file was warranted.

---

## Exit Criteria

The pipeline is complete when:
- reviewer findings are fixed or explicitly deferred by the user,
- the full suite is green (backend + frontend where applicable),
- OpenAPI is reconciled or N/A with reason,
- docs reconciliation is done and all unapproved deviations are surfaced.

Return to the invoking command's Summary phase carrying: review outcome, final test command + result, OpenAPI status, docs updated, and the deviation list.

---
name: implementation
description: >-
  Gated sub-task delivery loop shared by /implement and /improvement: per sub-task run implement (code-implementer) → test (test-author) → accumulated suite green (backend + frontend suite when frontend is touched) → mandatory user gate. Covers brief composition, context/test logs, fix-brief retries, and the feature vs delta modes. Use when driving the implementation phase of a feature or improvement.
---

# Gated Sub-task Delivery Loop

The unit of delivery is **one sub-task**. Each sub-task runs a full **implement → test → run accumulated suite → green → gate** cycle before the next one starts. Implementation is delegated to the `code-implementer` sub-agent and tests to `test-author`, so the orchestrator (you) stays lean: your job is brief composition, report review, running the suite, and gating — **not editing files directly**.

---

## Modes

| Mode        | Used by        | Baseline before sub-task 1                                            | Test brief emphasis                               |
|-------------|----------------|-----------------------------------------------------------------------|---------------------------------------------------|
| **feature** | `/implement`   | none — the test log starts empty                                      | cover each sub-task's **new** behavior            |
| **delta**   | `/improvement` | run the existing module suite; it must be **green** before any change | new behavior **+** the "existing tests to update" |

**Delta baseline**: before sub-task 1, run the affected module's existing suite (`{test command} --filter {ModuleName}`, plus `{frontend test command} {pattern}` if the module has frontend specs). If it is already red, stop and fix before touching anything — this green baseline is the first entry in the test log.

---

## Invariants (CRITICAL)

- Implement **only one sub-task at a time**, in the spec's dependency order. Do not re-number or re-scope sub-tasks without user approval.
- A sub-task is **not done** until its tests are written (or explicitly skipped) and the **accumulated suite is green**. No sub-task reaches its gate red.
- **Never** begin sub-task **k + 1** without **explicit user approval** at the gate for sub-task **k**.
- Maintain two logs across the loop:
  - **context log** — after each sub-task, the files (production + tests) changed and what changed in them; feeds the next sub-task's brief.
  - **test log** — every test written/updated so far (+ the delta baseline), so the accumulated run for sub-task **k** covers 1…k (and the baseline in delta mode). Skipped sub-tasks are recorded verbatim as `tests: skipped (no testable surface — {reason})`.
- Use TodoWrite: one todo per sub-task; never mark the next in-progress without approval.

---

## Per sub-task k

### Before k (k ≥ 2)

Review the reports from k − 1. If it was BLOCKED, or DONE WITH DEVIATIONS not yet resolved, or the accumulated suite was not green — stop and resolve first.

### During k

1. **Implement** — launch one `code-implementer` with a sub-task brief:
   ```
   Spec: <path to spec file>
   Sub-task: #N — <name>
   Context from previous sub-tasks: <changed files (production + tests) and what changed — omit for sub-task #1>
   ```
   Review the report by status:
   - **DONE** → proceed to tests.
   - **DONE WITH DEVIATIONS** → assess; minor → proceed and note for the quality review phase; significant → ask the user.
   - **BLOCKED** → resolve the blocker (read code / ask user), relaunch.

2. **Test** — if the sub-task has a testable surface (skip criteria per `.claude/skills/test/SKILL.md`), launch one `test-author`:
   ```
   Mode: sub-task
   Spec: <path>
   Sub-task: #N — <name>
   Scope: <files changed / behavior to cover>
   Existing tests to update: <class::method — reason>   (delta mode only)
   Test log: <tests from sub-tasks 1…k-1 + baseline>
   ```
   A `SKIPPED (no testable surface — {reason})` report is valid for foundational layers (entity / repository / migration / DTO / enum) and purely presentational frontend tweaks — record it verbatim in the test log.

3. **Run the accumulated suite** — every test from the test log for 1…k (plus the baseline in delta mode). Include the **frontend suite** (`{frontend test command}`) whenever any sub-task so far touched frontend, alongside the backend suite. This is the regression guard; run it even when sub-task k added no tests.

4. **Resolve failures until green** — compose a fix brief and relaunch `code-implementer` for each failure, then re-run:
   ```
   Fix: <what is broken>
   Evidence: <failing test + output, or finding with file:line>
   Scope: <files allowed to change>
   ```
   - Failure in **new** tests → bug in sub-task k.
   - Failure in a **previous / baseline** test → sub-task k broke earlier behavior.

   Loop until the entire accumulated suite is green. Do not reach the gate red.

5. **Update** the context log and test log.

### Gate after k (mandatory stop)

1. Surface the reports: **files changed** (production + tests), **lint status**, **test results** — new tests (X passed, or *skipped — reason*) + regression from 1…k-1 (and baseline in delta mode) (Y passed), **all green** — and **deviations** (if any).
2. Ask clearly: **Approve** and proceed to k+1, or send **revisions**.
3. TodoWrite: do not start the next sub-task until approval. If k = N (last) and approved, the loop is complete — return to the command's next phase.

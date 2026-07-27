---
name: test
description: >-
  Test-authoring policy: which test types we write per sub-task — functional
  (web/API, message handler, data table, command, filter — all built on the
  project's test-case base class) and frontend (component + controller tests),
  when unit tests apply, when it is OK to skip tests for a sub-task (no
  testable surface), base classes, fixtures, and how to run the suite. Use
  when writing or planning tests, or deciding whether a sub-task needs tests
  during /implement, /feature, or /improvement.
---

# Test Authoring Policy

Which tests we write, and when it is OK to skip them for a sub-task.
Depth, patterns, and base classes: `docs/testing/` — start at
[[../../../docs/testing/overview|overview]].

---

## Backend test types we write

The **per-sub-task deliverable** for a backend surface is one or more of these functional types — all built
on the project's shared test-case base class (framework kernel + DB + fixtures):

| Type                     | Covers                                                                         | Reference                                 |
|--------------------------|-----------------------------------------------------------------------------------|----------------------------------------------|
| **Web functional / API** | HTTP endpoints, API contracts, status codes, serialization groups, permissions | `docs/testing/web/web-functional.md`      |
| **Message handler**      | Async handler behavior via direct invocation                                    | `docs/testing/command/message-handler.md` |
| **Data table / listing** | List endpoint init + data endpoint, columns, rendering                          | `docs/testing/web/datatable.md`           |
| **Command**              | CLI command execution and assertions                                            | `docs/testing/command/command.md`         |
| **Filter**               | Filter submit + session-persistence flows                                       | `docs/testing/web/filters.md`             |

**Unit tests** (no kernel/DB — isolated test runner) exist for
**pure isolated logic** (services with mockable deps, validators, factories,
authenticators, enum logic). They are used **selectively** where such logic is genuinely
unit-testable — not written mechanically for every class. Functional types above are the
default. See `docs/testing/unit.md`.

We do **not** write a test for every class just to have one. Coverage is driven by a
**testable surface**, not by class count.

---

## Frontend test types we write

Frontend component/controller tests run on the project's frontend test runner (e.g. Jest,
Vitest) — separate from the backend suite — and mirror the source module layout:

| Type              | Covers                                                    | Reference                   |
|--------------------|-------------------------------------------------------------|------------------------------|
| **Component**      | Component render, props, emitted events, user interaction | `docs/testing/frontend.md`  |
| **Page controller** | Controller connect/actions, DOM targets, values behavior  | `docs/testing/frontend.md`  |

- Mirror the existing layout: place a component's test next to (or in the same module folder as)
  the closest existing spec; import the component by relative path from the source root.
- Assert **behavior**, not markup internals: rendered text/branches, emitted events,
  interaction via simulated events. Follow the patterns in neighboring spec files.
- The same **testable-surface** principle applies: a new/changed component or page
  controller with observable behavior is a testable surface; a pure style/markup tweak with
  no logic is not.

---

## When to skip tests for a sub-task

A sub-task **may be implemented without new tests** when it has **no testable surface**
of the types above and no pure logic worth a unit test — e.g. it only adds a foundational
layer:

- Entity / data-model mapping / migration
- Repository plumbing with no branching logic
- DTO / enum / config with no behavior
- Presentational-only frontend change (markup, styling) with no logic, event, or state

In that case:

1. **Skip test authoring** for the sub-task.
2. Record `tests: skipped (no testable surface — {reason})` in the run's test log.
3. The behavior is covered later, **indirectly**, by the tests of the consuming surface
   (API / command / data table / handler / filter) implemented in a later sub-task.

Do **not** skip when a testable surface exists — a red or absent test there blocks the gate.

---

## Fixtures

DB-dependent tests require fixtures. Ensure fixtures cover the scenarios under test
(new fields, states, edge cases). See `docs/testing/fixtures.md`.

---

## Running the suite

```bash
# Full suite + quality gates (from repo root)
{pre-commit / quality-gate command}
```

```bash
# Backend, targeted run
{test command} --filter <TestClass>
# or by module/path
{test command} --filter <ModuleName>
```

```bash
# Frontend
{frontend test command}                          # full suite
{frontend test command} <path-or-name-pattern>   # targeted spec file or test name
```

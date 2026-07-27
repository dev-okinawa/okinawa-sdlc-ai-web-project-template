# Frontend Testing

Component-level tests for UI components and page controllers, running on the
project's frontend test runner (e.g. Jest, Vitest) — separate from the backend suite.

---

## Tooling

Fill in with the project's actual frontend test configuration (runner, environment,
transforms, path aliases, asset/lib mocks).

Source under test lives under the frontend asset path of each module.

---

## Test types

| Type | Covers | Location |
|--------------|-----------------------------------------------------------|--------------------------|
| **Component** | Render, props, emitted events, user interaction | `{tests root}/{Module}/*.spec.js` |
| **Page controller** | Connect/actions, DOM targets, values behavior | `{tests root}/{Module}/*.test.js` |

Tests mirror the source module: place a spec under the folder matching the
component's module, and import the component/controller by relative path from the source root.

---

## Running tests

```bash
{frontend test command}                          # full suite
{frontend test command} <path-or-name-pattern>   # targeted spec file or test name
```

---

## Guidelines

- Assert **behavior**, not markup internals: rendered text/branches, emitted events, target/DOM state after interaction.
- Cover positive and negative paths.
- Follow patterns in neighboring spec files; reuse existing mocks rather than inventing new ones.
- A purely presentational change (markup/styling) with no logic, event, or state is **not** a testable surface — see `.claude/skills/test/SKILL.md`.

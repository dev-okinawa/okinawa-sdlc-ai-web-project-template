---
description: Development workflow — docs-first task pipeline (BRD → Spec → Code → Verification) and the Git lifecycle (branch → commit → MR) woven into it.
---

# Development Workflow

Docs are the source of truth. Code implements the spec; when they diverge, the spec wins unless the spec is wrong — then fix the spec first.

## Task Pipeline

| Phase            | Do                                                                                         | Source of truth                 |
|------------------|--------------------------------------------------------------------------------------------|----------------------------------|
| 1. BRD           | Understand what the client asked for and the risks                                         | `docs/business/{module}/`       |
| 2. Spec          | Read/update the feature spec (business rules, decisions, contracts) before coding          | `docs/specification/{module}/{submodule}/` |
| 3. Branch        | **Create the task branch before writing any code**                                         | `git` skill, `release-flow.md`  |
| 4. Code          | Implement against the spec                                                                 | code                             |
| 5. Verification  | Pre-PR checks, tests, self-review                                                          | `.claude/rules/verification.md` |
| 6. Merge Request | Commit, push, and open the MR (with template)                                              | `git` skill                     |

- The spec is the technical source of truth: it carries the business rules, decisions, and contracts (WHAT, WHY & HOW).
  - Business-level artifacts (user flows, acceptance criteria) stay in the BRD, which remains the client-facing requirements record.
- Do not code before the spec for the touched module/submodule is understood.
  - If the spec is missing or wrong, update `docs/` first (see `.claude/rules/documentation.md`).

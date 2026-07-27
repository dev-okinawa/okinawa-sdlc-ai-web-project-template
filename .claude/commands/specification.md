---
description: Stage 2 of the docs-first pipeline — transform an approved BRD into the technical feature spec (docs/specification/{module}/{submodule}/) with business rules, technical flow, decisions, contracts, and a numbered sub-task breakdown ready for /implement.
argument-hint: "BRD filename or path, e.g. 'create-order' or 'docs/business/commerce/orders/create-order.md'"
---

# /specification — Stage 2: Technical Spec

You are a senior software architect and technical writer. You transform an approved BRD into the feature spec — the single technical source of truth (WHAT, WHY & HOW) — and commit to one architecture with user approval. Business-level artifacts (user flows, acceptance criteria) stay in the BRD.

Layout everywhere: **module → submodule → feature** (same tree under `docs/business/` and `docs/specification/`).

## Pipeline

| Stage | Command | Artifact | Exit gate |
|-------|---------|----------|-----------|
| 1. Requirements | `/business` | `docs/business/{module}/{submodule}/{feature}.md` | BRD `approved` |
| **2. Specification — you are here** | `/specification` | `docs/specification/{module}/{submodule}/{feature}.md` + sub-tasks | feature spec `active`, BRD `spec-complete` |
| 3. Implementation | `/implement` | code + tests + reconciled docs/OpenAPI | quality pipeline green |

Orchestrated end-to-end by `/feature`.

## Contract

- **Input**: `$ARGUMENTS` — BRD filename or path. Resolve: exact `docs/business/{module}/{submodule}/{feature}.md` path → use as-is; bare slug → search across module/submodule folders; not found → ask.
- **Output**: `docs/specification/{module}/{submodule}/{feature}.md` (from `docs/_templates/specification.md`) with numbered sub-tasks; feature spec frontmatter `status: active` (was `draft` while writing); BRD frontmatter `status: spec-complete` + `spec` cross-link; submodule Features table row with the same spec status.
- **Handoff**: `/implement docs/business/{module}/{submodule}/{feature}.md docs/specification/{module}/{submodule}/{feature}.md`.
- **Non-goals**: no production code; no OpenAPI updates (that happens in `/implement`'s quality pipeline).

## Operating Rules

- **BRD is the contract** — every requirement in the spec traces back to the BRD; do not invent requirements.
- **Open items block the spec** — unresolved BRD questions that affect architecture must be resolved by the user first.
- **One architecture** — commit to a single approach per user approval; present options only where meaningful trade-offs exist.
- **Sub-tasks are implementation-ready** — each has a goal, layers, files, and a testable definition of done, sufficient for `/implement` to execute without re-design.
- **TodoWrite** — one todo per phase before starting.

---

## Phase 1 — BRD Intake

**Goal**: load and validate the BRD.

1. Read the BRD; display a structured summary: feature name + module / submodule, FR-* list, affected layers, Domain Checklist status, Open Items.
2. If the BRD status is not `approved`, warn and ask whether to proceed.

**Gate**: if any open item directly affects architecture (data model, permissions, integration choice) and is unresolved — stop and ask the user to resolve before continuing.

## Phase 2 — Deep Exploration

**Goal**: understand the affected code at the depth needed for architectural decisions.

Launch **2–3 `code-explorer` agents in parallel** (`Angle:` + `Feature/domain:` + `Scope hints:` drawn from the BRD's Affected Modules table):

- **Angle — module structure**: full current structure of each affected submodule — entities, services, controllers, DTOs, repositories, and the frontend surface. What gets extended vs. what is new.
- **Angle — pattern for this feature type**: the closest existing feature of the same kind (export → export handler; column → listing/table definition; interactive UI → existing frontend component). Extract the exact pattern to reuse.
- **Angle — cross-cutting wiring**: audit, search indexing, async messaging, permissions, translations — the exact classes and config files similar features use.

For a UI-bearing feature, one angle must cover the frontend explicitly.

Read all files flagged as essential; search `docs/` for pages not yet consulted; produce an exploration summary with file:line references and the updated living doc list.

## Phase 3 — Clarifying Questions

**Goal**: close remaining technical ambiguities before committing to an architecture. **Do not skip.**

Ask about: codebase areas with multiple valid patterns where the BRD doesn't choose; Domain Checklist items still TBD that affect design; doc-vs-code conflicts found in Phase 2.

**Gate**: present all questions at once and wait for answers. If there are genuinely none, state that explicitly and proceed.

## Phase 4 — Architecture Design

**Goal**: select one implementation approach with user approval.

Launch **2–3 `code-architect` agents in parallel**, one per lens, each with the agent's brief format:

```
Lens: minimal | clean | pragmatic
Feature: <name + one-line goal>
Requirements: <BRD functional requirements>
Exploration: <Phase 2 summary + key file paths>
Decisions: <Phase 3 answers>
Docs: <living doc list>
```

- `minimal` — extend existing classes, fewest new files, lowest risk.
- `clean` — strict layered module structure, even if more files.
- `pragmatic` — balance cleanliness and delivery speed; note deferrables.

Compare the approaches and **recommend one** with rationale.

**Gate**:
> **Choose the implementation approach**: `1` minimal / `2` clean / `3` pragmatic — or describe a custom hybrid.

## Phase 5 — Spec Writing

**Goal**: write the self-contained spec and cross-link the docs.

1. **Path**: `docs/specification/{module}/{submodule}/{feature}.md` — same module/submodule/feature as the BRD. If the file exists — extend it without overwriting unrelated sections; otherwise create from `docs/_templates/specification.md` with frontmatter `status: draft` while drafting.
2. Ensure indexes exist:
   - Module index `docs/specification/{module}/{module}.md` from `docs/_templates/module.md` (`layer: specification`) if missing — Submodules table row for the submodule
   - Submodule overview `docs/specification/{module}/{submodule}/{submodule}.md` from `docs/_templates/submodule.md` (`layer: specification`) if missing — Features table + technical inventory as needed
   - Optional: extend *Module specification* / *Submodule specification* when the feature introduces shared cross-feature rules; keep feature-only rules in the feature spec
3. **Sections** (self-contained — business rules, flow, and decisions live here, not in a separate document):
   - **Overview** — business problem, gap, what the feature does (from BRD Summary + Scope)
   - **Technical flow** — Mermaid sequence diagram (User → Controller → Service → DB); the business-level user flow stays in the BRD
   - **Code Map** — every file to create/modify (Type | Path | Role), including the UI layer when present
   - **Permissions** — role permissions and voters
   - **Contracts** — input (DTO fields) and output (response structure)
   - **Business Rules** — formal rules from BRD FRs, edge cases, and confirmed domain checklist items, with inline implementation notes
   - **Edge Cases** — phrased as rules with handling details
   - **Decisions** — chosen architecture + rationale; alternatives considered
   - **Implementation Notes** — non-obvious constraints
   - Do **not** duplicate the BRD's user flow or acceptance criteria.
4. **Sub-task breakdown** — numbered, in strict dependency order (migration → entity → repository → service → controller/API → UI → tests); a UI sub-task comes after the contracts it consumes:

```
## Implementation Sub-tasks

### Sub-task 1: {Name}
**Goal**: {one sentence}
**Layers**: {Entity / Repository / Migration / …}
**Files to create/modify**:
- `{path}` — {what}
**Definition of done**: {testable condition}
```

5. **Statuses & cross-links** (see `docs/specification/specification.md` Spec Lifecycle):
   - Feature spec frontmatter → `status: active` (ready for `/implement`)
   - Submodule Features table → `| {Feature Name} | active | [[./{feature}|{Feature Name}]] |`
   - BRD frontmatter → `status: spec-complete`, `spec: "[[../../../specification/{module}/{submodule}/{feature}|{Feature}]]"`
   - Use `deprecated` only when explicitly retiring/superseding a spec (not part of the happy path)

## Phase 6 — Report & Handoff

```
### Spec generated
**Files created / updated**
- docs/specification/{module}/{submodule}/{feature}.md — {created / updated}, status: active
- docs/specification/{module}/{submodule}/{submodule}.md — Features table (status: active)
- docs/business/{module}/{submodule}/{feature}.md — status: spec-complete

**Implementation sub-tasks**
1. {name} — {layers}
…

**Docs consulted**: {living list}

**Next step**: /implement docs/business/{module}/{submodule}/{feature}.md docs/specification/{module}/{submodule}/{feature}.md
```

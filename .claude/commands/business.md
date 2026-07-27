---
description: Stage 1 of the docs-first pipeline — Business Requirements Document. Create Mode turns a raw client description into a complete, codebase-grounded BRD; Update Mode records client answers into an existing BRD and generates follow-ups until it is ready for approval.
argument-hint: "Client description (any language) — OR — path/slug of an existing BRD, e.g. docs/business/commerce/orders/create-order.md"
---

# /business — Stage 1: Requirements (BRD)

You are a senior business analyst. You turn an ambiguous client request into a complete BRD grounded in the actual codebase, then iterate it with client answers until every open question is closed and the document is approvable.

## Pipeline

| Stage | Command | Artifact | Exit gate |
|-------|---------|----------|-----------|
| **1. Requirements — you are here** | `/business` | `docs/business/{module}/{submodule}/{feature}.md` | BRD `approved` |
| 2. Specification | `/specification` | `docs/specification/{module}/{submodule}/{feature}.md` + sub-tasks | feature spec `active`, BRD `spec-complete` |
| 3. Implementation | `/implement` | code + tests + reconciled docs/OpenAPI | quality pipeline green |

Orchestrated end-to-end by `/feature`. Brownfield changes to existing features use `/improvement` instead.

## Contract

- **Input**: `$ARGUMENTS` — a client description in any language (UA/EN), **or** a path/slug of an existing BRD.
- **Output**: `docs/business/{module}/{submodule}/{feature}.md` created (Create Mode) or updated in place (Update Mode); submodule index `docs/business/{module}/{submodule}/{submodule}.md` and module index `docs/business/{module}/{module}.md` kept current.
- **Handoff**: `/specification docs/business/{module}/{submodule}/{feature}.md` once the BRD is `approved`.
- **Non-goals**: no production code; no technical spec or architecture decisions — that is `/specification`'s job.

## Mode Detection

Inspect `$ARGUMENTS`:

1. Ends with `.md`, resolves to a file under `docs/business/`, or matches a known BRD slug (search `docs/business/{module}/{submodule}/{feature}.md` across modules) → **Update Mode** (U1–U4).
2. Otherwise → **Create Mode** (C1–C5).

Announce the active mode before proceeding.

## Operating Rules

- **Never assume** — every ambiguity becomes a numbered Stakeholder Question with a proposed default drawn from existing architecture patterns.
- **Stay grounded** — edge cases and proposed solutions reference existing code or `docs/` pages, never invented patterns.
- **Scan wide** — even simple requests can touch multiple modules/submodules; always run the discovery phase.
- **BRD template** is `docs/_templates/business.md`; layout and naming per `.claude/rules/documentation.md`.
- **TodoWrite** — create one todo per phase of the active mode before starting.

---

# Create Mode

## Phase C1 — Intake

**Goal**: restate the request and fix the artifact location.

1. Display the original request verbatim, then a 2–4 sentence English summary: what the client wants, the business goal, the expected outcome.
2. Determine the administrative **module** and **submodule** (e.g. `commerce` / `orders`) and propose a kebab-case English **feature** slug. Layout: `docs/business/{module}/{submodule}/{feature}.md` (see the Commerce example).

**Gate**: user confirms module + submodule + feature slug (or provides alternatives) before C2.

## Phase C2 — Discovery

**Goal**: learn what the codebase and docs already say about the affected area.

Launch **2–3 `code-explorer` agents in parallel** (brief format per the agent contract: `Angle:` + `Feature/domain:` + `Scope hints:`):

- **Angle — affected entities & services**: entities, repositories, services, enums, events related to the request across the project's feature modules and shared platform code, with file:line references.
- **Angle — similar features & patterns**: the closest architecturally similar features (exports, listings, API endpoints in the same domain) and the patterns they establish.
- **Angle — integration & UI surface**: cross-cutting wiring (admin listings, reporting/analytics config, export handlers, search indexing, async messaging) and the frontend surface (server-rendered views, UI components, page controllers under the frontend asset path).

Simultaneously launch **`docs-author`** (`Mode: mapping`) to inventory `docs/` pages for the feature area.

Read every file the agents flag as essential, then summarize: relevant modules/submodules and key classes, patterns to reuse, docs consulted (living list), initial scope observations.

## Phase C3 — Impact, Questions & Risks

**Goal**: analyze impact layer by layer and surface everything the team must know before committing.

**3a. Layer impact** — for each layer state `unchanged` / `additive` / `breaking` / `new` / `unknown (→ question)`:

| Layer | Status | Notes |
|-------|--------|-------|
| Data model (new fields / new entity) | | |
| Data access (new queries) | | |
| Service layer (create / update / query logic) | | |
| API / controller endpoints | | |
| Request/response contracts (DTOs) | | |
| Serialization (exposed fields) | | |
| Listing / table columns & filters | | |
| Analytics / reporting fields | | |
| Export handlers / columns | | |
| Async processing (queue / message handlers) | | |
| Events / Listeners | | |
| Enums (new values) | | |
| CLI commands | | |
| Search index mappings / re-index *(if applicable)* | | |
| Permissions | | |
| Audit trail | | |
| Translations / localization | | |
| Sensitive data handling | | |
| Server-rendered views / templates | | |
| UI components | | |
| Page controllers | | |
| Frontend tests | | |
| Migrations (schema change) | | |

**3b. Stakeholder Questions** — numbered, grouped by area (UX / Data Model / Permissions / Integration / Business Logic / Domain). Each question carries:

```markdown
{N}. **Q**: {question}
   **Why**: {what decision depends on the answer}
   **Default**: {reasonable default based on existing patterns}
   **Status**: open
   **Answer**: —
```

**3c. Edge cases** — scenario + proposed handling referencing an existing architectural precedent (file/class).

**3d. Risks** — risk + probability (L/M/H) + impact (L/M/H) + mitigation.

## Phase C4 — Compose & Approve

**Goal**: assemble the full BRD from the template and get sign-off.

Fill `docs/_templates/business.md`: frontmatter (`status: draft`, `iteration: 1`, today's date, module, submodule, feature, empty spec link), original request verbatim, summary, scope (in/out/assumptions), Mermaid user flow, layer table (C3a), stakeholder questions (C3b), functional requirements (SHALL/SHOULD/MAY), NFRs, data impact, edge cases (C3c), risks (C3d), dependencies, domain checklist, acceptance criteria, estimation factors, open items (= all open questions).

Present the full BRD in chat.

**Gate**:
> **Review the BRD above.** Reply `approve` (save), `adjust: [changes]` (revise and re-present), or `cancel` (discard).

## Phase C5 — Save & Handoff

1. Save to `docs/business/{module}/{submodule}/{feature}.md`.
2. Ensure indexes exist and are current:
   - Module index `docs/business/{module}/{module}.md` from `docs/_templates/module.md` (`layer: business`) if missing — Submodules table row if the submodule is new:
     `| {Submodule} | {short description} | [[./{submodule}/{submodule}|{submodule}/]] |`
   - Submodule index `docs/business/{module}/{submodule}/{submodule}.md` from `docs/_templates/submodule.md` (`layer: business`) if missing — Features table row:
     `| {Feature Name} | draft | [[./{feature}|{Feature Name}]] |`
   - Optional: fill *Module specification* / *Submodule specification* only when the new BRD reveals shared rules that are not feature-specific; never invent them.
3. Report:

```
### BRD saved
**File**: docs/business/{module}/{submodule}/{feature}.md
**Indexes updated**: docs/business/{module}/{submodule}/{submodule}.md (+ module index if new submodule)
**Open items before /specification**:
- [ ] Q{N}: {unresolved question}
**Next step**: send open questions to the client, then `/business {feature}` with the answers.
When approved: `/specification docs/business/{module}/{submodule}/{feature}.md`
```

---

# Update Mode

## Phase U1 — Load & Status

**Goal**: read the BRD and surface its Q&A state.

1. Resolve the path (full path as-is; slug → search across module subfolders) and read the BRD.
2. Parse all Stakeholder Questions (number, group, text, status, answer) and display:

```
### BRD: {Feature Name} — iteration {N}, last updated {date}
| # | Group | Status |
|---|-------|--------|
| Q1 | UX / User Flow | open |
| Q2 | Data Model | answered (iteration 1) |
**Open**: {X}  **Answered**: {Y}  **Total**: {Z}
```

3. List all open questions verbatim.

**Gate**:
> **Paste client answers** — one per line, `Q{N}: {answer}`. Any subset is fine; the rest stay open. Type `skip` to close this iteration without recording answers.

## Phase U2 — Record Answers

1. If `skip` → jump to U4.
2. Parse `Q{N}: {answer}` lines; warn about malformed ones.
3. Confirm the parsed set back to the user.

**Gate**: `Proceed with re-analysis? (yes / cancel)`

## Phase U3 — Re-analyze & Update In Place

For each answered question:

1. **Follow-ups** — if the answer introduces new ambiguity or a new architectural area, draft a follow-up question (C3b format, `Source: Follow-up from Q{N} (iteration {M})`).
2. **Scope / requirement changes** — check impact on In/Out-of-Scope, assumptions, FRs, NFRs, edge cases, risks.
3. **Code check** — if the answer reveals a new codebase area, launch a lightweight `code-explorer` (`Angle: verify {assumption}`) before writing.

Then apply all changes to the file atomically:

- Answered questions → `**Status**: answered (iteration {N})` + `**Answer**: {verbatim}`.
- Follow-ups appended to the end of their group.
- Changed sections updated in place, prefixed `*(updated iteration {N})*`.
- Frontmatter: `iteration` +1, `last-updated` today.
- Open Items section rebuilt from all still-open questions.

Report: answers recorded, follow-ups added, sections updated, open items remaining.

## Phase U4 — Status & Handoff

Count remaining open questions.

**If open > 0**:

```
### {X} open question(s) remain
Send them to the client and run `/business {feature}` again with the answers.
```

**If open = 0**:

> **All questions answered — the BRD is ready for approval.**
> Reply `approve` (set `status: approved`; next step `/specification`) or `keep draft`.

On `approve`: set frontmatter `status: approved` and report:

```
### BRD approved
**File**: docs/business/{module}/{submodule}/{feature}.md
**Iterations**: {total}
**Next step**: /specification docs/business/{module}/{submodule}/{feature}.md
```

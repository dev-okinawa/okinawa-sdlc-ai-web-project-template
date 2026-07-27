# Documentation

This folder is an **Obsidian** vault. Entry point for all docs layers:

| Layer              | Folder                                       | Answers                                                                     |
| ------------------ | --------------------------------------------- | --------------------------------------------------------------------------- |
| **Business**       | [[business/business\|business/]]             | *Client requirements* — BRDs, impact analysis, stakeholder questions, risks |
| **Specification**  | [[specification/specification\|specification/]] | *What & how* a feature is built — business rules, entities, services, APIs |
| **Architecture**   | [[architecture/architecture\|architecture/]] | *System-level* shape — cross-cutting concerns, decisions                    |
| **Testing**        | [[testing/testing\|testing/]]                | *How quality is validated* — test strategy, test types, execution           |
| **Infrastructure** | [[infrastructure/infrastructure\|infrastructure/]] | *How to run* — local setup, env vars, workers, AI, optional tools     |

Layout for business and specification: **module → submodule → feature**.

---

## How to navigate

**"I have a client request and need to analyze it"** → `business/{module}/{submodule}/` (run `/business`)

**"I have an approved BRD and need a technical spec"** → run `/specification` → creates `specification/{module}/{submodule}/{feature}.md`

**"I need to understand a submodule"** → `specification/{module}/{submodule}/{submodule}.md`

**"I need to add a feature"** → Read the submodule overview, then create `specification/{module}/{submodule}/{feature}.md`

**"I need to understand a business rule"** → `specification/{module}/{submodule}/{feature}.md` (Business Rules section)

**"I need to understand why we built it this way"** → `architecture/decisions/`

**"I need to understand auth / caching / search"** → `architecture/cross-cutting/`

**"I need the HTTP API contract"** → `openapi.yaml` (repo root) · [[architecture/cross-cutting/api|api.md]]

**"I need to understand how quality is validated"** → `testing/`

**"I need to understand how to run/manage the system"** → `infrastructure/`

---

## Diagrams

- **Mermaid** — fenced code block with language `mermaid` (`flowchart`, `sequenceDiagram`, `stateDiagram-v2`, `sankey-beta`)
- **Obsidian Graph** — for note-link overview across the vault

---

## Templates

| Template                                       | Use for                              |
| ---------------------------------------------- | ------------------------------------- |
| [[_templates/business\|business.md]]           | Business Requirements Document (feature BRD) |
| [[_templates/module\|module.md]]               | Module index (`business/` / `specification/`) + optional module spec |
| [[_templates/submodule\|submodule.md]]         | Submodule map + optional submodule spec (both layers) |
| [[_templates/specification\|specification.md]] | Feature-level technical spec         |
| [[_templates/adr\|adr.md]]                     | Architecture decision record         |

---
description: Layout and conventions for internal documentation under docs/: canonical tree, where each document type belongs, module/submodule/feature and BRD naming rules.
globs: ["docs/**/*.md"]
---

# Documentation (`docs/`)

Docs are the source of truth. This rule covers where documents live and how they are named.

## Canonical Layout

```text
docs/
├── DOCS.md                    # Docs entry point
├── business/business.md       # Layer index (Modules table)
├── business/{module}/         # Module index: {module}.md (+ optional module spec)
│   └── {submodule}/           # Submodule map: {submodule}.md; BRDs: {feature}.md
├── specification/specification.md  # Layer index (Modules table)
├── specification/{module}/    # Same module → submodule → feature tree as business/
│   ├── {module}.md            # Module index (+ optional module spec)
│   └── {submodule}/           # {submodule}.md = map + optional overview; {feature}.md = feature spec
├── architecture/              # System-level, above features
│   ├── overview.md            # Stack, bounded contexts
│   ├── cross-cutting/         # Topics spanning multiple modules
│   └── decisions/             # ADRs: NNNN-title.md
├── infrastructure/            # env.md, queue.md, local-setup.md, commands/
├── testing/                   # overview.md, unit.md, web/, command/, fixtures.md
└── _templates/                # business, module, submodule, specification, adr
```

Example: `business/commerce/orders/create-order.md` → `specification/commerce/orders/create-order.md`.

Indexes use `_templates/module.md` and `_templates/submodule.md` on both layers. Required part is the map (Submodules / Features). Optional *Module specification* / *Submodule specification* hold shared rules that are not a single feature BRD/spec. On `layer: specification`, the submodule file also holds the technical inventory (Structure, Entities, …).

## Where Does a Doc Belong?

| Question                                        | Location                                           |
|-------------------------------------------------|----------------------------------------------------|
| What did the client ask for? Risks?             | `business/{module}/{submodule}/{feature}.md`       |
| What features belong to a submodule?            | `business/{module}/{submodule}/{submodule}.md`     |
| Shared rules for a module / submodule?          | Optional spec block in that module/submodule index |
| How is submodule X built?                       | `specification/{module}/{submodule}/{submodule}.md`|
| How does feature Y work? Why & how?             | `specification/{module}/{submodule}/{y}.md`        |
| How does auth/caching/search work system-wide?  | `architecture/cross-cutting/`                      |
| Why did we choose this approach?                | `architecture/decisions/NNNN-title.md`             |
| Infrastructure, integrations, operations?       | `infrastructure/`                                  |
| How should tests be written and run?            | `testing/`                                         |

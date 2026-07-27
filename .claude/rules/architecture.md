---
description: Architecture rules — modular structure, layered responsibilities, service naming, permissions, caching, and database conventions. Fill in with your stack.
globs: ["src/**/*"]
---

# Architecture

**Stack**: *(e.g. Symfony/PHP monolith, MySQL + ORM, Elasticsearch, Redis cache, message queue, Vue/React frontend — replace with your actual stack)*.

## Modular Structure

- `{core path}` — core logic, shared entities, base services.
- `{modules path}/{Name}{Module}/` — feature-specific logic.
- A domain feature keeps all its components (entry point, data model, service, UI) inside its own module.

## Module anatomy

Canonical layout of a `{Name}{Module}` — not every module has every directory, but when a component exists it lives here. Each node's comment states the layer's responsibility.

```
{Name}Module/
├── Controller/                     # thin entry points (permission checks + delegation)
│   └── Api/                        # API endpoints (JSON, DTO-driven)
├── Command/                        # CLI commands
├── Entity/                         # data model definitions (ORM mapping)
├── Enum/                           # enums (statuses, types, permission sets)
├── Repository/                     # all DB access; no raw queries in services
│   └── QueryBuilder/               # reusable query builders for complex reads
├── Service/                        # domain logic, one class per responsibility:
│                                   #   Service   — core domain logic
│                                   #   Manager   — complex/multi-entity workflows
│                                   #   Creator/Editor — single entity create/update
│                                   #   Provider  — retrieval, filtering, statistics
│                                   #   Resolver  — entity lookup by id/code/DTO
│                                   #   Processor — data transformation & validation
├── DTO/                            # data transfer between layers
├── Form/                           # form/validation types
├── DataTable/                      # server-side tables (if applicable)
├── Queue/                          # async work (queue/message bus)
│   ├── Message/                    # message DTOs
│   └── Handler/                    # command/event handling
├── Event/                          # domain events
├── EventListener/                  # listeners/subscribers
├── Security/
│   └── Voter/                      # object-level authorization checks
├── Factory/                        # object/request factories
├── Exception/                      # module-specific exceptions
├── DataFixtures/                   # test/seed fixtures
└── Resources/
    ├── config/                     # routes, service wiring
    ├── translations/               # i18n strings
    ├── views/                      # server-rendered templates (if applicable)
    └── assets/js/
        ├── components/             # frontend components (complex interactive UI)
        └── controllers/            # lightweight page-behavior controllers
```

## Caching and Performance

- Use a central cache manager; define a consistent key pattern and default TTL.
- Prevent N+1: explicit joins/preloads for relations in repositories and list views.
- Heavy tasks (reindexing, emails, audits) go through the async queue/message bus.

## Database

- Attribute/annotation-based mapping; soft delete if the project requires it.
- Every schema change goes through a migration.

## Project-Specific

*(Fill in with your project's compliance and domain requirements, e.g.:)*

- Audit trail requirements for sensitive data changes.
- Localization requirements (which languages/locales, where translations live).
- Sensitive/PII data handling rules (masking, access control).

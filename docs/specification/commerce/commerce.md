---
module: commerce
layer: specification
status: active
last_reviewed: 2026-07-25
---

# Commerce

**Purpose:** Technical specs for the Commerce domain.

**Layer:** `specification`

---

## Submodules

| Submodule | Description | Index |
| --------- | ----------- | ----- |
| Orders | Order create / cancel | [[orders/orders\|orders/]] |

---

## Module specification

### Overview

Technical home for order lifecycle APIs and services. Feature specs live under submodules; this index holds cross-submodule conventions only.

### Core concepts

| Term | Meaning |
|------|---------|
| Minor units | Integer money; paired with ISO 4217 currency code |
| Order number | Assigned only on confirm; immutable thereafter |

### Business rules

| # | Rule | Notes |
|---|------|-------|
| MR-01 | Order aggregate SHALL reference exactly one customer id | Enforced in create/confirm services |
| MR-02 | Monetary fields SHALL use integer minor units + currency | Shared DTO / entity convention |

### Data ownership

| Concept | Owner (submodule) | Notes |
|---------|-------------------|-------|
| Order / OrderLine | orders | Canonical tables / entities |

### Non-functional constraints

| Area | Constraint |
|------|------------|
| Security | All mutating order endpoints require operator auth |
| Audit | Confirm and cancel MUST be auditable |

### Dependencies & integrations

| Module / Submodule / System | Kind | Why |
|-----------------------------|------|-----|
| catalog *(future)* | uses | product / SKU resolution when creating lines |

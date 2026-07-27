---
module: commerce
layer: business
status: active
last_reviewed: 2026-07-25
---

# Commerce

**Purpose:** Order lifecycle and related commercial flows.

**Layer:** `business`

---

## Submodules

| Submodule | Description | Index |
| --------- | ----------- | ----- |
| Orders | Order create / cancel flows | [[orders/orders\|orders/]] |

---

## Module specification

### Overview

Commerce owns customer-facing commercial transactions. Sibling modules (catalog, payments) appear only when a feature explicitly depends on them.

### Actors & roles

| Role | How they interact with this module |
|------|------------------------------------|
| Customer | Places and tracks orders |
| Operator | Creates, confirms, cancels orders on behalf of customers |

### Core concepts

| Term | Meaning |
|------|---------|
| Order | Commercial commitment: customer + lines + status |
| Order line | Product, quantity, unit price within an order |
| Minor units | Integer money amounts (e.g. cents); currency is ISO 4217 |

### Scope & boundaries

| In scope | Out of scope |
|----------|--------------|
| Order create / confirm / cancel | Shipping, returns, invoicing |
| Stock reservation at confirm | Full warehouse WMS |

### Business rules

| # | Rule | Notes |
|---|------|-------|
| MR-01 | An order SHALL belong to exactly one customer | |
| MR-02 | Money amounts SHALL be stored in minor units with an ISO 4217 currency | |

### Data ownership

| Concept | Owner (submodule) | Notes |
|---------|-------------------|-------|
| Order / OrderLine | orders | Canonical commercial aggregate |

### Dependencies & integrations

| Module / Submodule / System | Kind | Why |
|-----------------------------|------|-----|
| catalog *(future)* | uses | SKU / product resolution for lines |

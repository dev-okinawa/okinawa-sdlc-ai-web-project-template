---
status: draft
date: 2026-07-25
last-updated: 2026-07-25
iteration: 1
module: "commerce"
submodule: "orders"
feature: "Cancel order"
---

# BRD: Cancel order

---

# Part 1 — For Product Owner

## Original Request

> Operator should cancel an order that is not yet shipped, with a reason.

## Summary

Allow an operator to cancel an unshipped order and record a cancellation reason. Cancelled orders stay in history; stock is released.

## Scope

### In-Scope

- Cancel when status is draft or confirmed (not shipped)
- Mandatory cancellation reason
- Release reserved stock

### Out-of-Scope

- Refunds
- Partial line-item cancel

### User Roles

| Role | How they interact with this feature |
|------|-------------------------------------|
| Operator | Cancels own team's orders |
| Admin | Cancels any order |

## Stakeholder Questions

1. **Q**: Is a free-text reason enough, or a fixed list?
   - **Default**: Fixed list + optional comment.
   - **Status**: open
   - **Answer**:

## Acceptance Criteria

- Cancel blocked for shipped orders
- Reason is required
- Stock for cancelled lines is released

---

# Part 2 — For Engineering

## Affected Modules

| Module / Submodule | Impact |
|--------------------|--------|
| Commerce / Orders | Owns the flow; status transition, stock release |

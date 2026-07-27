# API layer

Conventions for the public HTTP API. Contract source of truth: OpenAPI YAML.

---

## Spec

| | |
|---|---|
| **File** | [`openapi.yaml`](../../../openapi.yaml) (repo root) |
| **Version** | `info.version` in the YAML |
| **Auth** | Bearer JWT (`components.securitySchemes.bearerAuth`) |
| **Base path** | `/api` |

`api-docs-author` reconciles controllers / DTOs / routes against this file.

---

## Conventions

- Controllers are thin: permission check → service call → response DTO / entity serialization.
- Request bodies validated against DTO schema before the service layer.
- Errors use a shared shape: `{ code, message, details? }` (see OpenAPI `Error` / `ValidationError`).
- List endpoints paginate; mutation endpoints return the updated resource.
- New / changed endpoints update `openapi.yaml` (repo root) in the quality pipeline.

---

## Demo endpoints (Orders)

| Method | Path | Permission | Spec |
|--------|------|------------|------|
| `POST` | `/api/orders` | `ORDER_CREATE` | [[../../specification/commerce/orders/create-order\|Create order]] |
| `POST` | `/api/orders/{id}/confirm` | `ORDER_CREATE` | [[../../specification/commerce/orders/create-order\|Create order]] |
| `POST` | `/api/orders/{id}/cancel` | `ORDER_CANCEL` | [[../../business/commerce/orders/cancel-order\|Cancel order]] |

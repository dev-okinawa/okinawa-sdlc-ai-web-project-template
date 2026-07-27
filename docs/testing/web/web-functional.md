# Web Functional Testing

HTTP-level tests using the project's shared test-case base class.

---

## Base class

All web functional tests extend `{shared test-case base class}`.

Provides (or should provide):
- preloaded fixtures and fixture references
- per-test DB transaction/rollback
- auth helpers
- request helpers (API + form)
- response helpers (e.g. JSON decoding)

---

## Specialized subtypes

The base class is also the foundation for more specific test types:

| Subtype | Doc |
|---|---|
| Listing / data table | [[./datatable\|Listing / data-table testing]] |
| Filters | [[./filters\|Filter testing]] |
| Command | [[../command/command\|Command testing]] |
| Message handler | [[../command/message-handler\|Message handler testing]] |

---

## Location

```
{tests root}/Web/           ← HTTP functional tests by module
```

---

## Guidelines

- Test success path + permission checks + key validation errors
- Cover both positive and negative scenarios
- Assert response payload shape and key fields, not only status code
- Use auth helpers with specific roles/org context when testing access control

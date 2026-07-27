# Unit Testing

Pure isolated tests for service and domain logic.

---

## Base class

`{unit test base class}` — no framework kernel, no database, no fixtures.

---

## What to test

- Service methods with mocked dependencies
- Validators and value objects
- Factories and builders
- Authenticators and handlers
- Enum-based logic

---

## Location

```
{tests root}/Unit/
└── {Module}/
```

---

## Conventions

- Mock all external dependencies
- Use data providers for multiple input variants
- Keep test methods focused on one behavior
- Shared entity creation helpers → reusable traits/helpers

# Fixtures in Tests

Fixtures provide stable, reusable test data for integration and web functional tests.

---

## Principles

- Keep fixtures deterministic and minimal
- Reuse references instead of duplicating entity creation logic
- Separate seed data by module/domain responsibility

---

## Location

- Module fixtures: `{module path}/{Module}/DataFixtures/`

---

## Guidelines

- Name fixtures by intent (not by technical detail)
- Include only data needed for tested scenarios
- Update fixture references when entity relations evolve

---
name: review
description: >-
  Perform code review.
  Checks types, security, architecture compliance, testing, and docs-first alignment.
  Use when reviewing pull requests, staged changes, or when
  the user says "review code", "check my code", or "review PR".
---

# Code Review

Comprehensive code review checklist. Covers types, architecture, security, database patterns,
API design, performance, testing, and docs alignment.

References: `@.claude/skills/security/SKILL.md` for full security checklist.

## Review Scope

By default, review changes from `git diff` (unstaged) or `git diff --cached` (staged).
For PR review: `git diff main...HEAD` or specific branch/commit ref.

## Confidence Scoring

Rate each issue 0–100. **Only report issues with confidence >= 75.**

| Score | Meaning                                                                     |
|-------|-------------------------------------------------------------------------------|
| 75    | Very likely real issue, will impact functionality or violates project rules |
| 90    | Confirmed issue with evidence from project conventions                      |
| 100   | Certain — will break in production or violates mandatory rule               |

## Checklist

### 1. Type Safety & Language Strictness

- [ ] Type hints/annotations on all function params, returns, properties (no `any`/`mixed` where a concrete type exists)
- [ ] No implicit type coercions that could mask bugs
- [ ] Constructor/dependency injection used; no bare instantiation in services/logic
- [ ] Null/undefined checks present where expected
- [ ] Linter (ESLint, mypy, PHPStan, etc.) passes with strict config

### 2. Architecture Compliance

- [ ] Business logic separated from HTTP/request handling
- [ ] Layers respected: handlers/controllers thin, logic in services/managers
- [ ] No cross-module imports without an abstraction layer
- [ ] Naming conventions match project standards
- [ ] Single Responsibility Principle observed: one class/function = one reason to change
- [ ] DRY principle: logic not duplicated across layers

### 3. Database & Data Access

- [ ] No N+1 queries: relations loaded via explicit joins/eager loading
- [ ] Database schema changes have corresponding migrations
- [ ] Queries use parameterized statements — no string concatenation with user input
- [ ] Connection pooling/caching considered for high-load operations
- [ ] Soft deletes or audit trails implemented where required

### 4. Security (2-layer check)

Reference: `@.claude/skills/security/SKILL.md` for the exhaustive list.

- [ ] **Layer 1 (Role):** Authentication/authorization at entry point (controller/route handler)
- [ ] **Layer 2 (Resource):** Resource-level permissions checked before access
- [ ] Input validation: all user inputs checked against schema/constraints
- [ ] Output sanitization: no PII in logs, error messages, or debug output
- [ ] API endpoints require auth (token, key, session) and use HTTPS
- [ ] Secrets not hardcoded; use environment variables/secrets manager
- [ ] SQL injection, XSS, CSRF vectors checked

### 5. API Endpoints

- [ ] Input validated against schema/DTO (types, length, format constraints)
- [ ] Response shape matches documented spec
- [ ] Serialization controlled: only intended fields exposed (groups/selectors)
- [ ] Error responses standardized (HTTP status + error code + message)
- [ ] API docs (OpenAPI, Swagger, etc.) updated if the contract changed
- [ ] Pagination/filtering implemented for list endpoints

### 6. Performance

- [ ] No obvious N+1 queries or unbounded data loading
- [ ] Heavy/long operations async (queues, background jobs)
- [ ] Caching strategy clear (Redis, in-memory, CDN as appropriate)
- [ ] No unnecessary loops or repeated DB calls
- [ ] Indexes considered for frequently-queried columns

### 7. Frontend (Language-agnostic)

- [ ] Component naming and file structure follows project conventions
- [ ] Props/inputs typed (TypeScript, PropTypes, or equivalent)
- [ ] Events/outputs clearly defined
- [ ] No inline styles (use CSS classes or theme system)
- [ ] State management pattern consistent (hooks, composables, store, etc.)
- [ ] No `console.log` or debug code left
- [ ] Accessibility considered (ARIA labels, semantic HTML, keyboard nav)

### 8. Testing

- [ ] New feature/endpoint has a corresponding test
- [ ] Test naming: descriptive, matches project convention
- [ ] Happy path + error cases covered
- [ ] No hardcoded test data; use fixtures/factories
- [ ] Mocks/stubs isolated; no external service calls in unit tests
- [ ] Integration tests call real services/database

### 9. Docs-First Alignment

- [ ] If a feature spec exists in docs, implementation matches it
- [ ] No undocumented business rules silently enforced
- [ ] Response shape matches documented spec
- [ ] If code deviates from spec — deviation flagged and discussed

## Output Format

```markdown
## Code Review Summary

**Files Reviewed:** [count]
**Risk Level:** Low / Medium / High

### Critical Issues (confidence >= 90)
[Must fix before merge]

### Important Issues (confidence >= 75)
[Should fix]

### Architecture Notes
[Boundary violations, naming, structure]

### Docs Alignment
[Spec mismatches if applicable]

### Positive Notes
[Good patterns observed]
```

## Review Style

- Constructive, not critical — explain the "why"
- Reference specific project rules (`architecture.md`, `coding-standards.md`, security SKILL)
- Provide concrete fix suggestions with code
- Acknowledge good patterns
- Prioritize by impact: security > correctness > architecture > style

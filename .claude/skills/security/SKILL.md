---
name: security
description: >-
  Security checklist: authorization (role + resource), authentication,
  PII/data protection, audit trails, injection prevention, XSS, serialization leaks,
  input validation, sessions, race conditions. Use when reviewing security,
  implementing endpoints, handling sensitive data, or modifying permissions.
---

# Security Checklist

Normative references: `.claude/rules/architecture.md` (permissions), `docs/architecture/cross-cutting/` (API layer & auth).

## 1. Authorization — Two-Layer Check (CRITICAL)

Every protected endpoint/action **must** enforce both layers:

```
Layer 1: role-based (does the user's role allow this action?)
Layer 2: resource-level (can this user access THIS specific entity?)
```

### Examples by tech stack

**Symfony:**
```php
$this->denyAccessUnlessGranted(RoleEnum::VIEW_DISEASE_CASE);
$this->denyAccessUnlessGranted(ResourceEnum::DISEASE_CASE, $entity);
```

**Express/Node:**
```js
if (!user.hasRole('VIEW_DISEASE_CASE')) throw new ForbiddenError();
if (!user.canAccess('DISEASE_CASE', entity)) throw new ForbiddenError();
```

### Checklist

- [ ] New endpoint has a role-based check at the entry point
- [ ] If the endpoint accesses a specific resource (show/edit/delete), **also** check resource-level permission
- [ ] Role/permission definitions registered in the permission system
- [ ] Resource-level permission logic isolated in a service/voter, not mixed with business logic
- [ ] Closed/archived/restricted resources require an additional permission check
- [ ] Admin bypass documented and tested

## 2. Authentication

- [ ] All protected endpoints require valid authentication (token, session, OAuth, etc.)
- [ ] Authentication enforced at the entry point (middleware, filter, interceptor)
- [ ] Sessions or tokens validated on every request
- [ ] Invalid/expired tokens rejected (401 Unauthorized)
- [ ] Credentials transmitted over HTTPS only
- [ ] No credentials in logs, error messages, or debug output

### Session & Token Security

- [ ] Session idle timeout enforced (auto-logout after N minutes)
- [ ] Password expiry enforced where applicable
- [ ] 2FA/MFA flow: user cannot access protected routes until verified
- [ ] Login throttling: rate limit failed attempts (e.g. 4 attempts/minute)
- [ ] Blocked/suspended users rejected before authentication succeeds
- [ ] Tokens/sessions include user context; no unauthorized privilege escalation

## 3. Injection Prevention

All user-supplied input bound as parameters, never concatenated into code/queries.

### SQL/DQL Injection

```
SAFE:
  query.where("status = :status").setParameter("status", input)

DANGEROUS:
  query.where("status = " + input)
```

### Command Injection

```
SAFE:
  exec("command", ["--arg", input])

DANGEROUS:
  exec("command --arg " + input)
```

### Code Injection

```
SAFE:
  eval(trusted_code_only)

DANGEROUS:
  eval(user_input)
```

### Checklist

- [ ] All user-supplied values bound via placeholders/parameterized queries — never concatenated
- [ ] Dynamic field/table names come from enums/constants/whitelist only, never from request input
- [ ] OS commands sanitized or use language-native safe APIs (no `shell=True`)
- [ ] Template code never evaluates user input (Twig, ERB, Jinja auto-escape by default)
- [ ] Serialization (JSON, pickle, etc.) does not deserialize untrusted data

## 4. XSS / Template Injection

- [ ] User-generated content auto-escaped in HTML templates (Twig, React, Vue auto-escape by default)
- [ ] Raw/unescaped output used **only** for server-generated/trusted content (icons, form widgets, translations)
- [ ] URLs from user input validated before rendering in `href` / `src` attributes
- [ ] No eval/innerHTML/dangerouslySetInnerHTML with user data
- [ ] Content Security Policy (CSP) headers configured

## 5. Serialization / Data Leaks

APIs and exports must not expose unintended data.

- [ ] Responses explicitly specify which fields to include (allowlist, not denylist)
- [ ] Sensitive fields (password hash, API keys, internal IDs, PII in non-authorized contexts) excluded
- [ ] Nested relations checked — unintended cascade leaks prevented
- [ ] Different serialization contexts for different users (e.g. admin sees full data, guest sees minimal data)
- [ ] Database exports, backups, CSV downloads all respect authorization

**Example patterns:**
- **Symfony:** `AbstractNormalizer::GROUPS` + `#[Groups([...])]` on entity fields
- **Express:** Service layer transforms entity → DTO before response
- **GraphQL:** Field-level resolvers check authorization before returning data

## 6. Input Validation

All external input validated before use.

- [ ] API endpoints validate input against schema (type, length, format, allowed values)
- [ ] File uploads validated: mime type, size limit, extension whitelist
- [ ] Dates/times validated: parseable, within expected range
- [ ] Emails validated: format + optionally confirmed
- [ ] URLs validated: scheme (https only?), domain whitelist if applicable
- [ ] Failed validation returns 400/422 with a clear error, never processes invalid data

**Example:**
```
POST /api/users
{
  "name": { type: string, minLength: 1, maxLength: 255 },
  "email": { type: string, format: email },
  "password": { type: string, minLength: 8 },
  "avatar": { type: file, maxSize: 5MB, mimeType: ["image/jpeg", "image/png"] }
}
```

## 7. Race Conditions & Concurrency

Critical operations (financial, inventory) must be atomic.

- [ ] Financial/inventory operations use transactions with pessimistic locking or atomic operations
- [ ] Concurrent entity updates use optimistic locking (version checks) where applicable
- [ ] Distributed critical sections guarded by locks (Redis locks, database locks)
- [ ] Test concurrent scenarios: two users editing the same resource simultaneously

**Example race condition:**
```
User A reads balance: 100
User B reads balance: 100
User A withdraws 50 → 50
User B withdraws 50 → 50  (should fail, but balance was written twice!)
Final: 50 (lost update)
```

**Fix:** Use a transaction + pessimistic lock or atomic compare-and-swap.

## 8. API Security

- [ ] API endpoints under a protected route (require auth)
- [ ] Rate limiting enforced (prevent brute force, DoS)
- [ ] CORS configured: only allow trusted origins
- [ ] API versioning: deprecated versions supported with warnings
- [ ] Error responses standardized: no stack traces, no internal database/file paths leaked
- [ ] API keys stored securely (hash, never plain text); rotated periodically
- [ ] Public APIs documented (OpenAPI/Swagger) with security requirements

## Do Not

- Return entities/data from endpoints without explicit authorization + serialization filtering
- Skip resource-level permission check when a role check exists — they serve different purposes
- Render user-generated content unescaped in templates
- Concatenate user input into SQL/DQL/OS commands
- Log or expose PII in error messages, debug output, or email subjects
- Send real sensitive data to unverified/unauthorized users in any channel
- Hardcode secrets (API keys, DB passwords, JWT secrets) in source code — use environment variables
- Store passwords in plain text — use a strong hash (bcrypt, argon2, scrypt)
- Trust client-side validation — always validate server-side
- Use `eval()` on untrusted input

# Filter Testing

Tests for filter submit and session-persistence flows, built on the web functional foundation.

---

## What to cover

- Filter submission and the resulting query/response
- Session persistence of filter values across requests
- Filter reset / clear behavior

---

## Location

```
{tests root}/Web/{Module}/{Entity}FilterTest.php
```

---

## Guidelines

- Verify persisted filter values survive a follow-up request
- Cover empty/invalid filter input handling

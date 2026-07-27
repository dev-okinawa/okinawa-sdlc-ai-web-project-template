# Listing / Data-Table Testing

Tests for server-side listings (init + data endpoint), built on the web functional foundation.

---

## What to cover

- Listing initialization request (columns, filters, config)
- Data endpoint response shape (rows, pagination, sorting)
- Permission checks on the listing endpoint

---

## Location

```
{tests root}/Web/{Module}/{Entity}ListingTest.php
```

---

## Guidelines

- Assert column definitions and row shape, not just HTTP status
- Cover sorting, pagination, and filter combinations relevant to the listing

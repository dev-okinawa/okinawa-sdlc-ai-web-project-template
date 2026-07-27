# Scheduled Tasks

Cron / periodic jobs used by the application.

---

## Scheduling matrix

| Command | Frequency | Why it runs | Owner |
|---|---|---|---|
| `{command:name}` | daily / hourly / periodic | *(what this job does and why it's needed)* | [[../specification/{module}/{submodule}/{submodule}\|{module}/{submodule}]] |

---

## Notes

- Domain-specific behavior and arguments are documented in the owning module doc, not duplicated here.
- Queue-backed tasks should be coordinated with [[./queue\|Queue workers]].

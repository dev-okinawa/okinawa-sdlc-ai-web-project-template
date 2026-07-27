# Queue Workers

Async queue workers managed by `{process manager, e.g. Supervisor}` inside the `{worker service}` container.

Config: `{path to worker config}`

---

## Workers

| Queue | Workers | Memory limit | Time limit | Purpose |
|---|---|---|---|---|
| `{queue-name}` | 1 | default | `{seconds}` | *(what this queue processes)* |

All workers: `autostart=true`, `autorestart=true`, `startretries={N}`.

---

## Parallel workers and transport DSN

Workers with more than one process typically need a per-process consumer group via env override — fill in with your transport's actual mechanism.

---

## Logs

Inside the worker container:

```
{path to worker logs}/<queue>.log
{path to worker logs}/<queue>-error.log
```

---

## Manual consumer (without Docker)

```bash
# Run a single queue consumer manually
{command to run a single consumer}
```

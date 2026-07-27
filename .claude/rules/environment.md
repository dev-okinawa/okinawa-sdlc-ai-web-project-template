---
description: Detect where the project runs (host vs container) before executing any project command; route stack-specific commands into the containerized service when the stack is dockerized.
---

# Execution Environment (Host vs Docker)

Before running any project command, determine where the stack actually runs. Never assume host.

## 1. Detect Once Per Session

Run from the project root and remember the result:

```bash
docker compose ps --status=running --services 2>/dev/null | grep -qx {app service} \
  && echo "docker" || echo "host"
```

- `docker` → containers are up; all project commands go through the `{app service}` service.
- `host` and no running containers → verify the runtime is available on the host (e.g. `php -v`, `python --version`) before proceeding.

## 2. Command Routing

| Command class                                                              | Docker mode                                    | Host mode |
|-----------------------------------------------------------------------------|-------------------------------------------------|-----------|
| Language/runtime CLI (console commands, test runners, static analysis)     | `docker compose exec {app service} <cmd>`       | as is     |
| Package managers (`composer`, `yarn`, `npm`, `pip`, …)                      | `docker compose exec {app service} <cmd>`       | as is     |
| `make <target>`                                                              | `docker compose exec {app service} make <target>` | as is  |
| `git`, `docker`, `docker compose`, file operations                          | always on host                                  | as is     |

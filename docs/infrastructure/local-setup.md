# Local Development Setup

How to run the project locally. Fill in with your project's actual stack and commands.

---

## Prerequisites

- Docker + Docker Compose
- Git

---

## Stack

| Service | Image | Port | Purpose |
|---|---|---|---|
| `app` | *(custom)* | *(internal port)* | Application runtime |
| `{reverse proxy}` | *(image)* | *(exposed port)* → *(internal)* | Web server, proxies to app |
| `database` | *(image)* | *(port)* | Main database |
| `{cache}` | *(image)* | *(port)* | Cache + queue transport (if shared) |
| `{mailer}` | *(image)* | *(port)* | Local mail catcher |
| `{worker service}` | *(custom)* | — | Queue workers |
| `{monitoring}` | *(image)* | *(port)* | Error tracking (optional) |

---

## First run

```bash
# 1. Clone and go to the project directory

# 2. Copy the env file
cp .env .env.local
# Edit .env.local for your local environment

# 3. Build and start containers
{start command}

# 4. Enter the app container
{exec-into-app command}

# 5. Install dependencies
{backend package manager} install
{frontend package manager} install

# 6. Init database
{database refresh command}

# 7. Build frontend
{frontend build command}
```

---

## Common commands

| Command | Description |
|---|---|
| `{start command}` | Build images + start all containers |
| `{stop command}` | Stop all containers |
| `{shell command}` | Open a shell inside the app container |
| `{db refresh command}` | Drop → create → migrate → fixtures |
| `{pre-commit / quality-gate command}` | Run linters + static analysis + formatter |

---

## Useful URLs (after start)

| URL | Service |
|---|---|
| `http://localhost:8000` | Application |
| `http://localhost:1080` | Local mail catcher UI |

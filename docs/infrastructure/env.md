# Environment Variables

All variables are defined in `.env`. Local overrides go in `.env.local` (not committed).

Fill in with your project's actual variables, grouped by subsystem.

---

## Core

| Variable | Default | Description |
|---|---|---|
| `APP_ENV` | `dev` | Environment (`dev` / `test` / `prod`) |
| `APP_SECRET` | — | Application secret key (change in production) |
| `APP_URL` | `http://localhost:8000` | Base URL of the application |

---

## Database

| Variable | Default (Docker) | Description |
|---|---|---|
| `DATABASE_URL` | `{driver}://user:password@database:{port}/{db}` | Database connection string |

---

## Queue / Async

| Variable | Default | Description |
|---|---|---|
| `{QUEUE_TRANSPORT_DSN}` | `{transport DSN}` | Default async transport DSN |

---

## Search *(if applicable)*

| Variable | Default (Docker) | Description |
|---|---|---|
| `{SEARCH_HOST}` | `{search service}` | Search engine host |
| `{SEARCH_PORT}` | `{port}` | Search engine port |

---

## Cache

| Variable | Default (Docker) | Description |
|---|---|---|
| `{CACHE_DSN}` | `{cache connection}` | Cache connection DSN |

---

## Mailer

| Variable | Default | Description |
|---|---|---|
| `MAILER_DSN` | `null://null` | Mailer transport (use local mail catcher in Docker) |
| `MAILER_SENDER` | — | From address |

---

## Session & Security

| Variable | Default | Description |
|---|---|---|
| `SESSION_IDLE_TIME` | `1800` | Session idle timeout in seconds (≤0 disables) |

---

## Monitoring

| Variable | Default | Description |
|---|---|---|
| `SENTRY_DSN` | *(empty)* | Error tracking DSN |

---

## External integrations

| Variable | Default | Description |
|---|---|---|
| `{EXTERNAL_API_BASE_URL}` | — | Base URL of an external integration |
| `{EXTERNAL_API_USER}` | — | External integration credentials |

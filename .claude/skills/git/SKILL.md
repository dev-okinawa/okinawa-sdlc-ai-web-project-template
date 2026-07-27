---
name: git
description: >-
  Full Git lifecycle — create a branch (start of task), commit (during work),
  and open a Merge/Pull Request (end). Enforces naming for branches, commits,
  and MR/PR titles, pre-commit checks, and MR/PR templates. Use when the user
  says "create branch", "commit", "save changes", "push", "create MR",
  "open MR", "open PR", or "merge request".
---

# Git Lifecycle

| Stage         | When                                     | Section                              |
|---------------|-------------------------------------------|--------------------------------------|
| Branch        | Start of a task, before writing code     | [1. Branch](#1-branch)               |
| Commit        | Feature complete and all checks pass     | [2. Commit](#2-commit)               |
| Merge Request | Right after the push                     | [3. Merge Request](#3-merge-request) |

Mandatory rules:
- **Commit only when the feature is complete and all tests and linters pass** — one commit at the end, no work-in-progress commits.
- **Push right after committing, then open the Merge/Pull Request.**

## Naming

Branch matches `[a-z0-9-]+` (lowercase letters, digits, dashes only).

|              | Branch (lowercase)                      | Commit message                           | MR/PR title (Title Case)             |
|--------------|-------------------------------------------|--------------------------------------------|-----------------------------------------|
| Task-related | `{project-code}-{task-id}-{short-name}` | `{PROJECT-CODE}-{TASK-ID} {Description}` | `{PROJECT-CODE}-{TASK-ID} {Summary}`   |
| Non-task     | `{reason}`                              | `{Capitalized Description}`              | `{Capitalized Summary}`                |

Examples:

```
# task-related (project code ABC)
branch  abc-1-project-setup
commit  ABC-1 Project Setup
MR      ABC-1 Project Setup

# non-task
branch  updated-docker-config
commit  Update Docker Config
MR      Updated Docker Config
```

The branch name is the lowercase-dashed form; the MR/PR title is its well-formatted (Title Case, spaces) version.

---

## 1. Branch

The base branch is **not always `main`**.

1. **Ask which branch to branch off from** and remember it — it is also the MR/PR target in section 3.
2. Determine task context — is this related to a task? project code + task ID and a short summary?
3. Build the branch name per the Naming table (`[a-z0-9-]+` only).
4. Create from the up-to-date base:

```bash
git fetch origin
git checkout -b abc-1-project-setup origin/main    # or origin/1.16.x for a release fix
```

## 2. Commit

Commit only once the feature is finished. No work-in-progress commits.

### Step 1 — Run tests & linters first

Run the checks relevant to the change — see the checklist in `.claude/rules/verification.md` (through the container when dockerized). Everything must pass **before** committing; if anything fails — fix it, do not commit broken code.

### Step 2 — Stage & review

```bash
git status
git diff --cached --stat
```

Nothing staged → warn. Unrelated changes must not share a commit — split them into separate commits if needed.

### Step 3 — Commit & push

Message per the Naming table (branch `{code}-{id}-{name}` → reuse `{CODE}-{ID}` as the prefix). Then push:

```bash
git commit -m "ABC-1 Project Setup"
git push -u origin HEAD
```

Then go to section 3.

## 3. Merge Request

### Step 1 — Pre-flight

Rebase onto the **base branch chosen in section 1** (`main` or `N.M.x`), not blindly `main`:

```bash
git fetch origin
git rebase origin/main      # or origin/1.16.x — the base you branched from
```

If the MR/PR goes into a release branch, remember the forward-merge rule (into `main` and higher release branches) — see `docs/infrastructure/release-flow.md`.

### Step 2 — Pick a template

| Change type                        | Template                                            |
|-------------------------------------|-------------------------------------------------------|
| New feature / endpoint / module    | `.gitlab/merge_request_templates/feature.md`        |
| Refactoring, tooling, docs, infra  | `.gitlab/merge_request_templates/improvement.md`    |
| Bug fix                            | `.gitlab/merge_request_templates/fix.md`            |

### Step 3 — Push and open the MR/PR

**GitLab** — push with MR push options (on host):

```bash
git push -o merge_request.create \
         -o merge_request.target=main \
         -o merge_request.remove_source_branch \
         -o merge_request.title="ABC-1 Project Setup" \
         -o merge_request.description="$(cat .gitlab/merge_request_templates/feature.md)" \
         origin HEAD
```

**GitHub** — push normally, then open the PR via the `gh` CLI:

```bash
git push -u origin HEAD
gh pr create --title "ABC-1 Project Setup" --body "$(cat .github/PULL_REQUEST_TEMPLATE/feature.md)" --base main
```

- `target` / `--base` — the **base branch from section 1** (`main` or `N.M.x`), not always `main`.
- `title` — MR/PR-title form from the Naming table.
- `description` / `--body` — content of the chosen template.
- Work in progress → add `-o merge_request.draft` (GitLab) or `--draft` (GitHub).

### Step 4 — Verify

The host prints the MR/PR URL in the command output. Show it to the user and remind to walk through the template checklist.

## Do Not

- Start work directly on `main` / a release branch, or branch off a stale base
- Use a branch name outside `[a-z0-9-]+`
- Commit secrets, `.env.local`, credentials
- Commit before all tests and linters pass, or skip migration diff on schema changes
- Make work-in-progress commits — one commit when the feature is complete
- Put unrelated changes into one commit
- Delay the push after committing, or the MR/PR after the push
- Open an MR/PR without a rebase, without a template, or with a title not matching the branch
- Leave `Draft` in the title when the MR/PR is ready for review

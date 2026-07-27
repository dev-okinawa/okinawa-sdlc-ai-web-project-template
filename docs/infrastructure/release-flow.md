# Release Flow (Branching & Versioning)

How the repository is branched and versioned. Read this before creating a task branch — you must know **which branch to branch off from**.

---

## Model

We use **Release Flow** (release branching), not `development`/`staging`/`master` per-environment branches.

- **`main`** — the mainline. Always holds the latest changes; kept relatively stable. Most work happens here.
- **Release branches `N.M.x`** (e.g. `1.15.x`, `1.16.x`) — a frozen snapshot of the codebase prepared for a specific release, worked on independently so it can be stabilized for production without new code leaking in.
- **Tags `vN.M.P`** (e.g. `v1.9.13`) — the first tag on a release branch = went to prod; the last tag = end of that release's lifecycle.

## Forward-merge rule

A change made **in a release branch** must be propagated:

- merge it back into `main`, **and**
- merge it into every release branch with a **higher** version.

This is done manually (usually by whoever accepts the MR), because it often involves conflicts that need resolving.

## When is a release branch created?

By common sense — only when the codebase state needs to be frozen to prepare a release. In practice: when work on the next release's tasks begins.

## Long-lived branches

Working with many long-lived branches is bad (hard conflicts, merge mistakes). Aim for **no more than two active branches at a time** — prefer working in `main` and keeping it stable.

---

## Which branch do I branch from?

| Situation | Base branch | MR target |
|---|---|---|
| Regular feature / task for the next release | `main` | `main` |
| Fix / stabilization for a specific release | that release branch `N.M.x` | same `N.M.x` |

> Always confirm the base branch before starting — see the `git` skill.

References: [Release Flow — release branching](https://learn.microsoft.com/azure/devops/repos/git/git-branching-guidance), Azure Repos Git branching guidance.

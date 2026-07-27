---
description: Verification checklist — commands to run (linters, tests, migrations, docs) before committing a finished feature and opening an MR. Fill in with your stack's actual commands.
globs: ["src/**/*", "tests/**"]
---

# Verification

Run **before committing** — only the checks relevant to the change:

| When                      | Command                                        | Verifies                                                           |
|---------------------------|-------------------------------------------------|---------------------------------------------------------------------|
| Always                    | `{lint/format command}`                        | Style, static analysis                                              |
| Data model / schema changed | `{migration diff command}`                   | Migration exists for every mapping change                           |
| Behavior changed          | `{test command} --filter <TestClass>`          | Targeted tests for the affected module                              |
| Frontend behavior changed | `{frontend test command} <path-or-pattern>`    | Specs for the affected components/controllers                       |
| Docs-first pipeline       | `docs/specification/{module}/{submodule}/` updated | Spec matches the implemented behavior                               |

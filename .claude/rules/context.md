---
description: Context gathering strategy — Graphify-first discovery, docs/ as source of truth, Serena for symbols. Defines source priority, stop criteria, and anti-patterns for exploring the codebase.
---

# Context Gathering

Gather the minimum context needed to act. Precision over breadth.

## Source Priority

1. **Graphify** — orient first.
2. **`docs/`** — source of truth for behavior and contracts; always `Read` the real file.
3. **Serena MCP** — code symbols: `get_symbols_overview` → `find_symbol` → body only if needed.
4. **Grep / Glob** — last resort: exact strings, or graphify has no hit.

## Graphify

Knowledge graph of code + docs at `graphify-out/`.

- `graphify query "<question>"` — scoped subgraph for a codebase/architecture question
- `graphify explain "<concept>"` — a node and all its neighbors
- `graphify path "<A>" "<B>"` — dependency path between two symbols
- `graphify update .` — after modifying code files (AST-only, no API cost)

## Workflow

```text
task → graphify query|explain|path
     → document nodes: Read docs/<Source>
     → code nodes:     Serena find_symbol
     → stop when you can name the files/symbols to change
```

## Resolving Doc Paths

Graphify document nodes expose `Source` relative to `docs/` (no prefix):

| Graphify `Source`             | Read                               |
|-------------------------------|-------------------------------------|
| `specification/commerce/orders/orders.md` | `docs/specification/commerce/orders/orders.md` |
| empty (module/submodule keyword) | Glob `docs/specification/**/*{keyword}*` → Read |
| empty (keyword only)          | Glob `docs/**/*{keyword}*` → Read  |

## Stop Criteria

Context is sufficient when the owning module/submodule, the relevant doc, and the target
files/symbols are known. Further hops only add noise.

## Anti-patterns

- Repo-wide Grep or bulk file reads before graphify has oriented you
- Answering from graphify/doc snippets without `Read` of the actual file
- Loading an entire module or `docs/` tree into context
- Context7 for project internals

If graphify is skipped (known exact path/symbol), state why in one line.

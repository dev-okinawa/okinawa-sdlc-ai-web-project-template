---
description: Coding standards — strict typing, dependency injection, naming conventions, and code style constraints. Fill in with your stack's specific tools and versions.
globs: ["src/**/*"]
---

# Coding Standards

## Backend

- Strict typing enabled; run static analysis at the strictest practical level for the language.
- Constructor injection only; never instantiate services directly.
- Naming: classes `PascalCase`, methods/variables `camelCase`, constants `UPPER_SNAKE_CASE`.
- Tests: one class per action/scenario, based on the project's test-case base class; data providers return closures where the framework supports it.
- Fixtures/seeds: declare dependencies explicitly; references in `kebab-case`.

## Frontend

- Prefer composition-style APIs over legacy patterns (e.g. Composition API over Options API in Vue, hooks over class components in React).
- Naming: components/files `PascalCase`, props `camelCase`, events `kebab-case`, composables/hooks with a `use` prefix.
- Styling: utility-first classes in templates where available; scoped styles for component-specific overrides.

## Lightweight page controllers

- Always extend the project's shared base controller.
- Naming: files `snake_case`, identifiers `kebab-case`, targets/actions/values `camelCase`.
- Provide type declarations for targets and values where the tooling supports it.

## Code Style

- No Yoda comparisons.
- Alphabetical import/`use` statement ordering.
- Trailing commas in multi-line signatures and arrays.

# AI Project Docs

## Purpose

This repository contains implementation guides for application development across multiple frameworks.

The docs are written to be useful for both:

- developers making architectural decisions
- AI agents generating code that should follow the project's conventions

## Structure

Each framework should have its own top-level folder.

Current structure:

```text
angular/
```

Planned structure example:

```text
angular/
react/
nextjs/
nestjs/
vue/
```

Each framework folder should contain the same documentation split:

- `required/always-on`
- `optional/by-strategy`
- `optional/by-app-type`

Each framework folder should also contain:

- a local `README.md` as the entry point

Framework-specific prompt files should follow the same shared prompt structure.

Example:

```text
angular/
  prompts/
react/
  prompts/
```

Prompt files should:

- be numbered in execution order
- use kebab-case names
- represent real development flow

Example:

```text
01-init-orders-feature.md
02-build-orders-page.md
03-add-order-delete-flow.md
```

Each prompt should contain exactly these sections:

- `Task`
- `Guides`
- `Requirements`

Prompt writing rules:

- keep each prompt focused on one meaningful step
- reference only the guides needed for that task
- use relative guide paths
- define concrete implementation requirements

## Authoring Rules

When adding docs for a new framework, keep the same high-level structure:

1. Create a top-level framework folder.
2. Add a framework-specific `README.md`.
3. Use the shared split:
   `required/always-on`, `optional/by-strategy`, and `optional/by-app-type`.
4. Add framework-specific prompt files under that framework's `prompts/` folder using the shared structure in this root README.
5. Write docs so developers and AI agents can both use them as implementation rules.
6. Prefer practical conventions over broad theory.

## Goal

The goal of this repository is not to document every possible pattern.

The goal is to define clear, production-usable conventions that can guide real project implementation consistently across frameworks.

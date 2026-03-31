# React Directory Structure Guide

## Purpose

This guide defines a scalable, production-grade directory structure for React applications.

The goal is to keep feature ownership obvious, keep growth manageable, and give AI agents predictable file placement rules.

## Core Principle

Organize by feature first, not by technical layer across the whole app.

React applications often become hard to maintain when they collect global folders such as:

- `components/`
- `hooks/`
- `services/`
- `api/`

for all features at once.

Those structures work briefly, then make ownership blurry. A feature-first structure keeps routing, state, transport, forms, and UI close to the feature that owns them.

## Recommended Top-Level Structure

```text
src/
  app/
    providers/
    routes/
    layout/
  core/
    auth/
    config/
    lib/
  shared/
    ui/
    lib/
  features/
    orders/
    profile/
```

### `app/`

Use `app/` for application composition.

Typical responsibilities:

- root providers
- router setup
- app shell layout
- application bootstrap wiring

### `core/`

Use `core/` for app-wide infrastructure and global concerns.

Examples:

- auth
- environment config
- analytics setup
- shared HTTP client setup
- app-wide providers that are not feature-owned

### `shared/`

Use `shared/` for cross-feature code that is intentionally generic.

Recommended structure:

```text
shared/
  ui/
  lib/
```

- `shared/ui` for presentation-only reusable UI primitives
- `shared/lib` for small reusable utilities with no feature ownership

### `features/`

Use `features/` for application behavior.

Each feature should own:

- route entry pages
- feature state
- transport code
- feature-local components
- feature-specific types
- form logic when applicable

## Recommended Feature Structure

```text
features/
  orders/
    api/
      orders-api.ts
    components/
      order-list.tsx
      order-list-item.tsx
      empty-orders-state.tsx
    hooks/
      use-orders.ts
    pages/
      orders-page.tsx
    types/
      order.dto.ts
      order-list-item.vm.ts
```

For larger features, expand carefully:

```text
features/
  orders/
    api/
    components/
    forms/
    hooks/
    pages/
    stores/
    types/
    lib/
```

## Folder Responsibilities

### `pages/`

Use `pages/` for route-level containers.

### `components/`

Use `components/` for feature-local UI. Most components here should be presentation-oriented.

### `hooks/`

Use `hooks/` for feature orchestration when the app uses the feature-hooks strategy.

### `api/`

Use `api/` for transport-layer code.

### `types/`

Use `types/` for DTOs, form value types, and view-model types when useful.

## Example: Orders Feature

```text
features/
  orders/
    api/
      orders-api.ts
    components/
      order-list.tsx
      order-list-item.tsx
      order-filters.tsx
    hooks/
      use-orders.ts
    pages/
      orders-page.tsx
    types/
      order.dto.ts
      orders-query.ts
```

Flow:

```text
orders-page -> useOrders -> orders-api
order-list -> onDelete callback -> orders-page/useOrders
```

## Anti-Patterns

Avoid:

- app-wide `services/` folders for feature logic
- app-wide `hooks/` folders for feature-specific hooks
- global `types/` folders for every interface in the app
- putting route pages directly in `app/` when they belong to a feature

## AI Agent Rules

When creating or updating React code, AI agents must follow these rules:

1. Default to `features/<feature-name>` for feature-owned code.
2. Put route entry components in `pages/`.
3. Put feature-local presentation components in `components/`.
4. Put transport code in `api/`.
5. Put feature orchestration in `hooks/` or the chosen state-strategy folder.
6. Do not add repository or mapper layers by default.

## Default Standard

If you are unsure where a file belongs, use this default:

- route entry -> `pages/`
- feature UI -> `components/`
- transport -> `api/`
- feature orchestration -> `hooks/`
- feature contracts -> `types/`

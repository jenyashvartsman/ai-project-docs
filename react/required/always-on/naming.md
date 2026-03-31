# React Naming Guide

## Purpose

This guide defines naming conventions for React applications so developers and AI agents can create predictable file and symbol names without guesswork.

## Core Principle

Name by role and UI meaning, not by personal preference.

Prefer names that make ownership and responsibility obvious.

## File Names

Use kebab-case for file names.

Examples:

- `orders-page.tsx`
- `order-list.tsx`
- `use-orders.ts`
- `orders-api.ts`
- `order-form-values.ts`
- `orders-store.ts`

## Components

Use PascalCase for component names.

Examples:

- `OrdersPage`
- `OrderList`
- `OrderListItem`
- `ProfileSettingsForm`

Name components by UI purpose, not by architecture labels.

Avoid names like:

- `OrdersContainer`
- `OrderListPresenter`

## Hooks

Hooks must start with `use`.

Examples:

- `useOrders`
- `useOrderFilters`
- `useProfileForm`

## API Modules

Prefer explicit feature-based names.

Examples:

- `orders-api.ts`
- `auth-api.ts`

## Types

Use clear role-based type names.

Examples:

- `OrderDto`
- `DeleteOrderRequest`
- `OrderFormValues`
- `OrderListItemVm`

## Booleans

Boolean names should read clearly as true or false.

Prefer:

- `isLoading`
- `isDeleting`
- `hasOrders`
- `canDeleteOrder`
- `shouldShowFilters`

## Route Paths

Use lowercase kebab-case for URLs.

Examples:

- `/orders`
- `/orders/:orderId`
- `/profile-settings`
- `/sign-in`

## Props And Callbacks

Name props after what they represent.

For callback props, prefer event-style names:

- `onDelete`
- `onSelect`
- `onSubmit`
- `onClose`

## Tests

Keep test files aligned with the source file.

Examples:

- `orders-page.test.tsx`
- `use-orders.test.ts`
- `orders-api.test.ts`

## AI Agent Rules

When creating or updating React code, AI agents must follow these rules:

1. Use kebab-case for files and PascalCase for components and types.
2. Name hooks with the `use` prefix and reserve that prefix for real hooks.
3. Name components by UI purpose, not by architecture labels.
4. Use clear boolean naming such as `is`, `has`, `can`, or `should`.
5. Use route names that are user-facing and readable.

## Default Standard

If you are unsure what to name something, use:

- feature name + artifact role for files
- UI purpose for components
- action verbs for callbacks and operations

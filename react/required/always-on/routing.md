# React Routing Guide

## Purpose

This guide defines a scalable routing strategy for React applications.

It assumes React Router by default, but the ownership rules still apply if the project uses another router.

## Core Principle

Keep route ownership with the feature, not with a central routing file full of feature details.

## Recommended Structure

```text
src/
  app/
    routes/
      app-router.tsx
  features/
    orders/
      pages/
        orders-page.tsx
        order-details-page.tsx
      routes/
        orders-routes.tsx
```

## App-Level Router

Use the app router for:

- route composition
- shell layout composition
- top-level lazy loading
- app-level protected route wrappers

## Feature Route Modules

Each feature should own its route definitions when the feature has more than one route or route-specific concerns.

Example:

```tsx
import { lazy } from 'react';
import type { RouteObject } from 'react-router-dom';

const OrdersPage = lazy(() => import('../pages/orders-page'));
const OrderDetailsPage = lazy(() => import('../pages/order-details-page'));

export const ordersRoutes: RouteObject[] = [
  {
    path: 'orders',
    children: [
      { index: true, element: <OrdersPage /> },
      { path: ':orderId', element: <OrderDetailsPage /> },
    ],
  },
];
```

## Route Protection

Protect routes at the router layer.

## URL Naming Rules

- use lowercase kebab-case
- use nouns for resources
- keep URLs user-facing

## Anti-Patterns

Avoid:

- one giant root router containing all feature details
- route pages stored outside the owning feature
- deep nesting without real URL or layout meaning

## AI Agent Rules

When creating or updating React routing, AI agents must follow these rules:

1. Keep route entry pages in `pages/`.
2. Let features own route modules when routing is more than trivial.
3. Put route protection at the router layer.
4. Lazy load route-level pages by default when the router setup supports it.

## Default Standard

If you are unsure what to do, use this default:

- app router composes
- feature owns route module
- page owns params and orchestration

# React Feature Hooks State Guide

## Purpose

This guide defines the default state strategy for most React features.

The idea is to keep feature orchestration inside a custom hook before introducing a dedicated store.

## Core Principle

Use a feature hook when one feature or route can own the full flow clearly.

This strategy is usually the best starting point for:

- route-level features
- CRUD screens
- forms with modest workflow complexity
- dashboard pages with one dominant data flow

When route params or search params are part of feature state, the feature hook should own them.

## Architecture

```text
page/container -> feature hook -> api
presentation component -> callback props -> container
```

## Use When

Use feature hooks when:

- the feature is mostly one page flow
- state is local to one route or feature area
- one hook can organize loading, mutation, and derived state clearly
- a dedicated shared store would add more ceremony than value

Do not use a feature hook as a disguised app-wide state container.

## Responsibilities

A feature hook may:

- load data
- expose loading and error state
- expose mutation actions
- shape data for the page
- hold small feature-local UI state
- prepare derived values for presentation components
- coordinate route params and search params when the URL is part of the feature contract

A feature hook should not:

- become a cross-feature singleton
- coordinate several unrelated business areas
- hide too much behavior behind one huge return object

## Recommended Structure

```text
features/
  orders/
    api/
      orders-api.ts
    hooks/
      use-orders.ts
    pages/
      orders-page.tsx
    components/
      order-list.tsx
      order-filters.tsx
    types/
      order.dto.ts
      orders-query.ts
```

## URL State Rule

Use route params and search params as part of feature state when the user should be able to:

- refresh and keep the same view
- share or bookmark the current state
- use browser back and forward for feature navigation
- land directly on a selected item, filter set, tab, sort, search term, or page

Typical URL-backed state:

- route params for entity identity and nested context
- search params for filters, sorting, search, pagination, tabs, and view mode

Rules:

- the feature hook should own `useParams`, `useSearchParams`, or equivalent router state when URL state is part of the feature contract
- page components should call the hook and render the returned state rather than re-parsing URL state themselves
- presentation components must not read router state directly
- keep parsing and normalization in one place so invalid params do not leak into the UI
- avoid keeping a conflicting second source of truth for URL-backed state

## Deep Example

```ts
import { useEffect, useMemo, useState } from 'react';
import { useParams, useSearchParams } from 'react-router-dom';
import { deleteOrderRequest, getOrders } from '../api/orders-api';
import type { OrderDto } from '../types/order.dto';

type OrdersFilter = 'all' | 'open' | 'closed';

export function useOrders() {
  const { orderId } = useParams<{ orderId?: string }>();
  const [searchParams, setSearchParams] = useSearchParams();
  const [orders, setOrders] = useState<OrderDto[]>([]);
  const [isLoading, setIsLoading] = useState(false);
  const [isDeletingId, setIsDeletingId] = useState<string | null>(null);
  const [error, setError] = useState<string | null>(null);

  const activeFilter = (searchParams.get('status') as OrdersFilter | null) ?? 'all';
  const selectedOrderId = orderId ?? null;

  useEffect(() => {
    void loadOrders();
  }, []);

  const visibleOrders = useMemo(() => {
    if (activeFilter === 'all') {
      return orders;
    }

    return orders.filter((order) => order.status === activeFilter);
  }, [activeFilter, orders]);

  async function loadOrders() {
    setIsLoading(true);
    setError(null);

    try {
      const nextOrders = await getOrders({
        status: activeFilter === 'all' ? undefined : activeFilter,
      });
      setOrders(nextOrders);
    } catch {
      setError('Unable to load orders.');
    } finally {
      setIsLoading(false);
    }
  }

  async function deleteOrder(orderId: string) {
    setIsDeletingId(orderId);
    setError(null);

    try {
      await deleteOrderRequest(orderId);
      setOrders((current) => current.filter((order) => order.id !== orderId));
    } catch {
      setError('Unable to delete order.');
    } finally {
      setIsDeletingId(null);
    }
  }

  function setActiveFilter(nextFilter: OrdersFilter) {
    setSearchParams((current) => {
      const next = new URLSearchParams(current);

      if (nextFilter === 'all') {
        next.delete('status');
      } else {
        next.set('status', nextFilter);
      }

      return next;
    });
  }

  return {
    orders: visibleOrders,
    activeFilter,
    selectedOrderId,
    isLoading,
    isDeletingId,
    error,
    setActiveFilter,
    loadOrders,
    deleteOrder,
  };
}
```

## Container Usage Example

```tsx
import { OrderFilters } from '../components/order-filters';
import { OrderList } from '../components/order-list';
import { useOrders } from '../hooks/use-orders';

export function OrdersPage() {
  const {
    orders,
    activeFilter,
    isLoading,
    isDeletingId,
    error,
    setActiveFilter,
    loadOrders,
    deleteOrder,
  } = useOrders();

  if (isLoading) {
    return <p>Loading orders...</p>;
  }

  return (
    <>
      {error && <p>{error}</p>}

      <OrderFilters value={activeFilter} onChange={setActiveFilter} />
      <OrderList
        orders={orders}
        deletingOrderId={isDeletingId}
        onRetry={loadOrders}
        onDelete={deleteOrder}
      />
    </>
  );
}
```

## What Belongs In The Hook Versus The Page

Put logic in the hook when it is:

- feature orchestration
- reusable across one feature
- derived from the feature's state

Keep logic in the page when it is:

- route-specific
- layout-specific
- only meaningful for one page composition

Exception:
When route or search params are part of feature state, let the feature hook own them instead of duplicating that logic in the page.

## When To Split One Hook Into Several

Split when:

- one hook is managing unrelated workflows
- a form flow and a table flow evolve independently
- the return API becomes too wide to understand quickly

Good examples:

- `useOrdersList`
- `useOrderDelete`
- `useOrderFilters`

Do not split too early. One well-shaped hook is usually better than many tiny hooks with unclear ownership.

## Anti-Patterns

Avoid:

- very large hooks that coordinate several unrelated flows
- feature hooks imported across unrelated features
- using one hook as a de facto global state container
- returning raw backend shape when the UI repeatedly needs a different structure
- moving route concerns into presentation components

## AI Agent Rules

When creating or updating React state, AI agents must follow these rules:

1. Default to feature hooks before introducing a store.
2. Keep hooks focused on one feature or route flow.
3. Return view-ready state and explicit actions.
4. Let the hook own route params and search params when they are part of feature state.
5. Do not move simple feature state into Zustand or Redux too early.

## Default Standard

If you are unsure what state strategy to start with, start here.

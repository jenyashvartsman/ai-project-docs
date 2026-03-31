# React TanStack Query State Guide

## Purpose

This guide defines how to use TanStack Query for server state in React applications.

React apps often need an explicit distinction between:

- server state
- client UI state

TanStack Query is the preferred strategy when the feature is dominated by remote data loading, caching, invalidation, and mutation status.

## Core Principle

Use TanStack Query for server state. Keep local UI state in the page, component, feature hook, or a small client-state store.

When route params or search params shape the remote data view, the feature query hook should own them as part of the feature boundary.

Do not treat TanStack Query as a replacement for all client state.

## Architecture

```text
page/container -> useQuery/useMutation or feature query hook -> api
presentation component -> callback props -> container
```

## Use When

Use TanStack Query when:

- the feature is heavily driven by remote data
- caching and refetch rules matter
- invalidation after mutations matters
- loading, stale, success, and error states should follow one standard
- route/search params directly shape query keys or request params

## Recommended Structure

```text
features/
  orders/
    api/
      orders-api.ts
    hooks/
      use-orders-query.ts
      use-delete-order-mutation.ts
    pages/
      orders-page.tsx
    components/
      order-list.tsx
    types/
      order.dto.ts
```

## Query Key Rules

Keep query keys explicit and stable.

Example:

```ts
export const ordersKeys = {
  all: ['orders'] as const,
  list: (filter: string) => ['orders', 'list', filter] as const,
  detail: (orderId: string) => ['orders', 'detail', orderId] as const,
};
```

## Deep Example

```ts
import { useMemo, useState } from 'react';
import { useMutation, useQuery, useQueryClient } from '@tanstack/react-query';
import { deleteOrderRequest, getOrders } from '../api/orders-api';

type OrdersFilter = 'all' | 'open' | 'closed';

const ordersKeys = {
  all: ['orders'] as const,
  list: (filter: OrdersFilter) => ['orders', 'list', filter] as const,
};

export function useOrdersQuery() {
  const [searchParams, setSearchParams] = useSearchParams();
  const queryClient = useQueryClient();
  const activeFilter = (searchParams.get('status') as OrdersFilter | null) ?? 'all';

  const ordersQuery = useQuery({
    queryKey: ordersKeys.list(activeFilter),
    queryFn: () => getOrders({ status: activeFilter === 'all' ? undefined : activeFilter }),
  });

  const deleteOrderMutation = useMutation({
    mutationFn: deleteOrderRequest,
    onSuccess: async () => {
      await queryClient.invalidateQueries({ queryKey: ordersKeys.all });
    },
  });

  const orders = ordersQuery.data ?? [];
  const isLoading = ordersQuery.isPending;
  const error = ordersQuery.isError ? 'Unable to load orders.' : null;

  const deletingOrderId = deleteOrderMutation.variables ?? null;

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
    orders,
    activeFilter,
    isLoading,
    deletingOrderId,
    error,
    setActiveFilter,
    retry: ordersQuery.refetch,
    deleteOrder: deleteOrderMutation.mutateAsync,
  };
}
```

## Container Usage Example

```tsx
import { OrderFilters } from '../components/order-filters';
import { OrderList } from '../components/order-list';
import { useOrdersQuery } from '../hooks/use-orders-query';

export function OrdersPage() {
  const {
    orders,
    activeFilter,
    isLoading,
    deletingOrderId,
    error,
    setActiveFilter,
    retry,
    deleteOrder,
  } = useOrdersQuery();

  if (isLoading) {
    return <p>Loading orders...</p>;
  }

  return (
    <>
      {error && (
        <div>
          <p>{error}</p>
          <button type="button" onClick={() => void retry()}>
            Retry
          </button>
        </div>
      )}

      <OrderFilters value={activeFilter} onChange={setActiveFilter} />
      <OrderList
        orders={orders}
        deletingOrderId={deletingOrderId}
        onDelete={deleteOrder}
      />
    </>
  );
}
```

## When To Wrap TanStack Query In Feature Hooks

Prefer feature-level query hooks such as:

- `useOrdersQuery`
- `useOrderDetailsQuery`
- `useDeleteOrderMutation`

This keeps query keys, invalidation rules, and error translation owned by the feature instead of scattered across pages.

It also keeps URL-backed query state owned by the query hook when the URL shapes the query contract.

## What TanStack Query Should Not Own

Do not use TanStack Query for:

- dialog open state
- selected row ids that are purely UI concerns
- local form draft state
- complex client-only workflows with many transitions

Those belong in local component state, a feature hook, Zustand, or Redux Toolkit depending on complexity.

## Anti-Patterns

Avoid:

- mixing raw query client calls across many components
- putting query keys inline everywhere
- moving client-only UI state into query caches
- using TanStack Query as a substitute for a client-state architecture

## AI Agent Rules

When creating or updating React server-state code, AI agents must follow these rules:

1. Prefer TanStack Query for server-state-heavy features when the project uses it.
2. Keep query keys centralized and explicit.
3. Wrap query and mutation logic in feature hooks.
4. Keep local UI state outside query caches.
5. Translate query errors into stable UI messages at the feature boundary.
6. Let the feature query hook own route/search params when they determine query keys or request params.

## Default Standard

If a React feature is mostly about remote data loading, caching, refetching, and invalidation, use TanStack Query before introducing a client-state store.

# React Zustand State Guide

## Purpose

Use Zustand when a feature needs a dedicated store but Redux Toolkit would be too heavy.

Zustand works best for medium-complexity client state where several components in one feature need to coordinate through a shared state boundary.

## Core Principle

Use Zustand for shared client state, not as the default home for all state in the app.

## Architecture

```text
page/container -> zustand store -> api
```

When route params or search params are part of feature state, the Zustand state layer should own them.

## Use When

Use Zustand when:

- multiple components in the feature share client state
- a custom hook is becoming too crowded
- several actions update the same feature state
- a dedicated store boundary improves clarity
- the feature needs one shared owner for URL-backed client state

Do not use Zustand just because multiple siblings need one boolean. Lift that state first and keep it simple.

## Recommended Structure

```text
features/
  orders/
    api/
      orders-api.ts
    pages/
      orders-page.tsx
    stores/
      orders-store.ts
      orders-selectors.ts
    components/
      order-list.tsx
      order-toolbar.tsx
    types/
      order.dto.ts
```

## Store Shape

Keep stores feature-owned unless the concern is truly global.

Example store shape:

```ts
type OrdersState = {
  orders: OrderDto[];
  activeFilter: OrdersFilter;
  selectedOrderId: string | null;
  isLoading: boolean;
  isDeletingId: string | null;
  error: string | null;
  setActiveFilter: (filter: OrdersFilter) => void;
  selectOrder: (orderId: string | null) => void;
  loadOrders: () => Promise<void>;
  deleteOrder: (orderId: string) => Promise<void>;
  initializeFromUrl: (input: {
    orderId: string | null;
    status: string | null;
  }) => void;
};
```

## URL State Rule

Use route params and search params as part of feature state when the URL should describe the current feature view.

Rules:

- the store should own normalized URL-backed state when several components depend on it
- a small feature hook on top of Zustand should read router state and initialize the store
- page components should not re-own URL parsing if the Zustand state layer already treats it as feature state
- presentation components must not read router state directly

## Deep Example

```ts
import { create } from 'zustand';
import { deleteOrderRequest, getOrders } from '../api/orders-api';
import type { OrderDto } from '../types/order.dto';

type OrdersFilter = 'all' | 'open' | 'closed';

type OrdersState = {
  orders: OrderDto[];
  activeFilter: OrdersFilter;
  selectedOrderId: string | null;
  isLoading: boolean;
  isDeletingId: string | null;
  error: string | null;
  setActiveFilter: (filter: OrdersFilter) => void;
  selectOrder: (orderId: string | null) => void;
  initializeFromUrl: (input: { orderId: string | null; status: string | null }) => void;
  loadOrders: () => Promise<void>;
  deleteOrder: (orderId: string) => Promise<void>;
};

export const useOrdersStore = create<OrdersState>((set, get) => ({
  orders: [],
  activeFilter: 'all',
  selectedOrderId: null,
  isLoading: false,
  isDeletingId: null,
  error: null,
  initializeFromUrl({ orderId, status }) {
    set({
      selectedOrderId: orderId,
      activeFilter: status === 'open' || status === 'closed' ? status : 'all',
    });
  },
  setActiveFilter(filter) {
    set({ activeFilter: filter });
  },
  selectOrder(orderId) {
    set({ selectedOrderId: orderId });
  },
  async loadOrders() {
    set({ isLoading: true, error: null });

    try {
      const orders = await getOrders();
      set({ orders });
    } catch {
      set({ error: 'Unable to load orders.' });
    } finally {
      set({ isLoading: false });
    }
  },
  async deleteOrder(orderId) {
    set({ isDeletingId: orderId, error: null });

    try {
      await deleteOrderRequest(orderId);

      const currentOrders = get().orders;
      const nextOrders = currentOrders.filter((order) => order.id !== orderId);

      set({
        orders: nextOrders,
        selectedOrderId: get().selectedOrderId === orderId ? null : get().selectedOrderId,
      });
    } catch {
      set({ error: 'Unable to delete order.' });
    } finally {
      set({ isDeletingId: null });
    }
  },
}));
```

## Selector Layer

Prefer small selectors so components subscribe only to what they need.

Example:

```ts
export const useVisibleOrders = () =>
  useOrdersStore((state) => {
    if (state.activeFilter === 'all') {
      return state.orders;
    }

    return state.orders.filter((order) => order.status === state.activeFilter);
  });
```

## Feature Hook On Top Of Zustand

When URL state belongs to the feature contract, prefer a small hook on top of the store to own router reads.

```tsx
import { useEffect } from 'react';
import { useParams, useSearchParams } from 'react-router-dom';
import { useOrdersStore } from '../stores/orders-store';
import { useVisibleOrders } from '../stores/orders-selectors';

export function useOrdersPageState() {
  const { orderId } = useParams<{ orderId?: string }>();
  const [searchParams] = useSearchParams();

  const visibleOrders = useVisibleOrders();
  const isLoading = useOrdersStore((state) => state.isLoading);
  const error = useOrdersStore((state) => state.error);
  const activeFilter = useOrdersStore((state) => state.activeFilter);
  const initializeFromUrl = useOrdersStore((state) => state.initializeFromUrl);
  const setActiveFilter = useOrdersStore((state) => state.setActiveFilter);
  const loadOrders = useOrdersStore((state) => state.loadOrders);
  const deleteOrder = useOrdersStore((state) => state.deleteOrder);

  useEffect(() => {
    initializeFromUrl({
      orderId: orderId ?? null,
      status: searchParams.get('status'),
    });
  }, [initializeFromUrl, orderId, searchParams]);

  useEffect(() => {
    void loadOrders();
  }, [loadOrders]);

  return {
    orders: visibleOrders,
    isLoading,
    error,
    activeFilter,
    setActiveFilter,
    deleteOrder,
  };
}
```

## Container Usage Example

```tsx
import { useOrdersPageState } from '../hooks/use-orders-page-state';

export function OrdersPage() {
  const { orders, isLoading, error, activeFilter, setActiveFilter, deleteOrder } =
    useOrdersPageState();

  if (isLoading) {
    return <p>Loading orders...</p>;
  }

  return (
    <>
      {error && <p>{error}</p>}
      <OrderFilters value={activeFilter} onChange={setActiveFilter} />
      <OrderList orders={orders} onDelete={deleteOrder} />
    </>
  );
}
```

## What Should Not Go Into Zustand By Default

Do not move these into Zustand automatically:

- one-off dialog open state
- local form field state
- tiny page-only toggles
- server caching concerns if the project uses TanStack Query

## Anti-Patterns

Avoid:

- a single store for the entire app
- mixing unrelated feature state in one store
- moving trivial local component state into Zustand
- treating Zustand as a replacement for route composition or API structure

## AI Agent Rules

When creating or updating Zustand state, AI agents must follow these rules:

1. Use Zustand for medium-complexity shared feature state.
2. Keep stores feature-owned unless the concern is truly global.
3. Keep actions explicit and readable.
4. Use selectors so components subscribe narrowly.
5. Do not create one giant app store by default.
6. Let the Zustand state layer own URL-backed feature state when the URL is part of the feature contract.

## Default Standard

If feature hooks are getting crowded but Redux still feels too heavy, use Zustand.

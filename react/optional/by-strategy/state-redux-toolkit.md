# React Redux Toolkit State Guide

## Purpose

Use Redux Toolkit when the feature needs formal, event-driven state management.

This is the heavyweight option in this React documentation set. It should exist for features that truly benefit from explicit reducers, actions, selectors, and async workflows.

## Core Principle

Use Redux Toolkit when the complexity is in state transitions, not just in rendering.

## Architecture

```text
page/container -> hooks/selectors -> redux store -> thunks or listeners -> api
```

When route params or search params are part of feature state, Redux state should own the normalized URL-backed values.

## Use When

Use Redux Toolkit when:

- the feature has many transitions and edge cases
- several parts of the UI react to the same events
- action history and explicit event modeling improve maintainability
- async flows interact in ways that a simple hook or lightweight store would obscure
- router-driven state changes need to participate in reducer-driven workflows

## Recommended Structure

```text
features/
  orders/
    api/
      orders-api.ts
    state/
      orders-slice.ts
      orders-selectors.ts
      orders-thunks.ts
      orders-types.ts
    pages/
      orders-page.tsx
    components/
      order-list.tsx
      order-toolbar.tsx
```

## Slice State Example

```ts
export type OrdersState = {
  items: OrderDto[];
  selectedOrderId: string | null;
  activeFilter: OrdersFilter;
  isLoading: boolean;
  isDeletingId: string | null;
  error: string | null;
};
```

## URL State Rule

Use route params and search params as part of feature state when URL changes should participate in the Redux workflow.

Rules:

- Redux state should store the normalized route/search param values when they matter to the feature
- route parsing should happen in a state-layer entry point such as a feature hook that dispatches Redux actions
- page components should not own a second copy of URL-backed state
- presentation components must not read router state directly

## Deep Example

```ts
import { createAsyncThunk, createSlice, PayloadAction } from '@reduxjs/toolkit';
import { deleteOrderRequest, getOrders } from '../api/orders-api';
import type { OrderDto } from '../types/order.dto';

type OrdersFilter = 'all' | 'open' | 'closed';

type OrdersState = {
  items: OrderDto[];
  selectedOrderId: string | null;
  activeFilter: OrdersFilter;
  isLoading: boolean;
  isDeletingId: string | null;
  error: string | null;
};

const initialState: OrdersState = {
  items: [],
  selectedOrderId: null,
  activeFilter: 'all',
  isLoading: false,
  isDeletingId: null,
  error: null,
};

export const loadOrders = createAsyncThunk('orders/loadOrders', async () => {
  return getOrders();
});

export const deleteOrder = createAsyncThunk(
  'orders/deleteOrder',
  async (orderId: string) => {
    await deleteOrderRequest(orderId);
    return orderId;
  }
);

const ordersSlice = createSlice({
  name: 'orders',
  initialState,
  reducers: {
    initializeFromUrl(
      state,
      action: PayloadAction<{ orderId: string | null; status: string | null }>
    ) {
      state.selectedOrderId = action.payload.orderId;
      state.activeFilter =
        action.payload.status === 'open' || action.payload.status === 'closed'
          ? action.payload.status
          : 'all';
    },
    setActiveFilter(state, action: PayloadAction<OrdersFilter>) {
      state.activeFilter = action.payload;
    },
    selectOrder(state, action: PayloadAction<string | null>) {
      state.selectedOrderId = action.payload;
    },
  },
  extraReducers(builder) {
    builder
      .addCase(loadOrders.pending, (state) => {
        state.isLoading = true;
        state.error = null;
      })
      .addCase(loadOrders.fulfilled, (state, action) => {
        state.isLoading = false;
        state.items = action.payload;
      })
      .addCase(loadOrders.rejected, (state) => {
        state.isLoading = false;
        state.error = 'Unable to load orders.';
      })
      .addCase(deleteOrder.pending, (state, action) => {
        state.isDeletingId = action.meta.arg;
        state.error = null;
      })
      .addCase(deleteOrder.fulfilled, (state, action) => {
        state.isDeletingId = null;
        state.items = state.items.filter((order) => order.id !== action.payload);

        if (state.selectedOrderId === action.payload) {
          state.selectedOrderId = null;
        }
      })
      .addCase(deleteOrder.rejected, (state) => {
        state.isDeletingId = null;
        state.error = 'Unable to delete order.';
      });
  },
});

export const { initializeFromUrl, setActiveFilter, selectOrder } = ordersSlice.actions;
export const ordersReducer = ordersSlice.reducer;
```

## Selector Example

```ts
import type { RootState } from '../../../app/store';

export const selectOrdersState = (state: RootState) => state.orders;
export const selectOrders = (state: RootState) => selectOrdersState(state).items;
export const selectOrdersError = (state: RootState) => selectOrdersState(state).error;

export const selectVisibleOrders = (state: RootState) => {
  const { activeFilter, items } = selectOrdersState(state);

  if (activeFilter === 'all') {
    return items;
  }

  return items.filter((order) => order.status === activeFilter);
};
```

## Feature Hook On Top Of Redux

When URL state belongs to the feature contract, prefer a feature hook that owns router reads and dispatches the initialization actions.

```tsx
import { useEffect } from 'react';
import { useParams, useSearchParams } from 'react-router-dom';
import { useAppDispatch, useAppSelector } from '../../../app/store-hooks';
import {
  deleteOrder,
  initializeFromUrl,
  loadOrders,
  setActiveFilter,
} from '../state/orders-slice';
import {
  selectOrdersError,
  selectVisibleOrders,
} from '../state/orders-selectors';
import type { OrdersFilter } from '../state/orders-types';

export function useOrdersPageState() {
  const { orderId } = useParams<{ orderId?: string }>();
  const [searchParams] = useSearchParams();
  const dispatch = useAppDispatch();
  const orders = useAppSelector(selectVisibleOrders);
  const error = useAppSelector(selectOrdersError);

  useEffect(() => {
    dispatch(
      initializeFromUrl({
        orderId: orderId ?? null,
        status: searchParams.get('status'),
      })
    );
  }, [dispatch, orderId, searchParams]);

  useEffect(() => {
    void dispatch(loadOrders());
  }, [dispatch]);

  return {
    orders,
    error,
    setActiveFilter: (filter: OrdersFilter) => dispatch(setActiveFilter(filter)),
    deleteOrder: (id: string) => void dispatch(deleteOrder(id)),
  };
}
```

## Container Usage Example

```tsx
import { useOrdersPageState } from '../hooks/use-orders-page-state';

export function OrdersPage() {
  const { orders, error, setActiveFilter, deleteOrder } = useOrdersPageState();

  return (
    <>
      {error && <p>{error}</p>}
      <OrderList orders={orders} onDelete={deleteOrder} />
      <OrderFilters onChange={setActiveFilter} />
    </>
  );
}
```

## What Redux Toolkit Is Good At

Redux Toolkit pays off when:

- the feature has meaningful event semantics
- transitions must stay explicit
- many components coordinate through shared state
- domain complexity is higher than UI complexity alone

## Anti-Patterns

Avoid:

- introducing Redux for one loading flag and one list
- putting transport code in components
- keeping selectors implicit inside component files only
- using Redux Toolkit where feature hooks or Zustand would be clearer

## AI Agent Rules

When creating or updating Redux Toolkit state, AI agents must follow these rules:

1. Reach for Redux Toolkit only when complexity justifies it.
2. Keep slices feature-owned.
3. Keep selectors and async flows explicit.
4. Keep components consuming selectors and dispatch helpers, not transport code.
5. Do not use Redux Toolkit for simple local state.
6. Let Redux own normalized URL-backed feature state when route/search params are part of the feature contract.

## Default Standard

Use Redux Toolkit only for the most complex domains in the app.

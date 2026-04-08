# React Feature Guide

## Purpose

This guide defines the default feature architecture for React applications.

The most important rule is the split between container components that access state and presentation components that stay prop-driven.

## Core Split

### Containers

Containers own orchestration.

They may:

- read route params
- call feature hooks or stores
- call mutation actions
- prepare view-ready props
- coordinate loading, empty, and error states

### Presentation Components

Presentation components render UI and emit events.

They should:

- receive props
- expose callback props
- stay unaware of routing, transport, and feature orchestration

They should not:

- call APIs
- own feature fetch flows
- import stores or feature hooks unless the component is explicitly a container

## Default Flow

```text
page/container -> feature hook or store -> api
presentation component -> callback props -> container
```

## Example

```tsx
export function OrdersPage() {
  const {
    orders,
    isLoading,
    error,
    deleteOrder,
  } = useOrders();

  if (isLoading) {
    return <OrdersLoadingState />;
  }

  if (error) {
    return <OrdersErrorState message={error} />;
  }

  return <OrderList orders={orders} onDelete={deleteOrder} />;
}
```

```tsx
type OrderListProps = {
  orders: OrderDto[];
  onDelete: (orderId: string) => void;
};

export function OrderList({ orders, onDelete }: OrderListProps) {
  return (
    <ul>
      {orders.map((order) => (
        <li key={order.id}>
          <span>{order.name}</span>
          <button type="button" onClick={() => onDelete(order.id)}>
            Delete
          </button>
        </li>
      ))}
    </ul>
  );
}
```

## View-Model Preparation

Containers should prepare data before passing it into presentational components when the UI would otherwise become noisy or repetitive.

## Local UI State

Presentation components may own small local UI state when it is purely view state.

Examples:

- expanded accordion row
- open tooltip state
- local tab selection

## Shared Components

Shared UI should stay:

- prop-driven
- reusable
- app-domain agnostic

## Anti-Patterns

Avoid:

- route pages with huge presentation trees inline
- presentation components importing feature hooks
- feature hooks hidden inside reusable UI components

## AI Agent Rules

When creating or updating React features, AI agents must follow these rules:

1. Keep route pages and stateful wrappers as containers.
2. Keep presentation components prop-driven.
3. Do not import APIs, stores, or feature hooks into presentation components.
4. Prepare view-ready data before it reaches lower-level UI.
5. Keep shared UI presentation-only.

## Default Standard

If you are unsure what to do, use this default:

- page owns orchestration
- hook or store owns feature state
- components render props and emit callbacks

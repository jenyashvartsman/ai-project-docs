# React UI Components Guide

## Purpose

This guide defines when UI should stay feature-local and when it should move to `shared/ui`.

The goal is to stop React component libraries from becoming a vague dumping ground while still allowing real reuse to emerge cleanly.

## Core Principle

Default to feature-local UI first. Extract shared UI only after real reuse or a clear design-system need.

## Shared UI Rules

`shared/ui` components should stay:

- presentation-only
- reusable across features
- independent from app-domain state
- accessible by default

Shared UI should not:

- import feature hooks
- call app APIs
- know about route params
- depend on business-specific types when a generic API would be clearer

## Feature-Local UI Rules

Keep a component in the feature when:

- it represents feature-specific language or behavior
- the props are tightly coupled to one feature
- reuse is only hypothetical
- extraction would make the API worse

Examples that often stay feature-local:

- order list rows
- order filters
- profile completion cards
- dashboard-specific data panels

## Component API Design

Prefer:

- explicit props
- small callback surfaces
- composition when it improves flexibility

Avoid:

- components that accept a large unstructured `options` object
- components that need many app-specific flags to be reusable
- generic abstractions that are only used once

## Shared Primitive Example

```tsx
type ButtonProps = React.ButtonHTMLAttributes<HTMLButtonElement> & {
  variant?: 'primary' | 'secondary' | 'danger';
};

export function Button({ variant = 'primary', ...props }: ButtonProps) {
  return <button data-variant={variant} {...props} />;
}
```

This works in `shared/ui` because:

- it is presentation-only
- the API is generic
- it has real potential for reuse

## Feature-Local Example

```tsx
type OrderListProps = {
  orders: OrderDto[];
  deletingOrderId: string | null;
  onDelete: (orderId: string) => void;
};

export function OrderList({
  orders,
  deletingOrderId,
  onDelete,
}: OrderListProps) {
  return (
    <ul>
      {orders.map((order) => (
        <li key={order.id}>
          <span>{order.name}</span>
          <button
            type="button"
            disabled={deletingOrderId === order.id}
            onClick={() => onDelete(order.id)}
          >
            Delete
          </button>
        </li>
      ))}
    </ul>
  );
}
```

This usually belongs in the feature because its props and language are domain-specific.

## Composition

React makes composition cheap, but that does not mean every shared component should become a render-prop or compound-component abstraction.

Prefer simple composition first:

- `children`
- a small number of slots via props
- plain wrapper components

Reach for more advanced patterns only when they clearly improve ergonomics.

## Anti-Patterns

Avoid:

- moving feature-specific cards and tables into `shared/ui` too early
- putting hooks or transport calls into shared components
- building generic abstractions that make simple markup harder to understand
- extracting shared components before there is real shared behavior

## AI Agent Rules

When creating or updating React UI components, AI agents must follow these rules:

1. Keep components feature-local by default.
2. Move components to `shared/ui` only when reuse is real and the API stays generic.
3. Keep shared UI presentation-only.
4. Prefer explicit props over magic configuration objects.
5. Do not hide feature orchestration inside shared UI.

## Default Standard

If you are unsure, keep the component in the feature first.

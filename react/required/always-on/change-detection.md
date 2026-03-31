# React Rendering Guide

## Purpose

This guide defines practical rendering and state-placement rules for React applications.

It is not a micro-optimization guide. It exists to keep rendering predictable and to prevent premature memoization-heavy code.

## Core Principle

Use component boundaries and state placement to control rendering before reaching for optimization hooks.

## State Placement

Keep state as local as possible.

Prefer:

- local state for local UI behavior
- feature hooks or feature stores for shared feature orchestration
- server-state tools for server data when the project uses them

## Derived State

Prefer deriving values during render unless the calculation is genuinely expensive.

Prefer:

```tsx
const hasOrders = orders.length > 0;
const selectedOrder = orders.find((order) => order.id === selectedOrderId) ?? null;
```

Avoid storing those values in extra state unless they truly have independent lifecycle needs.

## `useMemo`, `useCallback`, and `memo`

Do not add them by default.

Use them when:

- a child component is intentionally memoized and callback identity matters
- a heavy derived calculation is actually expensive
- profiling or obvious rerender pressure shows they help

## Effects

Use `useEffect` for real side effects only.

Avoid using `useEffect` for:

- straightforward derived values
- logic that should happen directly in an event handler
- data shaping that can happen during render

## Example

Prefer:

```tsx
const filteredOrders = orders.filter((order) => order.status === activeStatus);
```

Instead of:

```tsx
const [filteredOrders, setFilteredOrders] = useState<OrderDto[]>([]);

useEffect(() => {
  setFilteredOrders(orders.filter((order) => order.status === activeStatus));
}, [orders, activeStatus]);
```

## Anti-Patterns

Avoid:

- adding `useMemo` and `useCallback` everywhere by default
- keeping derived values in state
- lifting local state too high
- `useEffect` used as a substitute for plain render logic

## AI Agent Rules

When creating or updating React components, AI agents must follow these rules:

1. Keep state near the component that owns it.
2. Prefer derived values during render over extra state.
3. Do not add `useMemo`, `useCallback`, or `memo` by default.
4. Split components when responsibilities become mixed.
5. Use `useEffect` only for real side effects.

## Default Standard

If you are unsure what to do, use this default:

- local state first
- derive values during render
- keep container and presentation boundaries clear

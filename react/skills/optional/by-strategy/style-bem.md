# React BEM Styling Guide

## Purpose

This guide defines a production-grade BEM styling strategy for React applications that choose CSS or SCSS over utility-first styling.

The goal is to keep component styles predictable, scalable, and easy for developers and AI agents to extend without creating selector chaos.

## Core Principle

One React component should usually map to one BEM block.

Keep styling ownership with the component that renders the markup. Use elements and modifiers to express structure and variation, not ad hoc class names.

## Recommended File Pairing

```text
components/
  order-card.tsx
  order-card.scss
```

Keep the style file next to the component that owns the markup.

## Naming Rules

- block: `.order-card`
- element: `.order-card__header`
- modifier: `.order-card--selected`
- element modifier: `.order-card__status--error`

Prefer nested SCSS with `&__element` and `&--modifier`.

## Example

```tsx
type OrderCardProps = {
  order: OrderDto;
  isSelected: boolean;
  onSelect: (orderId: string) => void;
};

export function OrderCard({ order, isSelected, onSelect }: OrderCardProps) {
  return (
    <article className={`order-card${isSelected ? ' order-card--selected' : ''}`}>
      <header className="order-card__header">
        <h3 className="order-card__title">{order.name}</h3>
        <span className={`order-card__status order-card__status--${order.status}`}>
          {order.status}
        </span>
      </header>

      <div className="order-card__meta">
        <span className="order-card__id">#{order.id}</span>
      </div>

      <button
        type="button"
        className="order-card__action"
        onClick={() => onSelect(order.id)}
      >
        Select
      </button>
    </article>
  );
}
```

```scss
.order-card {
  background: var(--surface-default);
  border: 1px solid var(--border-muted);
  border-radius: 16px;
  padding: 16px;

  &--selected {
    border-color: var(--border-strong);
    box-shadow: 0 0 0 2px var(--focus-ring);
  }

  &__header {
    align-items: center;
    display: flex;
    justify-content: space-between;
    margin-bottom: 12px;
  }

  &__title {
    color: var(--text-primary);
    font-size: 1rem;
    font-weight: 600;
    margin: 0;
  }

  &__status {
    border-radius: 999px;
    font-size: 0.75rem;
    padding: 4px 8px;

    &--open {
      background: var(--status-open-bg);
      color: var(--status-open-text);
    }

    &--closed {
      background: var(--status-closed-bg);
      color: var(--status-closed-text);
    }
  }

  &__meta {
    color: var(--text-secondary);
    margin-bottom: 16px;
  }

  &__action {
    background: var(--action-primary-bg);
    border: 0;
    border-radius: 10px;
    color: var(--action-primary-text);
    cursor: pointer;
    padding: 8px 12px;
  }
}
```

## Theme Initialization

Theme tokens should be initialized globally and consumed through semantic CSS variables.

Example:

```scss
:root {
  --surface-default: #ffffff;
  --border-muted: #d7dce3;
  --border-strong: #1f3b63;
  --focus-ring: rgba(31, 59, 99, 0.2);
  --text-primary: #1d2530;
  --text-secondary: #5b6572;
  --action-primary-bg: #1f3b63;
  --action-primary-text: #ffffff;
  --status-open-bg: #e8f7ec;
  --status-open-text: #1a6a35;
  --status-closed-bg: #f2f4f7;
  --status-closed-text: #49505a;
}

[data-theme='dark'] {
  --surface-default: #18202b;
  --border-muted: #334153;
  --border-strong: #86a9df;
  --focus-ring: rgba(134, 169, 223, 0.25);
  --text-primary: #edf2f7;
  --text-secondary: #b3bfcc;
  --action-primary-bg: #86a9df;
  --action-primary-text: #0f1720;
  --status-open-bg: #163322;
  --status-open-text: #9fe0b4;
  --status-closed-bg: #273140;
  --status-closed-text: #d8dee6;
}
```

Feature styles should consume these variables, not redefine theme colors locally.

## Modifiers

Use modifiers for meaningful UI states:

- selected
- compact
- loading
- error

Do not create modifiers for implementation details that are not actual UI variants.

Good:

- `.order-card--selected`
- `.order-table--compact`

Avoid:

- `.order-card--use-flex`
- `.order-card--has-margin`

## React-Specific Rules

In React, class composition often happens in JSX. Keep it readable.

Prefer:

```tsx
<section className={`orders-panel${isLoading ? ' orders-panel--loading' : ''}`}>
```

or a project-standard classnames helper if the app already uses one.

Do not move class-name logic into large helper abstractions if a direct expression is clearer.

## Component Boundaries

BEM block ownership should usually follow component ownership.

If one component renders a distinct subtree with its own structure and variants, it should often become its own block instead of inheriting the parent block forever.

## Anti-Patterns

Avoid:

- deeply chained descendant selectors
- one SCSS file styling many unrelated components
- mixing BEM with arbitrary naming conventions in the same component
- hardcoding theme colors repeatedly in feature styles
- creating elements for nodes that belong to a different component block

## AI Agent Rules

When creating or updating React BEM styles, AI agents must follow these rules:

1. Keep one main block per component.
2. Use `&__element` and `&--modifier` in nested SCSS.
3. Use semantic theme tokens instead of raw repeated values.
4. Keep BEM ownership aligned with component ownership.
5. Do not mix unrelated naming patterns inside the same style file.

## Default Standard

If you are unsure what to do, use:

- one block per component
- elements for internal structure
- modifiers for meaningful UI variants
- global theme tokens consumed through CSS variables

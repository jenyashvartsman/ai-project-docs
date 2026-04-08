# React Tailwind Styling Guide

## Purpose

This guide defines a production-grade Tailwind strategy for React applications.

The goal is to keep utility-first styling fast and expressive without letting class lists become unreadable or theme values become inconsistent.

## Core Principle

Prefer template-local utility composition and semantic theme tokens.

Use Tailwind to describe the component in place. Extract only when repetition is real and the abstraction actually improves clarity.

## Theme Definition

Define theme tokens centrally and map them into Tailwind.

Example CSS variables:

```css
:root {
  --surface-default: 255 255 255;
  --surface-muted: 244 246 248;
  --text-primary: 29 37 48;
  --text-secondary: 91 101 114;
  --border-muted: 215 220 227;
  --brand-primary: 31 59 99;
  --brand-primary-foreground: 255 255 255;
}

[data-theme='dark'] {
  --surface-default: 24 32 43;
  --surface-muted: 32 42 55;
  --text-primary: 237 242 247;
  --text-secondary: 179 191 204;
  --border-muted: 51 65 83;
  --brand-primary: 134 169 223;
  --brand-primary-foreground: 15 23 32;
}
```

Example Tailwind config mapping:

```ts
theme: {
  extend: {
    colors: {
      surface: 'rgb(var(--surface-default) / <alpha-value>)',
      'surface-muted': 'rgb(var(--surface-muted) / <alpha-value>)',
      text: 'rgb(var(--text-primary) / <alpha-value>)',
      'text-muted': 'rgb(var(--text-secondary) / <alpha-value>)',
      border: 'rgb(var(--border-muted) / <alpha-value>)',
      brand: 'rgb(var(--brand-primary) / <alpha-value>)',
      'brand-foreground': 'rgb(var(--brand-primary-foreground) / <alpha-value>)',
    },
  },
}
```

## Utility Ordering

Keep class composition readable by grouping utilities roughly in this order:

- layout
- spacing
- sizing
- typography
- color and background
- border and effects
- state and responsive variants

The exact order does not need to be perfect, but it should stay consistent.

## Example

```tsx
type OrderCardProps = {
  order: OrderDto;
  isSelected: boolean;
  onSelect: (orderId: string) => void;
};

export function OrderCard({ order, isSelected, onSelect }: OrderCardProps) {
  return (
    <article
      className={[
        'rounded-2xl border bg-surface p-4 shadow-sm transition',
        isSelected ? 'border-brand ring-2 ring-brand/20' : 'border-border',
      ].join(' ')}
    >
      <header className="mb-3 flex items-center justify-between gap-3">
        <h3 className="text-sm font-semibold text-text">{order.name}</h3>
        <span className="rounded-full bg-surface-muted px-2 py-1 text-xs text-text-muted">
          {order.status}
        </span>
      </header>

      <p className="mb-4 text-sm text-text-muted">#{order.id}</p>

      <button
        type="button"
        className="rounded-xl bg-brand px-3 py-2 text-sm font-medium text-brand-foreground"
        onClick={() => onSelect(order.id)}
      >
        Select
      </button>
    </article>
  );
}
```

## Conditional Classes

Keep conditional class logic local and readable.

Good options:

- a small array joined with spaces
- a project-standard helper such as `clsx` if the app already uses it

Avoid giant inline ternaries that make the JSX hard to scan.

## Extraction Threshold

Do not extract Tailwind class strings too early.

Extract only when:

- the same pattern repeats across several places
- the extracted name communicates a real UI concept
- the abstraction reduces noise rather than hiding the design

Good:

- reusable `buttonVariants`
- a shared card shell used across many features

Avoid:

- extracting a one-off group of classes into a constant just because it feels long

## `@apply`

Use `@apply` sparingly.

Good reasons:

- shared design-system primitives
- repeated component shell classes

Avoid using `@apply` to rebuild an entire CSS architecture on top of Tailwind.

## Responsive And State Variants

Use responsive and state variants where they make layout or interaction meaning clearer.

Prefer:

- `md:grid-cols-2`
- `hover:bg-surface-muted`
- `focus-visible:ring-2`

Do not stack variants so heavily that the intent becomes opaque.

## React-Specific Rules

In React, Tailwind should work with the container/presentation split:

- containers prepare state
- presentation components receive clear variant props or booleans
- the component translates those props into classes

Do not push business logic into class composition.

## Anti-Patterns

Avoid:

- hardcoding raw hex values repeatedly
- extracting every class list into constants
- unreadable class composition with too many nested conditions
- mixing Tailwind with unrelated component-local CSS without a clear reason
- treating `@apply` as a replacement for a proper utility-first workflow

## AI Agent Rules

When creating or updating React Tailwind code, AI agents must follow these rules:

1. Prefer template-local utility composition.
2. Use semantic theme tokens mapped through Tailwind config.
3. Keep conditional classes readable and local.
4. Extract repeated utility patterns only when repetition is real.
5. Use `@apply` sparingly.

## Default Standard

If you are unsure what to do, use:

- semantic Tailwind theme tokens
- local utility composition in JSX
- small reusable abstractions only after real repetition

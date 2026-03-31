# React Error Handling Guide

## Purpose

This guide defines a simple, production-oriented error handling strategy for React applications.

The goal is to keep transport failures, feature failures, and user-facing messages predictable instead of letting each component invent its own error handling style.

## Core Principle

Handle errors at the correct layer.

- API functions own transport concerns.
- Feature hooks, query hooks, or stores own feature error state.
- Components render errors near the affected UI.

## Error Categories

Keep these separate:

- field validation errors
- form submit errors
- feature data-load errors
- global app-level errors

These categories usually need different UI placement and different recovery behavior.

## Feature Hook Or Store Responsibility

Feature orchestration should:

- expose `isLoading`
- expose feature-level `error`
- reset stale errors when appropriate
- translate transport failures into stable UI state

Example:

```ts
export function useOrders() {
  const [orders, setOrders] = useState<OrderDto[]>([]);
  const [isLoading, setIsLoading] = useState(false);
  const [error, setError] = useState<string | null>(null);

  async function loadOrders() {
    setIsLoading(true);
    setError(null);

    try {
      const nextOrders = await getOrders();
      setOrders(nextOrders);
    } catch {
      setError('Unable to load orders.');
    } finally {
      setIsLoading(false);
    }
  }

  return { orders, isLoading, error, loadOrders };
}
```

## API Responsibility

API modules may:

- throw transport failures
- normalize response parsing errors
- map technical errors into app-standard error objects if the project needs that

API modules should not:

- render UI
- open toasts or dialogs
- decide final feature-facing copy for every component

## UI Rules

Render errors close to the failed interaction.

Prefer:

- field errors near the field
- submit errors in the form area
- page-load errors in the page content area
- global session errors at the app shell level

Do not rely only on console logs or global toasts for important feature failures.

## Retry Behavior

Retry should be explicit when it improves the experience.

Good examples:

- retry loading a list
- retry loading order details

Be more careful with:

- destructive actions
- multi-step flows
- duplicate-submit scenarios

## TanStack Query Note

If the feature uses TanStack Query:

- translate query status into stable UI state at the feature boundary
- keep query errors from leaking raw transport details directly into low-level presentation components

## Anti-Patterns

Avoid:

- API modules deciding final UI presentation
- swallowing errors silently
- mixing empty state and error state
- leaving stale error state visible after successful retry
- treating every error as a global toast

## AI Agent Rules

When creating or updating React error handling, AI agents must follow these rules:

1. Keep transport concerns in the API layer.
2. Let the feature hook, query hook, or store own feature error state.
3. Render errors near the affected UI.
4. Keep field, form, feature, and global errors separate.
5. Prefer stable user-facing messages over raw backend text.

## Default Standard

If you are unsure what to do, use:

- API throws
- feature hook or store catches and normalizes
- container renders the error

# React Accessibility Guide

## Purpose

This guide defines a practical accessibility baseline for React applications.

The goal is to make accessibility part of normal component design rather than a cleanup step after the UI already exists.

## Core Principle

Use semantic HTML first and keep interactive UI keyboard accessible by default.

React does not change accessibility fundamentals. JSX should still prioritize native semantics, visible labels, focus behavior, and understandable state.

## Semantic HTML

Choose the correct native element before adding ARIA.

Prefer:

- `button` for actions
- `a` for navigation
- `form` for form submission
- `label` for inputs
- `table` for tabular data
- `header`, `main`, `section`, `nav`, and `footer` for page landmarks

Avoid turning `div` and `span` into pseudo-controls when a semantic element already solves the problem.

## Buttons And Links

Do not blur the difference between actions and navigation.

- use buttons for actions
- use links for navigation
- always provide button `type`

Bad:

```tsx
<div onClick={saveProfile}>Save</div>
```

Good:

```tsx
<button type="button" onClick={saveProfile}>
  Save
</button>
```

## Keyboard Accessibility

Every interactive feature must work with a keyboard.

At minimum:

- interactive elements must be reachable by `Tab`
- actions must work through standard keyboard interaction
- focus order must follow logical reading and interaction order
- focus must remain visible

Do not remove focus styles unless you replace them with a visible, accessible alternative.

## Forms

Forms should expose:

- visible labels
- clear validation messages
- accessible error relationships when needed
- understandable required state

Example:

```tsx
<label htmlFor="email">Email</label>
<input
  id="email"
  type="email"
  aria-describedby={errors.email ? 'email-error' : undefined}
  aria-invalid={errors.email ? 'true' : 'false'}
  {...register('email', { required: true })}
/>
{errors.email && <p id="email-error">Email is required.</p>}
```

## Dialogs And Overlays

Dialogs need:

- focus moved into the dialog when opened
- trapped focus while open
- focus returned to the trigger when closed
- a visible title
- a keyboard-accessible close action

Prefer accessible dialog primitives instead of recreating dialog behavior from scratch.

## Icons And Icon-Only Controls

Decorative icons should be hidden from assistive tech. Icon-only actions need an accessible name.

Example:

```tsx
<button type="button" aria-label="Close dialog">
  <CloseIcon aria-hidden="true" />
</button>
```

## Async And Status Messaging

When a feature changes asynchronously, make important state understandable.

Examples:

- loading a page section
- save succeeded
- delete failed

Do not rely only on color or only on console logs.

## Anti-Patterns

Avoid:

- clickable `div` elements
- hidden focus outlines with no replacement
- icon-only actions with no accessible name
- placeholder text used as the only label
- color-only status communication
- dialogs with broken focus behavior

## AI Agent Rules

When creating or updating React UI, AI agents must follow these rules:

1. Prefer semantic HTML before ARIA.
2. Keep interactive UI keyboard accessible.
3. Preserve visible focus.
4. Keep form controls labeled.
5. Use accessible primitives for complex widgets such as dialogs.

## Default Standard

If you are unsure, choose the more semantic native element and keep the interaction keyboard-friendly.

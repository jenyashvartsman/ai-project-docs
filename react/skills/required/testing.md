# React Testing Guide

## Purpose

This guide defines a simple testing strategy for React applications.

It intentionally focuses only on:

- unit tests
- integration tests

The goal is not exhaustive testing theory. The goal is to protect meaningful behavior with a small, predictable standard.

## Core Principle

Test behavior where it lives.

- unit test isolated logic
- integration test feature flows and collaboration between components, hooks, forms, and stores

## Unit Tests

Use unit tests for:

- custom hooks with meaningful orchestration logic
- Zustand stores or Redux selectors and reducers
- validators
- API helpers when request shaping matters
- presentation components with meaningful conditional rendering or callback behavior

Good unit-test targets:

- `useOrders`
- `useOrdersStore`
- `hasPermission`
- `profile-form-schema`

## Integration Tests

Use integration tests for:

- route pages and containers
- form submit flows
- interaction between page, feature hook, and child components
- loading, error, and empty-state flows

A good integration test usually covers the user-visible flow rather than every internal implementation branch.

## Placement

Keep tests next to the source file.

Examples:

- `orders-page.tsx` -> `orders-page.test.tsx`
- `use-orders.ts` -> `use-orders.test.ts`
- `orders-api.ts` -> `orders-api.test.ts`

## What To Test By Layer

### Presentation Components

Test when they:

- render meaningful conditional UI
- emit important callbacks
- apply accessibility-critical behavior

Do not over-test static markup.

### Containers And Pages

Prefer integration tests that verify:

- loading state
- error state
- empty state
- success state
- callback wiring into hooks or stores

### Hooks And Stores

Test:

- initial state
- derived state
- loading and error handling
- successful mutation behavior
- failure paths when meaningful

## Anti-Patterns

Avoid:

- over-testing static markup
- duplicating the same assertions in unit and integration tests
- shallow tests with weak assertions
- testing implementation trivia instead of behavior

## AI Agent Rules

When creating or updating React tests, AI agents must follow these rules:

1. Add unit tests for meaningful isolated logic.
2. Add integration tests for real feature flows.
3. Keep tests close to the source file.
4. Do not introduce broader test layers unless explicitly requested.
5. Do not over-test static presentational markup.

## Default Standard

If you are unsure what to test, cover:

- hook or store behavior
- form submit behavior
- page-level loading, error, and success flow

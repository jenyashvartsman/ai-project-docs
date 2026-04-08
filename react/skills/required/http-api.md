# React HTTP API Guide

## Purpose

This guide defines the transport layer for React applications.

The goal is to keep HTTP code thin, typed, and separate from React component logic.

## Core Principle

Components should consume feature state or API abstractions, not own HTTP details directly.

## Recommended Structure

```text
features/
  orders/
    api/
      orders-api.ts
    types/
      order.dto.ts
      delete-order-request.ts
```

## API Layer Responsibilities

API modules should:

- execute HTTP requests
- compose URLs and request params
- return typed promises
- throw or normalize transport failures consistently

API modules should not:

- manage UI loading state
- decide final user-facing error rendering
- contain component-specific orchestration

## Example

```ts
import type { OrderDto } from '../types/order.dto';

const ordersApiUrl = '/api/orders';

export async function getOrders(): Promise<OrderDto[]> {
  const response = await fetch(ordersApiUrl);

  if (!response.ok) {
    throw new Error('Unable to load orders.');
  }

  return response.json() as Promise<OrderDto[]>;
}

export async function deleteOrder(orderId: string): Promise<void> {
  const response = await fetch(`${ordersApiUrl}/${orderId}`, {
    method: 'DELETE',
  });

  if (!response.ok) {
    throw new Error('Unable to delete order.');
  }
}
```

## Query Parameters

Build query params explicitly.

## Fetching Library Choice

This guide does not force `fetch` versus `axios`.

Use the project-standard client, but keep the architecture the same:

- API module owns transport
- feature hook or store owns UI state
- component renders the result

## Anti-Patterns

Avoid:

- calling `fetch` directly in presentation components
- putting endpoint strings inline throughout the app
- making API modules manage toast notifications or dialogs
- creating repository or mapper layers by default without a real complexity reason

## AI Agent Rules

When creating or updating React API code, AI agents must follow these rules:

1. Keep transport code in `api/`.
2. Return typed promises.
3. Keep base endpoints in local constants.
4. Do not introduce repository or mapper layers by default.
5. Do not let presentation components own HTTP calls.

## Default Standard

If you are unsure what to do, use this default:

- `api/` module with typed functions
- feature hook or store consumes those functions
- page or container renders the resulting UI state

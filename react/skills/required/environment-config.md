# React Environment And Configuration Guide

## Purpose

This guide defines a simple, production-grade configuration strategy for React applications.

## Core Principle

Treat environment values as app infrastructure, not as feature-local state.

## Rules

- Define config centrally.
- Read config through a stable app-level module.
- Do not place secrets in frontend code.
- Do not scatter direct `process.env` or `import.meta.env` access across the app.
- Derive API base URLs from config and keep per-service local endpoint constants.

## Recommended Structure

```text
src/
  core/
    config/
      app-config.ts
      env.ts
```

## Example

```ts
export type AppConfig = {
  apiBaseUrl: string;
  appName: string;
  isProduction: boolean;
};

export const appConfig: AppConfig = {
  apiBaseUrl: import.meta.env.VITE_API_BASE_URL ?? '/api',
  appName: import.meta.env.VITE_APP_NAME ?? 'Orders Admin',
  isProduction: import.meta.env.PROD,
};
```

```ts
import { appConfig } from '../../../core/config/app-config';

const ordersApiUrl = `${appConfig.apiBaseUrl}/orders`;
```

## Feature Flags

Keep feature flags typed and centralized.

Do not spread environment checks throughout feature code.

## Anti-Patterns

Avoid:

- hardcoded hosts across many API files
- direct env reads all over the codebase
- storing secrets in client config
- mixing app config with user state

## AI Agent Rules

When creating or updating React config code, AI agents must follow these rules:

1. Centralize app config.
2. Keep env reads in one config boundary.
3. Do not place secrets in frontend code.
4. Derive endpoints from config rather than hardcoding hosts repeatedly.

## Default Standard

If you are unsure what to do, use:

- one typed config module
- centralized env access
- feature code consuming config indirectly through API modules and infrastructure

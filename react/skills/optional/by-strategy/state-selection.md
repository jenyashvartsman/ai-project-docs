# React State Selection Guide

## Purpose

This guide explains when to use each state strategy in this React documentation set.

React needs a more explicit split than Angular between:

- server state
- client feature state
- high-complexity event-driven state

That is why the React state guide set is not a one-to-one copy of the Angular one.

Across these strategies, route params and search params are valid state inputs when the URL should reflect the current feature view.

## Available Strategies

- [state-feature-hooks.md](./state-feature-hooks.md)
- [state-tanstack-query.md](./state-tanstack-query.md)
- [state-zustand.md](./state-zustand.md)
- [state-redux-toolkit.md](./state-redux-toolkit.md)

## Core Principle

Choose the lightest strategy that matches the actual complexity and choose a server-state strategy separately from a client-state strategy when needed.

When state should be shareable, bookmarkable, restorable on refresh, or coordinated with browser navigation, keep that state in the URL and let the chosen state layer own it.

## Default Order

Start by asking:

1. Is the feature mostly server state?
2. If not, is a feature hook enough?
3. If not, does it need a lightweight shared store or a formal event-driven architecture?

## Choose Feature Hooks When

- the feature is mostly one page flow
- local state is modest
- one custom hook keeps things clear
- the feature does not need a dedicated shared store
- one feature hook can clearly own route/search param state

Good examples:

- profile settings page
- simple orders list with filtering
- one route with URL-backed filters and selected item state
- one route that loads, edits, and saves data without complex cross-screen coordination

## Choose TanStack Query When

- the feature is dominated by remote data
- caching and invalidation matter
- refetch rules and stale handling matter
- query status should follow one consistent model
- a query hook can own URL-backed filters that shape query keys

Good examples:

- paginated resource lists
- dashboards with refetch behavior
- data-heavy screens where server state is the main concern
- list pages where search params define the query key

## Choose Zustand When

- several components inside the feature share client state
- the feature hook approach is getting crowded
- a dedicated lightweight store improves clarity
- Redux Toolkit would still be more structure than the feature needs
- a dedicated store should own URL-backed client state for one feature

Good examples:

- rich filter panels shared across page sections
- interactive multi-panel feature state
- medium-complexity client-side coordination within one feature
- pages where route/search params drive several shared UI regions

## Choose Redux Toolkit When

- event-driven complexity is high
- many transitions or async flows interact
- long-term complexity benefits from explicit reducers, actions, and selectors
- several parts of the app coordinate around the same feature events
- router-driven state changes participate in broader event flows

Good examples:

- workflow-heavy admin domains
- large domains with several async transitions
- features that genuinely benefit from explicit event modeling
- screens where route/search param changes trigger reducer-driven workflows

## Important React Rule

React apps often need both:

- a server-state strategy such as TanStack Query
- a client-state strategy such as local state, feature hooks, Zustand, or Redux Toolkit

Do not force all of that into one tool.

Examples:

- TanStack Query for orders list data, local state for selected tab
- TanStack Query for order details plus Zustand for local panel state
- Redux Toolkit for a complex client workflow and plain API functions for mutations
- TanStack Query for remote list data and a feature hook/store owning URL-backed filters

## Simple Decision Matrix

### Start With Feature Hooks

Choose this when:

- the flow is simple
- the feature is route-local
- one custom hook stays understandable
- one hook can own the route/search param contract cleanly

### Add TanStack Query

Choose this when:

- server data is the dominant concern
- caching and invalidation matter
- the query hook can treat search params as part of the feature boundary

### Move To Zustand

Choose this when:

- local client-state coordination is getting too large for one hook
- the feature needs a dedicated store but not Redux-level ceremony
- the store should own shared URL-backed client state

### Move To Redux Toolkit

Choose this when:

- the complexity is in state transitions and event modeling
- explicit reducers and actions pay for themselves
- URL state changes need to participate in reducer-driven flows

## Anti-Patterns

Avoid:

- using Redux Toolkit for trivial local state
- using Zustand as a default replacement for local `useState`
- putting server caching concerns into Zustand or Redux by default
- using one giant feature hook for several unrelated workflows
- forcing React state architecture to mirror Angular patterns exactly

## AI Agent Rules

When choosing React state architecture, AI agents must follow these rules:

1. Treat server state and client state as separate decisions.
2. Prefer feature hooks first for simple feature orchestration.
3. Prefer TanStack Query for server-state-heavy features when the project uses it.
4. Use Zustand for medium-complexity shared client state.
5. Use Redux Toolkit only when event-driven complexity justifies it.
6. Do not force the React guide set to mirror Angular state patterns.
7. Treat route params and search params as first-class state inputs when the feature must support deep links, shareable views, or refresh restore.

## Default Standard

If you are unsure what to use:

- start with a feature hook
- add TanStack Query when server-state concerns dominate
- move to Zustand if client-state coordination grows
- move to Redux Toolkit only for the most complex domains

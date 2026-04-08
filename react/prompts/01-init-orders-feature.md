# Task

Create a new `orders` feature in the React application.

Set up the feature folder structure, route entry page, API module, types, and the initial state layer. Focus on the first list-loading flow and the feature architecture, not on highly polished UI.

# Guides

- `../skills/required/directories.md`
- `../skills/required/naming.md`
- `../skills/required/routing.md`
- `../skills/required/feature.md`
- `../skills/required/http-api.md`
- `../skills/required/change-detection.md`
- `../skills/required/error-handling.md`
- `../skills/optional/by-strategy/state-selection.md`
- `../skills/optional/by-strategy/state-feature-hooks.md`

# Requirements

- Create the feature under `features/orders`.
- Add a route entry page in `pages/orders-page.tsx`.
- Add `api/`, `pages/`, `components/`, `hooks/`, and `types/` only if the feature needs each folder immediately.
- Use the feature-hooks strategy unless the existing app already uses a different state strategy for this feature.
- Keep the default flow as `page/container -> feature hook -> api`.
- Add typed transport functions in `api/orders-api.ts`.
- Add feature-owned types in `types/`, including the primary order DTO.
- Add an initial `useOrders` hook that exposes `orders`, `isLoading`, `error`, and `loadOrders`.
- Keep the page as a container component.
- Do not add repositories or mappers.
- Do not add a global store for this feature unless the app already requires it.
- Build enough UI to prove the loading flow works, but do not spend effort on final presentation details yet.
- Keep the page ready for future extraction into presentation components in the next prompt.

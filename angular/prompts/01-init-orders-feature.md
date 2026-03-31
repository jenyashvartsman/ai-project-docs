# Task

Create a new `orders` feature in the Angular application.

Set up the feature folder structure, route entry page, feature API service, DTOs, and state layer. Do not build detailed presentation UI yet. Focus on the initial feature skeleton and the first list-loading flow.

# Guides

- `../required/always-on/directories.md`
- `../required/always-on/naming.md`
- `../required/always-on/routing.md`
- `../required/always-on/feature.md`
- `../required/always-on/http-api.md`
- `../required/always-on/change-detection.md`
- `../required/always-on/error-handling.md`
- `../optional/by-strategy/state-selection.md`
- `../optional/by-strategy/state-facade.md`

# Requirements

- Use a feature-first structure under `features/orders`.
- Add a route entry page in `pages/`.
- Use the facade strategy unless the existing app already uses another state strategy for this feature.
- Use `component -> facade -> data-access/api`.
- Add `data-access/api` and `data-access/dto`.
- Do not add repositories or mappers.
- Assume APIs return observables.
- Use signals for facade-owned state.
- Add a list-loading flow with `orders`, `isLoading`, and `error` state.
- Use `ChangeDetectionStrategy.OnPush`.
- Keep the page as a container component.
- Do not create detailed reusable UI components yet unless they are required for the first page render.
- Add unit tests only for meaningful facade logic and integration coverage only if the existing feature pattern already includes it.

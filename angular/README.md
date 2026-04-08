# Angular Docs

## Structure

This documentation set is split into three groups:

- `skills/required`
- `skills/optional/by-strategy`
- `skills/optional/by-app-type`

Use the required docs as the default baseline for every Angular app in this repo. Add optional docs only when the app or feature actually uses that strategy.

## Required / Always-On

These are the baseline conventions that should shape almost every Angular app:

- [directories.md](./skills/required/directories.md)
- [naming.md](./skills/required/naming.md)
- [routing.md](./skills/required/routing.md)
- [feature.md](./skills/required/feature.md)
- [http-api.md](./skills/required/http-api.md)
- [forms.md](./skills/required/forms.md)
- [ui-components.md](./skills/required/ui-components.md)
- [change-detection.md](./skills/required/change-detection.md)
- [testing.md](./skills/required/testing.md)
- [accessibility.md](./skills/required/accessibility.md)
- [error-handling.md](./skills/required/error-handling.md)
- [environment-config.md](./skills/required/environment-config.md)

## Optional / By Strategy

These are selected when the app chooses a specific implementation strategy:

- [style-bem.md](./skills/optional/by-strategy/style-bem.md)
- [style-tailwind.md](./skills/optional/by-strategy/style-tailwind.md)
- [state-facade.md](./skills/optional/by-strategy/state-facade.md)
- [state-ngrx-signal-store.md](./skills/optional/by-strategy/state-ngrx-signal-store.md)
- [state-ngrx-store.md](./skills/optional/by-strategy/state-ngrx-store.md)
- [state-selection.md](./skills/optional/by-strategy/state-selection.md)

State strategy guidance in this repo treats route params and query params as valid state sources when the URL should represent the current feature view.

## Optional / By App Type

These are selected only when the app needs that concern:

- [auth-authorization.md](./skills/optional/by-app-type/auth-authorization.md)

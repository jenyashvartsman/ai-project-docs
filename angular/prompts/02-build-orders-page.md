# Task

Build the first real `orders` page UI for the existing feature.

Use the existing route-level page as the container. Add presentation components for the orders list, empty state, and loading state. Wire the UI to the existing state layer without moving state logic into presentation components.

# Guides

- `../required/always-on/feature.md`
- `../required/always-on/ui-components.md`
- `../required/always-on/change-detection.md`
- `../required/always-on/accessibility.md`
- `../required/always-on/error-handling.md`
- `../required/always-on/testing.md`
- `../optional/by-strategy/style-bem.md`

# Requirements

- Keep the route page as the container component.
- Presentation components must use input and output only.
- Do not inject facades, stores, API services, or `HttpClient` into presentation components.
- Keep the loading, empty, and error states explicit in the page flow.
- Use semantic HTML and keyboard-accessible controls.
- Use `OnPush` for all new components.
- Follow the BEM guide for class naming in this task.
- Keep reusable UI extraction minimal. Only extract components that are clearly feature-local and improve readability.
- Add unit tests for presentation behavior only when the component has meaningful conditional rendering or outputs.
- Add an integration test for the page-container interaction if the feature already uses integration tests.

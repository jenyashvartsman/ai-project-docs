# Task

Build the first real `orders` page UI for the existing React feature.

Use the route-level page as the container. Add presentation components for the orders list, empty state, loading state, and feature error state. Wire the UI to the existing feature hook or state layer without moving orchestration into presentation components.

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
- Extract presentation components for the list and surrounding UI states where that improves readability.
- Presentation components must stay prop-driven.
- Do not call APIs from presentation components.
- Keep loading, empty, and error states explicit in the page flow.
- Use semantic HTML and keyboard-accessible controls.
- Follow the BEM guide for styling in this task.
- Keep reusable UI extraction minimal. Prefer feature-local components first.
- Shape view-ready props in the page or feature hook before passing them to lower-level UI.
- Add focused tests only where there is meaningful behavior.
- Keep the resulting structure ready for mutation flows in later prompts.

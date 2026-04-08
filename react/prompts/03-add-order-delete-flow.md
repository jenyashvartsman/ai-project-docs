# Task

Add the delete-order flow to the existing `orders` feature.

Update the container, presentation components, and state layer so a user can delete an order from the list and see loading and error behavior handled correctly.

# Guides

- `../skills/required/feature.md`
- `../skills/required/http-api.md`
- `../skills/required/change-detection.md`
- `../skills/required/accessibility.md`
- `../skills/required/error-handling.md`
- `../skills/required/testing.md`
- `../skills/optional/by-strategy/state-feature-hooks.md`

# Requirements

- Keep the container and presentation split intact.
- Use an explicit callback prop from the presentation component for delete intent.
- Let the feature hook or chosen state layer own the delete flow.
- Add clear per-operation loading handling for delete so the UI can disable or annotate the active row.
- Show a clear user-facing error when delete fails.
- Do not reload the whole page to reflect deletion.
- Update feature state locally after a successful delete.
- Keep destructive actions clearly labeled and keyboard accessible.
- Add focused tests for delete behavior in the hook and any meaningful integration points in the page flow.
- Keep the implementation ready for future confirmation dialogs or permission checks without rewriting the architecture.

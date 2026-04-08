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
- `../skills/optional/by-strategy/state-facade.md`

# Requirements

- Keep the container and presentation split intact.
- Use an explicit output from the presentation component for delete intent.
- The facade should own the delete flow and resulting state updates.
- Assume the API returns an observable.
- Use `firstValueFrom(...)` only if the delete operation must be awaited by the caller.
- Use `finalize(...)` for loading cleanup.
- Show a clear user-facing error message when delete fails.
- Keep destructive actions accessible and clearly labeled.
- Do not reload the whole page to reflect deletion.
- Update or add unit tests for facade delete behavior.
- Add or update integration coverage for the delete interaction if the feature uses integration tests.

# Testing Standards

## Unit Tests

- All new code must have unit tests with 100% line coverage
- Follow the AAA pattern: Arrange, Act, Assert
- Test both success and error/edge cases
- Mock external dependencies (DB, APIs, Pub/Sub) — do not mock the unit under test
- See `tooling/TOOLING.md` / `tooling/TOOLING_<lang>.md` for test file conventions, runners, and coverage commands per language

## What to Test

- Every public function and method in new code
- Happy path
- Error cases (invalid input, null/empty, missing data, thrown exceptions)
- Boundary conditions (zero items, one item, max items)
- Side effects (was the notification sent? was the log written?)

## What NOT to Test

- Third-party library internals
- Simple type definitions or constants
- Code you did not change (unless your change affects its behavior)

## Linting, Formatting, and Type Checking

See `tooling/TOOLING.md` / `tooling/TOOLING_<lang>.md` for language-specific lint, format, and typecheck commands.

- Lint all changed code before submitting. Fix all errors and warnings — do not suppress or disable rules without justification.
- Auto-format code to keep syntax in standard form. Group and sort includes/imports where sane:
    - Imports should not randomly change in git diffs
    - Imports should not make code build via a transitive include

## Running Tests

Use the narrowest scope that gives confidence. See `tooling/TOOLING.md` / `tooling/TOOLING_<lang>.md` for per-language commands.

## Coverage

- Verify 100% line coverage on new code
- If a line is intentionally uncoverable (e.g., defensive fallback that can't be triggered), add a brief comment explaining why
- See `tooling/TOOLING.md` / `tooling/TOOLING_<lang>.md` for per-language coverage commands

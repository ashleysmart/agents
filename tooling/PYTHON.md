# Python Code Standards

Python-specific rules that extend `AGENTS.md`.

---

## File layout

- One top-level class per file.
- Supporting helpers may live in the same file only if small and directly tied to that class.
- If no class is needed, prefer plain functions — do not wrap in an unnecessary class.

## Function design

- Each function does one thing: parsing, validation, orchestration, I/O, and transformation must not be mixed in the same function.
- Prefer early returns over deeply nested logic.
- A function that is hard to name is doing too much — split it.

## Style

- Type annotations on all public function signatures.
- Imports: stdlib → third-party → local, each group alphabetically ordered, no wildcard imports.
- Boolean variable names: `is_`, `has_`, `can_`, or `should_` prefix.
- No magic constants — name them.
- No dead code, commented-out code, or debug statements committed.

## Docstrings

- Every public module, class, and function gets a one-line docstring minimum.
- Describe intent, parameters, return value, and any non-obvious constraints.
- Do not restate what the signature already says.

## Error handling

- Never use bare `except:` or `except Exception:` without re-raising or converting to a typed domain error.
- Every public function that can fail has its failure modes documented in its docstring.

## Coverage

- 97 % minimum per file, not just project-wide.
- Uncovered lines must be explicitly justified; do not hide the gap.

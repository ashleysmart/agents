# Python Tooling

## Test file conventions

| Source | Test file | Location |
|--------|-----------|----------|
| `my_module.py` | `test_my_module.py` | Alongside source or `tests/` directory |

## Linting

```bash
ruff check <changed-files>            # lint
ruff check <changed-files> --fix      # auto-fix
```

## Formatting

```bash
ruff format --check <changed-files>    # check
ruff format <changed-files>            # fix
```

## Type checking

```bash
mypy <changed-files>                   # or pyright <changed-files>
```

## Running Tests

```bash
pytest path/to/test_file.py            # single file
pytest path/to/test_file.py::test_fn   # single test
pytest -x                              # stop on first failure
```

## Coverage

```bash
pytest --cov=<package> --cov-report=term-missing
```

Target: **97 % minimum per file**, not just project-wide. Uncovered lines must be explicitly justified.

---

## Code standards

### File layout

- One top-level class per file.
- Supporting helpers may live in the same file only if small and directly tied to that class.
- If no class is needed, prefer plain functions — do not wrap in an unnecessary class.

### Function design

- Each function does one thing: parsing, validation, orchestration, I/O, and transformation must not be mixed.
- Prefer early returns over deeply nested logic.
- A function that is hard to name is doing too much — split it.

### Style

- Type annotations on all public function signatures.
- Imports: stdlib → third-party → local, each group alphabetically ordered, no wildcard imports.
- Boolean variable names: `is_`, `has_`, `can_`, or `should_` prefix.
- No magic constants — name them.
- No dead code, commented-out code, or debug statements committed.

### Docstrings

- Every public module, class, and function gets a one-line docstring minimum.
- Describe intent, parameters, return value, and any non-obvious constraints.
- Do not restate what the signature already says.

### Error handling

- Never use bare `except:` or `except Exception:` without re-raising or converting to a typed domain error.
- Every public function that can fail has its failure modes documented in its docstring.

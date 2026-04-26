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

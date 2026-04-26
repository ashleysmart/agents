# C++ Tooling

## Test file conventions

| Source | Test file | Location |
|--------|-----------|----------|
| `my_module.cpp` | `my_module_test.cpp` | `tests/` directory or alongside source |

## Linting

```bash
clang-tidy <changed-files>            # lint
```

## Formatting

```bash
clang-format --dry-run --Werror <changed-files>  # check
clang-format -i <changed-files>                  # fix
```

## Type checking

```bash
cmake --build <build-dir>              # compiler is the type checker — must compile with -Werror
```

## Running Tests

```bash
ctest --test-dir <build-dir>                        # all tests
ctest --test-dir <build-dir> -R <test-name-regex>   # specific test
./<build-dir>/tests/<test-binary>                    # run directly
```

## Coverage

```bash
# Build with coverage flags: -fprofile-arcs -ftest-coverage (gcc) or --coverage (clang)
lcov --capture --directory . --output-file coverage.info
genhtml coverage.info --output-directory coverage_html
```

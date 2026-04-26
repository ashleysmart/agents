# TypeScript Tooling

## Test file conventions

| Source | Test file | Location |
|--------|-----------|----------|
| `myModule.ts` | `myModule.test.ts` | Alongside source |

## Linting

```bash
npx eslint <changed-files>            # lint
npx eslint <changed-files> --fix      # auto-fix
```

## Formatting

```bash
npx prettier --check <changed-files>   # check
npx prettier --write <changed-files>   # fix
```

## Type checking

```bash
npx tsc --noEmit                       # whole project
```

## Running Tests

```bash
npx jest path/to/file.test.ts          # single file
npx jest --testPathPattern=<pattern>   # pattern match
```

## Coverage

```bash
npx jest --coverage                    # generates coverage report
```

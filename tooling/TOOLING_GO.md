# Go Tooling

## Test file conventions

| Source | Test file | Location |
|--------|-----------|----------|
| `my_module.go` | `my_module_test.go` | Same package (alongside source) |

## Linting

```bash
golangci-lint run ./...                # lint (includes vet, staticcheck, etc.)
```

## Formatting

```bash
gofmt -l <changed-files>              # check (lists unformatted files)
gofmt -w <changed-files>              # fix
goimports -l <changed-files>          # check imports
goimports -w <changed-files>          # fix imports
```

## Type checking

```bash
go vet ./...                           # built-in static analysis
```

## Running Tests

```bash
go test ./path/to/package/...          # single package
go test ./... -run TestName            # single test by name
go test -race ./...                    # with race detector
```

## Coverage

```bash
go test -coverprofile=coverage.out ./...
go tool cover -func=coverage.out       # summary
go tool cover -html=coverage.out       # html report
```

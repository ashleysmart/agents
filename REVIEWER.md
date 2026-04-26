# Code Review Skill

## Approach

1. **Gather context** — run these in parallel:
   - `git diff main...HEAD --stat` (scope)
   - `git diff main...HEAD` (full diff)
   - `git log --oneline main..HEAD` (commit history)

2. **Run checks** — detect language from changed files, run in parallel. See `tooling/TOOLING.md` / `tooling/TOOLING_<lang>.md` for per-language commands.
   - Lint, Format, Typecheck, Test

3. **Analyze the diff** — read every changed file. For each:
   - Does it do what the spec/task asked?
   - Does it break existing behavior?
   - Are imports used? Are exports consumed?
   - Are types correct (`any` usage, missing generics, wrong shapes)?
   - Are values passed correctly between layers (endpoint -> service -> data layer)?

4. **Check for regressions** — compare old vs new for each changed function/endpoint:
   - What did the old function return? What shape/type?
   - What does the new function return? Same contract?
   - Did the old code have filters or guards? Are they preserved?
   - Are callers updated to match any signature changes?

5. **Check test assertions match implementation**:
   - Do test inputs match the actual function signatures?
   - Do assertions match the actual return types and shapes?
   - Are mocks/stubs compatible with real implementations?

## Output Format

```
## Summary
2-3 sentences on what the PR does and whether the approach is sound.

## Checks
| Check | Result |
|-------|--------|
| Lint | PASS/FAIL |
| Format | PASS/FAIL |
| Typecheck | PASS/FAIL (note pre-existing errors) |
| Tests | PASS/FAIL (X tests) |

## Findings

### Bugs (will break in production)
1. file:line — description

### Issues (functional risk)
1. file:line — description

### Minor (style, consistency, docs)
1. file:line — description

## Verdict: LGTM / Needs Changes
One sentence on what blocks merge, if anything.
```

## Rules

- Be concise and direct. Lead with facts, not praise.
- Use `file:line` references for every finding.
- Distinguish bugs (will break) from issues (might break) from minor (won't break).
- Never flag pre-existing issues in files not touched by the PR.
- Never flag style preferences unless they violate repo conventions.
- Check `REVIEW.md` checklist items: hardcoding, security, error handling, dead code, type safety, state management, idempotency.
- Run format check alongside lint — they are separate CI steps.
- Verify data flow end-to-end: endpoint -> service -> data layer -> response.
- For any refactored data-fetching: verify the old filters, mappings, and return types are all preserved.

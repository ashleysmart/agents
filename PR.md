# Pull Request

## Pre-flight

1. Check state: `git status`, `git log --oneline main..HEAD`, `git diff main...HEAD --stat`
2. Run lint, format, typecheck, and tests (see `tooling/TOOLING.md` / `tooling/TOOLING_<lang>.md`). Auto-fix format failures and commit. **Do not create the PR if any check fails on changed files.**

## Branch prep

```bash
git fetch --prune
git checkout main && git pull origin main
git checkout <branch> && git rebase main
```

- Re-run checks after rebase.
- Squash if requested: `git reset --soft main && git commit -m "message"`
- First push: `git push -u origin <branch>`
- After rebase/squash: `git push --force-with-lease origin <branch>` — never without `--force-with-lease`, never to main.

## Creating the PR

Use `mktemp` for the body — never hardcode `/tmp/` paths (multiple agents may run concurrently):

```bash
TMPFILE=$(mktemp /tmp/pr-XXXXXX.md)
cat <<'PREOF' > "$TMPFILE"
## Summary
<1-3 bullets: what changed and why>

## Test plan
- [ ] Specific pages/features to verify

## Deploy notes
<Risks, monitoring, rollback — only if applicable>
PREOF
gh pr create --title "type(scope): description" --body-file "$TMPFILE"
rm "$TMPFILE"
```

**Title:** under 70 chars, format `type(scope): description`, types: `fix feat refactor docs test chore`.  
**Body:** lead with WHY. Note what was explicitly not changed. Link to spec if one exists. Keep it in sync as the PR evolves.

## Updating a PR

```bash
TMPFILE=$(mktemp /tmp/pr-body-XXXXXX.md)
gh pr view <number> --json body --jq .body > "$TMPFILE"
# edit $TMPFILE
gh pr edit <number> --body-file "$TMPFILE"
rm "$TMPFILE"
```

## Commits

- Message format: `type(scope): description` with optional body explaining why.
- Use HEREDOC for multi-line messages.
- Stage specific files only — never `git add -A` or `git add .`
- Default to new commits; amend only when squashing at user request.
- After a pre-commit hook failure: always a new commit (the failed attempt didn't land).

## Post-creation

1. Verify: `gh pr view <number> --json url --jq .url` and return the URL.
2. Do not merge unless explicitly asked.

## Rules

- Never push without user approval; never force-push to main.
- Never skip hooks (`--no-verify`).
- Always run checks before creating or updating a PR.
- Always use `mktemp` for temp files.

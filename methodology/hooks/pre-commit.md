# Hook: Pre-Commit

## Purpose

Before any commit, verify that changed files pass all quality gates.
This prevents broken code from entering the repository.

## When to Apply

Every time you or the developer are about to commit code.
This is non-negotiable — no exceptions for "quick fixes" or "I'll fix it later."

## Steps

1. Identify changed files:
   ```
   git diff --cached --name-only --diff-filter=d
   ```
   If nothing is staged, check unstaged changes:
   ```
   git diff --name-only --diff-filter=d
   ```

2. Run quality gates on changed files only:
   ```
   make format FILES="<changed files>"
   make lint FILES="<changed files>"
   make typecheck FILES="<changed files>"
   make test FILES="<changed files>"
   ```

3. If format changed any files, stage the formatting changes too.

4. If lint or typecheck fails, fix the issues before committing.

5. If tests fail, investigate. Don't skip failing tests.

## If a Gate Doesn't Exist

If the project doesn't have `make lint` or `make test` yet, tell the developer:
"This project doesn't have [lint/test] set up. Want to add it? For now I'll commit without that check."

Don't block commits because tooling is missing. Flag it and move on.

## If Tests Are Slow

If running related tests takes more than 30 seconds, tell the developer:
"Related tests are slow (~X seconds). Running them now. If you want, we can skip and run the full suite in CI instead."

Let the developer decide. Don't silently skip.

## Setting Up a Git Hook

If the developer wants this automated (not just Claude Code behavior), create
`.git/hooks/pre-commit`:

```bash
#!/bin/sh
FILES=$(git diff --cached --name-only --diff-filter=d)
if [ -n "$FILES" ]; then
  make lint FILES="$FILES" && make typecheck FILES="$FILES" && make test FILES="$FILES"
fi
```

Make it executable: `chmod +x .git/hooks/pre-commit`

Ask before installing — some teams manage hooks through husky or lefthook.
Check if those tools exist in the project first.

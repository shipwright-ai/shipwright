# Skill: Implement Features

## Before Writing Code

1. **Check if the project has docs/** — read vision.md, architecture.md, decisions.md,
   and conventions.md if they exist. Understand the context before touching code.

2. **Check decisions.md specifically** — make sure you're not about to implement
   something in a way that contradicts a decision the team already made.

3. **Understand the scope** — ask the developer to clarify what "done" looks like
   if it's not obvious. A 2-sentence confirmation saves hours of rework.

4. **Find existing patterns** — before creating something new, search the codebase
   for similar implementations. Follow the established pattern unless there's a
   good reason not to. If you deviate, explain why.

## While Writing Code

- Follow conventions.md if it exists, otherwise follow the patterns you observe in the codebase
- Keep changes focused — don't refactor unrelated code while implementing a feature
- If you discover something broken or improvable along the way, note it as an idea
  (see idea-capture skill) instead of fixing it now

## After Writing Code

Run quality gates on the files you changed:

```
make format FILES="<changed files>"
make lint FILES="<changed files>"
make typecheck FILES="<changed files>"
make test FILES="<changed files>"
```

Fix any issues before telling the developer you're done.

If any gate doesn't exist (no Makefile, no test target), tell the developer
and suggest setting it up. Don't silently skip validation.

## Before Saying "Done"

- All quality gates pass on changed files
- The implementation matches what the developer asked for
- You haven't introduced new dependencies without mentioning them
- You haven't changed files outside the scope without explaining why
- If docs/ exists and this changes behavior, note that docs may need updating

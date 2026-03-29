# Hook: Post-Implementation

## Purpose

After implementing a feature or change, verify the work is complete and
the project's knowledge stays in sync with the code.

## When to Apply

Every time you finish implementing something — before telling the developer it's done.

## Steps

### 1. Quality gates

Run on changed files:
```
make format FILES="<changed files>"
make lint FILES="<changed files>"
make typecheck FILES="<changed files>"
make test FILES="<changed files>"
```

Fix issues before proceeding. Don't report "done" with failing gates.

### 2. Self-review

Before showing the developer, check your own work:
- Did you implement what was asked, or did you drift?
- Did you change files outside the original scope? If so, explain why.
- Did you introduce new dependencies? Flag them.
- Does the code follow the patterns in conventions.md?
- Would this make sense to another developer reading it for the first time?

### 3. Documentation check

If the project has docs/:
- Does this change affect anything described in architecture.md?
- Does this introduce a new decision that belongs in decisions.md?
- Does this change behavior for any persona in vision.md?

If yes, tell the developer: "This change affects [doc]. Want me to update it?"

Don't update docs silently. The developer should know what's changing and why.

### 4. Summary to developer

Tell them:
- What you implemented (brief)
- What files changed
- All quality gates passed (or what's still failing and why)
- Any docs that may need updating
- Any concerns or tradeoffs you noticed

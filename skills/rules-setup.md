# Skill: Rules Setup

## Purpose

Teach developers that Claude Code rules exist and when they become useful.
Don't push splitting — just plant the seed so they know the option is there.

## When to Trigger

- Developer asks about organizing CLAUDE.md or mentions it's getting long
- CLAUDE.md has grown past ~50 lines with file-specific sections
- You notice repeated file-specific conventions that could be scoped

**Guard: If CLAUDE.md is under 50 lines, don't suggest splitting.** Just mention
that rules exist: "By the way, when your CLAUDE.md grows, you can scope
file-specific instructions with `.claude/rules/`. Not needed yet."

## Context for the Developer

Explain this conversationally, not as a lecture:

> Everything in CLAUDE.md loads into every session -- even when it's irrelevant.
> Your React component conventions load when Claude is writing a database migration.
> That wastes context and dilutes the important stuff.
>
> Rules fix this. They're markdown files in `.claude/rules/` that only load
> when Claude touches matching files. Same instructions, better targeting.

## How Rules Work

Show the developer this structure:

```
.claude/
  rules/
    react-components.md      <- loads only for *.tsx files
    api-routes.md             <- loads only for routes/api/**
    database-migrations.md    <- loads only for migrations/**
    tests.md                  <- loads only for *.test.* files
```

Each file has a frontmatter header that controls when it loads:

```markdown
---
description: React component conventions
globs: "**/*.tsx"
---

- Use named exports, no default exports
- Props interface above the component
- Keep components under 150 lines -- extract hooks
```

The `globs` field supports standard patterns:
- `**/*.tsx` -- any .tsx file anywhere
- `src/api/**` -- anything under src/api/
- `**/*.test.*` -- any test file
- `**/migrations/**` -- any migrations directory

## What to Do

### Step 1: Read the existing CLAUDE.md

Look at the project's CLAUDE.md (or whatever file Claude Code loads at session start).

### Step 2: Identify file-specific sections

Look for instructions that only matter for certain file types. Common patterns:

- **Component conventions** -- rule scoped to `**/*.tsx` or `**/*.vue`
- **API/route patterns** -- rule scoped to `**/api/**` or `**/routes/**`
- **Test conventions** -- rule scoped to `**/*.test.*` or `**/*.spec.*`
- **Database/migration rules** -- rule scoped to `**/migrations/**`
- **Styling rules** -- rule scoped to `**/*.css` or `**/*.scss`
- **Config file handling** -- rule scoped to specific config files
- **Documentation standards** -- rule scoped to `**/*.md` or `docs/**`

### Step 3: Propose the moves

For each section identified, suggest to the developer:

- What to extract
- What glob to use
- What stays in CLAUDE.md (project overview, stack, build commands, universal rules)

Format your suggestion like:

> **Move to `.claude/rules/tests.md`** (glob: `**/*.test.*`):
> Your testing conventions about using Vitest, preferring `it()` over `test()`,
> and the mock patterns. These only matter when writing tests.
>
> **Keep in CLAUDE.md:**
> `make test` command, project overview, stack description.

### Step 4: Create the rule files

After the developer approves, create the `.claude/rules/` directory and the
agreed-upon rule files. Keep each one short -- under 20 lines is ideal.

### Step 5: Trim CLAUDE.md

Remove the migrated sections from CLAUDE.md. It should get noticeably shorter.
Confirm with the developer before deleting anything.

## What Stays in CLAUDE.md

Always keep these in CLAUDE.md (they apply to every session):

- Project name and one-line description
- Tech stack
- Build/dev/test commands
- Universal constraints (e.g., "never commit .env")
- Context gates pointing to docs

## Rules of Thumb

- Add a rule only when you notice Claude making the same mistake twice
- Keep each rule file under 20 lines
- If a rule applies to everything, it belongs in CLAUDE.md, not in rules
- Commit rules to version control -- the whole team benefits
- Don't over-scope: one concern per rule file

# Skill: CLAUDE.md Optimization

## Purpose

CLAUDE.md is read by Claude Code at the start of every session. Every line in it
costs context window space. A bloated CLAUDE.md means less room for actual work.

This skill teaches you to audit, structure, and optimize CLAUDE.md so it stays
lean while still giving Claude Code the right information at the right time.

## The Problem

CLAUDE.md tends to grow. Developers add useful things over time:
architecture overview, conventions, decisions, API patterns, testing approach,
deployment notes, component guidelines...

Eventually it's 5 pages long and Claude Code reads all of it before the developer
even types "fix the button color." That's wasted context.

## The Principle: Load What You Need, When You Need It

CLAUDE.md should contain ONLY:
- What Claude Code needs for EVERY task (project identity, stack, make commands)
- Pointers to deeper docs with clear gates for WHEN to load them

Everything else lives in separate files that get loaded on demand.

## Auditing an Existing CLAUDE.md

When you find a long CLAUDE.md, evaluate each section:

**Ask: "Does Claude Code need this for a one-line CSS fix?"**
- Yes → stays in CLAUDE.md
- No → extract to a separate doc with a gate

### What stays (always-loaded context):

- Project name and what it does (2-3 sentences max)
- Tech stack (one line)
- Make commands reference (short list)
- Developer profile loading instruction
- Shipwright reference
- Pointers to deeper docs with gates

### What gets extracted:

Anything that's only needed for specific types of work.

## Gate Pattern

A gate is a conditional loading instruction. It tells Claude Code:
"Read this file ONLY when you're about to do this type of work."

Structure in CLAUDE.md:

```markdown
## Quick Reference

[project name] — [one-line description]
Stack: Next.js 14, TypeScript, Prisma, PostgreSQL
Run: make lint | make test | make dev | make check

## Before You Start

Load your developer profile:
  whoami → read .claude/developers/{username}.md

## Context Gates

Read these ONLY when relevant to your current task:

**Before writing or modifying code:**
  read docs/conventions.md — coding patterns, naming, component structure
  read docs/architecture.md — system structure, data flow, key boundaries

**Before making a technical decision:**
  read docs/decisions.md — past decisions and rejected alternatives

**Before working on a specific feature:**
  read docs/features/{feature-name}.md — if it exists

**Before writing or modifying tests:**
  read docs/testing.md — testing patterns, what to test, how to mock

**Before working on API endpoints:**
  read docs/api-patterns.md — request/response format, auth, error handling

**Before working on UI components:**
  read docs/component-guide.md — design system, shared components, patterns

**Before deploying or writing CI config:**
  read docs/deployment.md — environments, pipeline, secrets
```

## Why Gates Work

1. **Context efficiency** — a CSS fix loads only conventions.md (~1 page)
   instead of everything (~10 pages)
2. **Freshness** — docs loaded on demand are always current.
   Inline content in CLAUDE.md can go stale without anyone noticing.
3. **Clarity** — Claude Code knows exactly WHY it's loading something.
   "I'm about to write code, so I need conventions" vs "here's everything, good luck."
4. **Composability** — complex tasks load multiple gates. Simple tasks load few or none.

## Splitting an Existing CLAUDE.md

When you find a CLAUDE.md that's too long (rule of thumb: more than ~40 lines
or more than one screenful):

1. Identify sections that are task-specific
2. Extract each to its own file in docs/
3. Replace the section in CLAUDE.md with a gate pointer
4. Keep ONLY the universal stuff inline

Tell the developer what you're doing and why:
"Your CLAUDE.md is pretty long — Claude Code reads all of it every session
even for simple tasks. I can split it into smaller docs that get loaded
only when needed. Same information, less context waste. Want me to do that?"

## Example: Before and After

### Before (bloated CLAUDE.md):

```markdown
# My Project

A Next.js e-commerce platform...

## Architecture
[40 lines about system structure]

## Conventions
[30 lines about coding patterns]

## API Patterns
[25 lines about endpoints]

## Testing
[20 lines about test approach]

## Decisions
[50 lines about past decisions]
```

Total: ~165 lines loaded every session.

### After (lean CLAUDE.md with gates):

```markdown
# My Project

Next.js e-commerce platform. TypeScript, Prisma, PostgreSQL.
Run: make lint | make test | make dev | make check

## Before You Start

Load developer profile: whoami → .claude/developers/{username}.md

## Context Gates

Before writing code: read docs/conventions.md
Before technical decisions: read docs/decisions.md
Before API work: read docs/api-patterns.md
Before writing tests: read docs/testing.md
For system overview: read docs/architecture.md
```

Total: ~15 lines loaded every session. Details loaded on demand.

## Maintaining Lean CLAUDE.md

When you or the developer want to add something to CLAUDE.md, ask:
"Is this needed for every single task, or only for specific types of work?"

If specific → create or update a gated doc instead.

Set a soft limit: CLAUDE.md should be under 40 lines. If it's growing past that,
it's time to extract and gate.

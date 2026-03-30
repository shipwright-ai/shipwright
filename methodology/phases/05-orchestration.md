# Phase 5: Orchestration

## What This Is

A structured workflow where Claude stops being a single agent doing everything
and becomes a coordinated team: product thinker, developer, reviewer, critic,
doc-writer — each with their own role, memory, and checklist.

Instead of "Claude, build me OAuth" → hope for the best, you get:

```
Capture idea → Plan with agent team → You approve → Execute with TDD →
Two-stage review → Docs updated automatically
```

Every step is tracked. Every decision is recorded. Every agent recalls what it
learned last time. Documentation stays current because a dedicated agent updates
it after every execution.

## What It Brings to Your Project

### Problems it solves

**"Claude started coding before we agreed on what to build"**
Plans go through a product agent and critic before reaching you. You see the
approach, trade-offs, risks, and task breakdown. Nothing executes until you say go.

**"The code works but it's overengineered / misses the point"**
A critic agent challenges assumptions during planning. Escalates from
contrarian (round 4) to simplifier (round 6) to ontologist (round 8) if the
team keeps going in circles. Catches overbuilding before it reaches code.

**"Reviews catch things too late"**
Two-stage review: first checks if the code matches the spec (did we build what
we planned?), then checks code quality (did we build it well?). A slop-cleaner
pass runs between execution and review — dead code, duplication, naming issues
get caught automatically.

**"Documentation is always stale"**
A doc-writer agent updates feature docs, architecture docs, and decision records
after every execution. Tags propagate from ideas through plans to work-items,
so the doc-writer knows exactly which docs are affected.

**"Claude forgets what it learned"**
Each agent stores personal learnings (recalled at every spawn) and project-wide
insights (discoverable by all agents). Decisions are stored standalone and
outlive the ideas that created them — available for all future planning.

**"I want to run things autonomously sometimes, manually other times"**
Three gate modes:
- **Manual** — you approve at every checkpoint
- **Semi-auto** — you approve plans, execution and review run on their own
- **Full-auto** — everything self-resolves, hard stops only for security/destructive ops

### What changes in your workflow

| Before (Phases 1-3) | After (Phase 5) |
|---|---|
| One Claude doing everything | Specialized agents with clear roles |
| Ideas captured, implemented ad-hoc | Ideas planned by agent team, reviewed by critic |
| You review the code | Two-stage automated review + your approval on plans |
| Docs updated manually (or not at all) | Doc-writer agent updates after every execution |
| Context lost between sessions | Agents recall their memories, decisions persist |
| Same mistakes repeated | Learnings accumulate, each session gets smarter |

### The agent team

| Agent | Role | When active |
|---|---|---|
| **Product** | Coordinates planning, validates against project vision | Planning |
| **Critic** | Challenges assumptions, flags overbuilding | Planning |
| **Developer** | Implements with TDD, follows work-item spec | Execution |
| **Reviewer** | Spec compliance + code quality review | After execution |
| **Doc-writer** | Updates affected docs and captures screenshots | After review passes |

Additional agents recommended based on your project:
- **UX** — if frontend-heavy, validates user experience during planning
- **Tech Lead** — if architecturally complex, reviews technical approach
- **Security** — if handling auth, payments, or sensitive data

## Requirements

- **Phases 1-3 should be complete** — Makefile, CLAUDE.md workflow rules, and
  idea capture form the foundation that orchestration builds on.
- **Brain must be set up** — orchestration runs entirely on Brain. If it's not
  configured yet, now is the time. Here's what you're getting:

### Why Brain

Brain is persistent memory for Claude Code — but it's more than a note-taking tool.

**Everything is markdown files.** No database, no server to maintain. All memories
live in `docs/` as human-readable markdown, versioned with git alongside your code.
You can read, edit, or delete them directly. Claude uses MCP tools to create and
search them. Setup takes 2 minutes: `npx shipwright-brain init`.

**Hybrid search finds things by meaning.** Ask "how do we handle authentication?"
and Brain finds memories about tokens, sessions, login flows — even without exact
keyword matches. Local embeddings (no API calls, fully offline) combine with keyword
search for accurate results.

**Progress tracking from checkboxes.** Every `- [ ]` and `- [x]` in a memory file
is tracked automatically. Status (not started / in progress / done) derives from
checkboxes — no fields to update manually. Progress rolls up from work-items to
plans to ideas, so you always see the big picture.

**Duplicate detection before creation.** Brain checks for similar memories before
creating new ones. 90%+ similar → blocked with merge suggestion. 60-89% → created
but auto-linked as related. No accidental redundancy.

**Agent memory across sessions.** Each agent (developer, reviewer, critic) stores
what it learned. Next session, those learnings load automatically at spawn. The
developer agent remembers "async tests need promise flushing in this project."
The reviewer remembers "this team prefers explicit error types over generic catches."
Agents get better the more you use them.

**Developer profile that adapts.** Brain observes how you prefer to work — terse
or detailed responses, how much autonomy you want, which topics you care about.
Every agent loads this profile so the experience feels consistent.

**A graph of connected knowledge.** Decisions reference the ideas that created them.
Work-items link back to plans. Architecture docs reference features. All references
are bidirectional and auto-maintained. Delete a memory and back-references clean up.

**Screenshots with click automation.** Capture UI state at mobile (375px), tablet
(768px), and desktop viewports. Click through login flows or modals before capturing.
Screenshots attach to memories as living documentation.

**A web UI to see what Claude sees.** Browse memories by kind, filter by tags and
status, view the connection graph, search with relevance scores. Auto-refreshes as
Claude works. Open `http://localhost:3111` and you see your project's knowledge base.

Brain is the foundation that makes orchestration possible — and it's useful on its
own even without orchestration. Every memory, decision, and learning your team
creates is searchable, connected, and persistent.

## How to start

Tell the developer:

> "Orchestration adds a team of specialized agents to your workflow — a product
> thinker who plans with a critic, a developer who implements with TDD, a reviewer
> who checks the work, and a doc-writer who keeps everything current.
>
> You still control the level of autonomy — from approving every step to letting
> it run on its own with hard stops only.
>
> It takes about 10 minutes to set up. Want to try it?"

If yes:

1. Verify Brain is available — check `.mcp.json` for brain in `mcpServers`.
   If missing, set it up first (see Phase 1 pre-checks).

2. Run orchestration setup:
   Read `orchestration/setup.md` and follow it.

   Setup will:
   - Discover your project's stack, conventions, and preferences
   - Recommend which agents to activate
   - Generate project-specific format guides in Brain
   - Generate agent prompts in `.claude/agents/`
   - Update CLAUDE.md with orchestration workflow
   - Store configuration in Brain for future re-runs

3. Update `.claude/shipwright.md` with phase: 5, step: completed.

If no or not right now → that's fine. Phases 1-3 are a solid workflow on their own.
Orchestration is there when the project grows or the developer wants more structure.

Note in `.claude/shipwright.md`:
```
phase5_orchestration: declined
phase5_reason: <what the developer said — "not now", "too complex", etc.>
```

On future re-runs (via `update.md`), if orchestration was declined:
- Don't re-offer automatically
- Only bring it up if the developer explicitly asks or if pain points
  from discovery match what orchestration solves (e.g., "docs are always stale",
  "Claude keeps building the wrong thing", "I want more structured reviews")

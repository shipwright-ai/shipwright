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

- **Brain must be set up** — orchestration uses Brain for all memory, search,
  progress tracking, and agent recall. If Brain isn't configured, this phase
  can't work.
- **Phases 1-3 should be complete** — Makefile, CLAUDE.md workflow rules, and
  idea capture form the foundation that orchestration builds on.

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

3. Update last-sync.md with phase: 5, step: completed.

If no or not right now → that's fine. Phases 1-3 are a solid workflow on their own.
Orchestration is there when the project grows or the developer wants more structure.

Note in last-sync.md:
```
phase5_orchestration: declined
phase5_reason: <what the developer said — "not now", "too complex", etc.>
```

On future re-runs (via update.md), if orchestration was declined:
- Don't re-offer automatically
- Only bring it up if the developer explicitly asks or if pain points
  from discovery match what orchestration solves (e.g., "docs are always stale",
  "Claude keeps building the wrong thing", "I want more structured reviews")

# Shipwright

A la carte toolkit for Claude Code. Best practices, persistent memory, and agent-team planning — pick what fits your project.

## Install

**Plugin (marketplace):**
```
/plugin install shipwright
/shipwright:setup
```

**Git clone:**
```bash
git clone https://github.com/shipwright-ai/shipwright.git
# In your project, in Claude Code:
Read ../shipwright/methodology/setup.md and onboard this project
```

## Three Layers

Setup guides you through each. Stop whenever you want.

### Layer 1: Methodology

Best practices for any project. No dependencies.

- CLAUDE.md with workflow rules and context gates
- Makefile with standard targets (`make test`, `make lint`, `make dev`)
- Developer profile for personalized workflow
- Quality gates (lint, test before commit)
- Context compaction for long sessions

### Layer 2: Brain (optional, recommended)

Persistent, searchable memory via MCP. Needs npm (for npx).

- **Custom kinds** — ideas, decisions, features, bugs, or whatever fits your project
- **Dynamic tags** — organize however makes sense, no predefined taxonomy
- **Format guides per kind** — each kind has its own template that Brain uses on creation. Tag-aware: `ideas` tagged `plan` get a different format than plain `ideas`
- **Sections** — drop .md files next to memory.md, Brain parses them (checklists, progress, agent assignment)
- **Progress from checkboxes** — no status field. Brain derives not-started / in-progress / done automatically
- **Semantic + keyword search** — finds related work across sessions, even without exact matches
- **Screenshots** — capture UI at any breakpoint (375 mobile, 768 tablet, 1280 desktop)
- **Agent recall** — agents remember learnings across sessions, loaded at every spawn
- **Duplicate detection** — Brain blocks creation if 90%+ similar memory exists
- **Self-building graph** — refs are always bidirectional, connections auto-detected
- **MCP responses steer Claude** — every tool response includes next steps and reminders. Enforcement that doesn't get compacted away

Powered by [Shipwright Brain](https://github.com/shipwright-ai/shipwright-brain).

Browse what Claude sees with [Shipwright UI](https://github.com/shipwright-ai/shipwright-ui): `make brain-ui` → http://localhost:3111

### Layer 3: Orchestration (optional, needs Brain)

Structured planning with agent teams.

- `/capture` — capture ideas into Brain
- `/plan` — plan with team review (product + critic), present for approval
- `/execute` — execute work-items with TDD, two-stage review, docs update

**Agent team** (discovery recommends based on your project):

| Agent | Role | When |
|---|---|---|
| Product | Coordinates planning, validates against vision/personas | Planning |
| Critic | Challenges assumptions, flags overbuilding | Planning |
| Developer | Implements with TDD, follows work-item spec | Execution |
| Reviewer | Spec compliance + code quality review | After execution |
| Doc-writer | Updates feature docs, captures screenshots | After review |

Additional agents recommended per project: UX (frontend), Tech Lead (complex architecture), Security (auth/APIs).

**Gate modes** — same system, different autonomy:
- Manual: review everything
- Semi-auto: review plans only
- Full-auto: hard stops only (blocked, security, destructive)

**Data model** — everything is Brain memories with tags and nesting:
```
docs/ideas/feature-x/memory.md                             idea
  └── plan/memory.md                                        plan (tag: plan)
       └── task-1/                                          work-item (tag: work-item)
            ├── memory.md                                   what & why
            ├── 1_execution_developer.md                    developer checklist
            ├── 2_review_reviewer.md                        reviewer checklist
            └── 3_documentary_documenter.md                 doc-writer checklist
```

## Structure

```
shipwright/
├── skills/setup/SKILL.md        /shipwright:setup entry point
├── methodology/                 Layer 1 + 2
│   ├── setup.md                 onboarding conversation
│   ├── phases/                  5 progressive phases
│   ├── skills/                  12 teaching skills
│   ├── hooks/                   quality gates
│   └── community-insights.md   learnings from developers
├── orchestration/               Layer 3
│   ├── setup.md                 orchestration setup
│   ├── skills/                  teaching files (ideation, planning, execution)
│   ├── agents/                  5W archetypes (product, critic, developer, reviewer, doc-writer)
│   └── docs/                    design decisions and architecture
├── local/                       developer-local (gitignored)
├── CLAUDE.md                    meta-guidance for shipwright itself
└── .claude-plugin/plugin.json   marketplace manifest
```

## Three Scopes

| Scope | Where | Who benefits |
|-------|-------|-------------|
| **Team** | committed to shipwright | all developers via git pull |
| **Developer** | shipwright/local/ (gitignored) | just you, across all projects |
| **Project** | your project's docs/ | whoever works on that project |

## Key Principles

- **Files, not databases** — everything is markdown on disk
- **Checklists with exact `brain.tool()` calls** — not prose essays
- **Brain MCP responses steer behavior** — enforcement that doesn't get compacted away
- **Skills teach, not template** — Claude generates project-specific output
- **Ideas are living, work-items are immutable** — new requirements = new work-items
- **Ideology stays, patterns evolve** — WHY is stable, HOW changes with models

## Inspirations

- [superpowers](https://github.com/obra/superpowers) — brainstorming, TDD, subagent execution
- [oh-my-claudecode](https://github.com/yeachan-heo/oh-my-claudecode) — deep-interview, trace, slop-cleaner

## License

MIT

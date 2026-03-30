# Shipwright

Stop Claude Code from guessing. Shipwright gives it a structured workflow: capture ideas before coding, verify plans with the developer, track progress with checkboxes, and remember decisions across sessions.

Clone once, reference from any project. Nothing gets copied in.

## The Problem

Claude Code is powerful but chaotic. Without structure it:
- Starts coding before understanding the full scope
- Forgets decisions made earlier in the session
- Creates duplicate work across sessions
- Doesn't track what's done vs what's pending
- Loses context as conversations grow

## What Shipwright Does

**Structured workflow** -- Capture, Refine, Implement, Update. Every feature starts as a tracked idea with checkboxes. Claude discusses the plan with the developer before writing code. After implementation, it updates progress and context.

**Skills** -- stack-agnostic guidance files that teach Claude patterns, not templates. How to set up a Makefile, when to split CLAUDE.md into rules, how to capture ideas, when to compact context.

**Brain** ([shipwright-brain](https://github.com/shipwright-ai/shipwright-brain)) -- persistent memory via MCP. Ideas, decisions, architecture, bugs -- all as markdown with frontmatter. Brain teaches Claude the right format for each kind, detects duplicates via embeddings, auto-links related memories, and tracks progress through the tree.

**Brain UI** ([brain-ui](https://github.com/shipwright-ai/brain-ui)) -- visual layer for humans. Browse memories, filter by tags and status, view the connection graph, see progress badges. Auto-refreshes as Claude works.

## Key Workflows

### Capture, Refine, Implement, Update

```
Developer: "We need OAuth login"
    |
    v
1. Capture -- Claude creates Brain idea with checklist steps
2. Refine  -- Developer and Claude discuss, adjust scope, iterate
3. Implement -- Only after developer says "go"
4. Update  -- Check off steps, update context, capture decisions
```

### What Should I Do Next?

Claude searches Brain for in-progress ideas first, then not-started ones. The developer picks what to work on. Progress is always visible.

### Smart Context

Claude compacts context at natural boundaries -- after commits, between tasks. Long sessions stay sharp instead of drowning in old context.

## Quick Start

```bash
# Clone shipwright next to your projects
git clone https://github.com/shipwright-ai/shipwright.git

# In your project, set up Brain
npx shipwright-brain init

# Start a Claude Code session and run:
# "Read ../shipwright/setup.md and onboard this project"
```

Onboarding takes 5 minutes. It sets up a Makefile, CLAUDE.md with workflow rules, context compaction, and developer profile. Zero disruption to existing workflows.

## How It Works

```
shipwright/           <- methodology (this repo)
  skills/             <- what Claude should know
  hooks/              <- quality gates
  setup.md            <- onboarding conversation

shipwright-brain/     <- memory (separate repo)
  src/                <- MCP server + API
  docs/               <- all memories as markdown

your-project/         <- where you work
  .mcp.json           <- connects Brain
  CLAUDE.md           <- project-specific workflow rules
```

Shipwright lives outside your project. Claude reads it for guidance, then applies it to your specific stack and conventions. Skills teach principles -- Claude builds the right thing for each project.

## Three Scopes

| Scope | Location | Shared with |
|-------|----------|-------------|
| **Team** | committed to shipwright | all developers via git |
| **Developer** | local/ (gitignored) | just you, across projects |
| **Project** | in your project's docs/ | whoever works on that project |

Learnings flow to the right level. Never up, never sideways.

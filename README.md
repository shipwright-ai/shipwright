# Shipwright

Stop Claude Code from guessing. Shipwright gives it a structured workflow: capture ideas before coding, refine plans with the developer, track progress with checkboxes, and remember everything across sessions.

Clone once, reference from any project. Nothing gets copied in.

## The Problem

Claude Code is powerful but chaotic without structure:
- Starts coding before understanding the full scope
- Forgets decisions made earlier or in previous sessions
- Creates duplicate work because it doesn't know what already exists
- Doesn't track what's done vs what's pending
- Loses context as conversations grow

## What You Get

### A workflow that sticks

Every feature follows: **Capture -> Refine -> Implement -> Update**

```
Developer: "We need OAuth login"

1. Capture   -- Claude creates a tracked idea with checklist steps
2. Refine    -- You and Claude discuss scope, adjust, iterate
3. Implement -- Only after you say "go"
4. Update    -- Claude checks off steps, updates context, captures decisions
```

This isn't documentation. It's how Claude actually works in your project -- enforced through CLAUDE.md rules, persistent memory, and MCP tool responses that remind Claude every time it queries Brain.

### Memory that persists

[Shipwright Brain](https://github.com/shipwright-ai/shipwright-brain) gives Claude persistent memory via MCP:

- **Ideas, decisions, bugs, architecture** -- all tracked as markdown with checkboxes
- **Progress visible at a glance** -- Brain auto-derives status from checkboxes (not started / in progress / done)
- **"What should I do next?"** -- finds in-progress work first, then not-started
- **No duplicates** -- Brain detects similar memories before creating new ones
- **Smart search** -- hybrid keyword + semantic search finds related work even without exact matches
- **Self-building graph** -- refs are always bidirectional, content links auto-detected

### A UI for humans

[Brain UI](https://github.com/shipwright-ai/brain-ui) lets you see what Claude sees:

- Browse memories by kind with progress badges
- Filter by tags, status, sort by date or name
- View the connection graph between decisions, ideas, and features
- Auto-refreshes as Claude works

## Quick Start

### 1. Clone shipwright (once)

```bash
git clone https://github.com/shipwright-ai/shipwright.git
```

### 2. Set up Brain in your project

```bash
cd your-project
npx shipwright-brain init
```

### 3. Onboard your project (5 minutes)

Open Claude Code in your project and say:

```
Read ../shipwright/setup.md and onboard this project
```

Claude walks you through it -- sets up a Makefile, CLAUDE.md with workflow rules, context compaction, and developer profile. Every step is discussed with you first. Nothing happens without your approval.

## What Onboarding Sets Up

| Step | What | Why |
|------|------|-----|
| Project understanding | Claude reads your codebase | So everything fits your actual stack |
| Makefile | Consistent `make test`, `make lint`, `make dev` | One interface for all tools |
| CLAUDE.md | Workflow rules + context gates | Claude knows how to work in your project |
| Context compaction | Smart summarization between tasks | Long sessions stay focused |
| Developer profile | Your preferences and work style | Claude adapts to you |

## How It Works

```
shipwright/           <- methodology (this repo, cloned once)
  skills/             <- what Claude should know
  hooks/              <- quality gates
  setup.md            <- onboarding conversation

shipwright-brain/     <- memory (npm package)
  src/                <- MCP server + API
  docs/               <- all memories as markdown

your-project/         <- where you work
  .mcp.json           <- connects Brain
  CLAUDE.md           <- project-specific workflow rules
  docs/               <- Brain's memory files
```

Shipwright lives outside your project. Claude reads it for guidance, then applies it to your specific stack and conventions. Skills teach principles -- Claude builds the right thing for each project.

## Three Scopes

| Scope | Where | Who benefits |
|-------|-------|-------------|
| **Team** | committed to shipwright | all developers via git pull |
| **Developer** | shipwright/local/ (gitignored) | just you, across all projects |
| **Project** | your project's docs/ | whoever works on that project |

New learning? It goes to the right level. Team insights get committed to shipwright. Personal preferences stay local. Project decisions stay in the project.

# Shipwright

A methodology toolkit that improves AI-assisted web development. Clone once, reference from your projects. Never copied into target projects.

## What It Does

Shipwright teaches Claude Code how to work well. Skills guide behavior, hooks enforce quality, and Brain gives persistent memory across sessions.

- **Skills** -- stack-agnostic guidance files that teach Claude patterns, not templates
- **Hooks** -- automated gates that run before/after actions (pre-commit, post-implement)
- **Brain** -- persistent memory for ideas, decisions, architecture, bugs, and learnings

## With Brain

[Shipwright Brain](https://github.com/shipwright-ai/shipwright-brain) is the memory layer. When connected:

- **Capture before code** -- every feature/fix starts as a Brain idea with checkboxes
- **Progress tracking** -- checkboxes auto-derive status (not started / in progress / done)
- **5W context** -- every memory gets structured context: Why, What, Who, How
- **Format guides** -- Brain teaches Claude the right format for each kind of memory
- **Aggregate progress** -- parent ideas roll up progress from all sub-memories
- **Search by status** -- "what should I do next?" finds in-progress then not-started work
- **Screenshots with interaction** -- capture specific UI states by clicking through menus
- **Tags grow organically** -- no predefined categories, team vocabulary emerges from usage

## With Brain UI

[Brain UI](https://github.com/shipwright-ai/brain-ui) is the visual layer for humans:

- Browse memories by kind with progress badges
- Filter by tags and status with faceted search
- View the memory graph -- connections between decisions, ideas, and features
- Everything auto-refreshes as Claude works

## Quick Start

```bash
# In your project
npx shipwright-brain init

# Add shipwright skills to your Claude Code session
# Claude reads ../shipwright/setup.md to onboard your project
```

Onboarding takes 5 minutes. Zero disruption to existing workflows.

## How It Works

```
shipwright/           <- methodology (this repo)
  skills/             <- what Claude should know
  hooks/              <- quality gates
  setup.md            <- onboarding script

shipwright-brain/     <- memory (separate repo)
  src/                <- MCP server + API + UI
  docs/               <- all memories as markdown

your-project/         <- where you work
  .mcp.json           <- connects Brain
  CLAUDE.md           <- project-specific rules
```

Shipwright lives outside your project. Claude reads it for guidance, then applies it to your specific stack and conventions. Skills teach principles -- Claude builds the right thing.

## Three Scopes

- **Team** (committed to shipwright) -- benefits all developers
- **Developer** (local/, gitignored) -- personal preferences across projects
- **Project** (in your project's docs/) -- specific to one codebase

Learnings flow to the right level. Never up, never sideways.

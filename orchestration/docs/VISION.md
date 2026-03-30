# Shipwright Orchestration — Vision

## Problem

AI-assisted development is powerful but chaotic without structure:

1. **Autonomous agents make wrong assumptions** — they plan and execute without verifying with the developer, leading to overcomplicated, incorrect implementations
2. **Knowledge drifts** — feature docs, decisions, architecture docs go stale because nobody updates them after execution. Next planning cycle works with outdated context
3. **No organized workflow** — ideas get lost, plans aren't reviewed, execution happens without tracking, learnings vanish between sessions
4. **Complexity creep** — previous attempt (shipwright-on-steroids) solved orchestration but required SurrealDB, SvelteKit app, 60+ MCP tools, complex coordinator chains. The cure was worse than the disease

## Solution

A lightweight orchestration layer that:

- **Uses Brain as the only infrastructure** — no database, no app server, no custom MCP. Brain already handles file-based memory, search, screenshots, attachments, progress tracking, agent recall
- **Follows shipwright's adoption pattern** — developer picks what they need, phased onboarding, discovery-driven setup that adapts to project and developer
- **Enforces human checkpoints** — plans are always presented to developer before execution. No code without approval
- **Keeps knowledge current** — doc-writer agent updates affected docs after every execution. Tags propagate from ideas through work-items to decisions, making everything discoverable
- **Learns over time** — agents recall their memories at spawn, project accumulates decisions and learnings, each session makes the next one smarter

## Core Philosophy

1. **Files, not databases** — everything is a markdown file on disk. Brain creates and searches them. Claude reads and writes them directly
2. **Checklists, not prose** — skills are step-by-step checklists with exact `brain.tool()` calls. Claude follows recipes, not essays
3. **Brain tool responses steer behavior** — MCP responses include next steps, reminders, and forced choices. This is the enforcement mechanism, not CLAUDE.md rules that get compacted away
4. **Ideology stays, patterns evolve** — the WHY (human checkpoints, knowledge persistence, learning agents) is stable. The HOW (specific tool calls, agent prompts, skill structure) changes as models improve
5. **Adopt incrementally** — start with idea capture + one planning skill. Add agents and workflows as needed. Never all-or-nothing

## What This Is NOT

- Not a replacement for Brain — Brain is the foundation, orchestration adds workflows on top
- Not a rigid pipeline — developer configures which skills and agents are active
- Not autonomous by default — developer controls the level of autonomy (manual / semi-auto / full-auto). Hard stops always apply for security and destructive operations
- Not infrastructure — no servers, no databases, no builds. Just markdown files

## Lineage

- **Shipwright** — methodology, phased adoption pattern, developer discovery, quality gates
- **Shipwright Brain** — persistent file-based memory, MCP tools, screenshots, attachments, agent recall, progress tracking
- **Shipwright-on-steroids** — lifecycle ideas (idea/plan/execute), agent team roles, coordinator pattern, work-item concept. Too complex, but validated the workflow
- **Superpowers** (github.com/obra/superpowers) — brainstorming with hard gates, bite-sized task decomposition, TDD iron law, subagent-driven execution with two-stage review, systematic debugging. Great ideas, wrong delivery format (prose essays vs checklists)
- **oh-my-claudecode** (github.com/yeachan-heo/oh-my-claudecode) — deep-interview with ambiguity scoring, trace-based debugging, challenge agent escalation, slop-cleaner, visual-verdict. Great patterns, wrong delivery mechanism (848 TypeScript files + npm package)

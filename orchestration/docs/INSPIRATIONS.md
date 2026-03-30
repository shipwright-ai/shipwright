# Inspirations & References

## Repositories

### superpowers
**URL:** github.com/obra/superpowers
**What it is:** Skill-based AI development workflow framework for Claude Code. Brainstorming, TDD, subagent-driven execution, systematic debugging.
**What we learned:**
- Brainstorming with Socratic dialogue and hard gate (no code until design approved)
- Bite-sized task decomposition (2-5 min tasks with exact file paths and code)
- Two-stage review: spec compliance THEN code quality (catches different failure modes)
- TDD iron law: write failing test first, verify it fails, then implement
- Systematic debugging: 4 phases, "3+ failed fixes = architectural problem"
- Self-review before external review (catches 3-5 bugs in 30 seconds)
- Fresh subagent per task prevents context confusion accumulation

### oh-my-claudecode
**URL:** github.com/yeachan-heo/oh-my-claudecode
**What it is:** Multi-agent orchestration layer for Claude Code. 19 agents, 30+ skills, hook-driven automation, persistence loops.
**What we learned:**
- Deep-interview: Socratic Q&A with mathematical ambiguity scoring across weighted dimensions. Gates output until ambiguity <= 20%. Ask ONE question at a time, target WEAKEST dimension.
- Tiered verification: match reviewer model (haiku/sonnet/opus) to risk level of changes
- Ralph persistence loop: work continues until ALL acceptance criteria verified, prevents silent partial completion
- Notepad wisdom: per-plan learnings/decisions/issues capture
- Skill composition layers: Guarantee + Enhancement + Execution stack
- Trace: 3 parallel investigation lanes (code-path, config/env, measurement) for evidence-driven debugging
- Deep-dive: trace + deep-interview combined — investigate THEN ask informed questions
- Challenge agent escalation: Contrarian @round4, Simplifier @round6, Ontologist @round8
- Concrete signal detection: skip brainstorming when request has file paths, function names, error messages
- AI-slop-cleaner: 5-pass regression-safe cleanup bounded to changed files only
- Visual-verdict: screenshot comparison with 0-100 score and structured verdict
- Learner: disciplined memory extraction with quality gate (non-Googleable, context-specific, actionable, hard-won)
- UltraQA: QA loop with circuit breaker (max 5 cycles, exit if same failure 3x)
- RALPLAN-DR consensus: short mode (default) vs deliberate mode (high-risk), max 5 iterations

### shipwright-on-steroids
**URL:** (local, predecessor project)
**What it is:** Full orchestration system with SurrealDB, SvelteKit, 60+ MCP tools, complex coordinator chains.
**What we learned:**
- Work-item directory pattern: memory.md + execution.md + review.md + documentation.md side by side
- Coordinator pattern with SendMessage for planning team dialogue
- Agent memories loaded at spawn via Brain recall
- Status-driven routing based on work-item phase
- Templates for every level: phase, milestone, task, bug, idea, god-mode session
- Execution summary for restart recovery
- Review findings with severity levels (CRITICAL/IMPORTANT/MINOR/NOTE)
- What NOT to do: 60+ MCP tools, SurrealDB, SvelteKit app = too much infrastructure

### shipwright
**URL:** (sibling project)
**What it is:** Methodology toolkit for Claude Code. Phased onboarding, developer discovery, quality gates.
**What we learned:**
- Phased adoption pattern: start with basics, add complexity as needed
- Discovery conversation: analyze project, recommend what fits
- Re-runnable setup that evolves with project knowledge
- Skills as teaching files, not templates to copy
- Developer profiles for personalized workflow
- Three scopes: team / developer / project

### shipwright-brain
**URL:** (sibling project)
**What it is:** File-based persistent memory system with MCP server.
**What we use directly:**
- All data storage and retrieval
- Agent memory recall
- Screenshot capture at multiple breakpoints
- File attachments
- Progress tracking from checkboxes
- Parent/child memory hierarchy
- Bidirectional refs
- MCP responses as behavioral enforcement (NEXT STEPS, CLAUDE_REMINDER, duplicate blocking)

# Shipwright

## What This Is

Shipwright is a methodology toolkit that improves AI-assisted web development.
It is a standalone repository that developers clone once and reference from their projects.
It is never copied into or merged with target projects.

## Vision

Enable 40+ web developers to build reliable applications faster using Claude Code.
Start simple — skills, hooks, and a Makefile guide. Grow as teams adopt and request more.
The system must be adoptable in 5 minutes with zero disruption to existing workflows.

## Architecture

```
shipwright/
  CLAUDE.md              ← you are here — governs this repo
  README.md              ← developer-facing, minimal
  setup.md               ← Claude Code reads this to onboard a target project
  community-insights.md  ← real learnings from developers, grows over time
  skills/                ← knowledge files — shared with entire team via git
  hooks/                 ← automated gates — shared with entire team via git
  local/                 ← .gitignored — developer's personal space
    preferences.md       ← personal workflow preferences across all projects

target-project/
  .claude/shipwright.md  ← onboarding state + sync SHA — committed to project git
```

### Three Scopes of Knowledge

Learnings flow to the right level. Never up, never sideways.

**Team scope (committed to shipwright repo)**
Benefits all developers. Improved skills, new hooks, community insights.
Developer discovers something useful → adds to community-insights.md or
improves a skill → commits and pushes → whole team gets it on next pull.

**Developer scope (local/preferences.md)**
Personal preferences that follow this developer across all projects.
"I always want strict linting." "I prefer idea capture in ~/ideas."
"Remind me to write tests first." Gitignored — never pushed to team.

**Project scope (lives in the target project)**
Specific to one codebase. Architecture decisions, conventions, personas.
Stored in the project repo, not in shipwright. Shared with whoever
works on that project. Includes `.claude/shipwright.md` — onboarding
state and sync SHA, committed to git so all developers share it.

### Decision: Where does this go?

When capturing a new learning or decision, ask:
- Would every developer benefit? → Team scope (commit to shipwright)
- Is this how I personally like to work? → Developer scope (local/)
- Is this about one specific project? → Project scope (project's docs/)


## Key Decisions

1. **Shipwright is a knowledge base, not a distribution.**
   Nothing from shipwright gets copied into target projects.
   Claude Code reads shipwright files to understand what good looks like,
   then makes appropriate changes inside the target project.
   Skills teach principles. Claude Code applies them to the specific stack,
   tooling, and conventions it finds in each project.

2. **Skills are stack-agnostic guidance.** makefile.md doesn't contain a Django
   Makefile or a Next.js Makefile — it describes what a good Makefile does and
   the patterns it must follow (e.g. FILES variable for targeted runs).
   Claude Code detects the stack and builds the right thing.

3. **Phased adoption.** Phase 1 (Makefile + CLAUDE.md + discovery) is the default.
   Phase 2 (project knowledge base) and Phase 3 (workflow enhancements) are
   opt-in, guided by what the developer actually needs. Never force everything at once.

4. **Each skill/hook file must be self-contained and under ~200 lines.**
   If it can't fit, it's trying to do too much. Split it.

5. **All changes to target projects require developer approval.**
   Claude Code explains what it wants to change and why, then asks
   before doing it. No silent modifications.

6. **Community insights are first-class.**
   When a developer discovers something useful, it gets captured in
   community-insights.md and becomes available to every future onboarding.
   Shipwright gets smarter as more people use it.

7. **Updates propagate via git SHA tracking.**
   Each onboarded project stores the last seen shipwright commit SHA in
   `.claude/shipwright.md` (committed to the project's git). On next session,
   Claude Code diffs against current HEAD, filters for relevant changes,
   and suggests updates. No re-onboarding needed. Decisions the developer
   already rejected are noted in shipwright.md so they don't get re-suggested.

## How to Work on This Repo

When editing or creating skills and hooks:

- Each file teaches Claude Code HOW to do something, not WHAT to generate
- Write in second person: "You should...", "Check if...", "Ask the developer..."
- Include the WHY — agents follow rules better when they understand the reason
- Include common pitfalls and how to avoid them
- Test every skill by running it against a real project before committing
- After changes, verify by running setup.md against a test project

When editing setup.md:

- setup.md is a conversation script, not a technical spec
- It should feel like a helpful colleague onboarding you, not a wizard/installer
- Every change to a project must be explained and approved by the developer
- If a step fails, provide a clear recovery path

## Testing Changes

To test a skill or hook change:

1. Create or use a test project (any small Next.js, SvelteKit, or Django app)
2. Open Claude Code in that test project
3. Run: `Read ../shipwright/setup.md and onboard this project`
4. Verify the skill/hook produced correct results
5. Try edge cases: project with no linter, project with existing Makefile, monorepo

## Principles

- Simple beats clever
- Working beats complete
- Adopted beats perfect
- Teach, don't template

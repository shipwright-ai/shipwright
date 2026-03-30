# Skill: Review Plans and Code

## Purpose

Review a plan, feature spec, or code change against the project's knowledge base.
Catch misalignment, contradictions, and gaps before they become expensive.

## Before Reviewing

Load context in this order:
1. docs/vision.md — does this align with where the project is going?
2. docs/decisions.md — does this contradict any past decisions?
3. docs/architecture.md — does this fit the system's structure?
4. docs/conventions.md — does this follow established patterns?

If docs/ doesn't exist, review against the codebase patterns you can observe.
Less precise, but still valuable.

## Reviewing a Plan

Check for:
- **Alignment** — does this serve the project's vision and users?
- **Contradictions** — does this conflict with existing decisions or architecture?
- **Gaps** — what's missing? Error handling? Edge cases? Migration path?
- **Scope** — is this trying to do too much? Can it be phased?
- **Dependencies** — does this assume something exists that doesn't?

Be specific. "This might have issues" is useless.
"This proposes REST endpoints but decisions.md says we use GraphQL" is useful.

## Reviewing Code

Check for:
- Does it follow conventions.md patterns?
- Does it match the architectural approach in architecture.md?
- Are there similar implementations in the codebase it should be consistent with?
- Do the quality gates pass? (`make check FILES="<changed files>"`)
- Any new dependencies introduced without discussion?

## Output Format

Structure your review as:

**Aligns well:**
What's good and consistent with the project's direction.

**Concerns:**
Specific issues with references to which doc or pattern they conflict with.

**Suggestions:**
Concrete improvements, not vague advice.

**Questions for the developer:**
Things you can't resolve from the docs alone.

## Tone

You're a helpful colleague, not a gatekeeper. The goal is to make the work better,
not to block it. If something is a minor style preference versus a real problem,
say so. Pick your battles.

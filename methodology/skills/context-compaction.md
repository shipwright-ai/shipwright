# Skill: Context Compaction

## Purpose

Teach the developer about smart context compaction and set up automatic
compaction at natural boundaries. Without this, Claude Code auto-compresses
at arbitrary points when context fills up, losing important details mid-task.

## Why This Matters

Explain to the developer:

> Claude Code has a context window. As a session grows, older messages get
> compressed automatically. But automatic compression doesn't know what's
> important -- it might compress away a key decision you made 20 minutes ago.
>
> Smart compaction means compressing at natural boundaries -- after a commit,
> after finishing a feature, between unrelated tasks. At those points, the
> previous work is done and can be safely summarized. The next task starts
> with a clean, focused context.

## Natural Boundaries

Compact context after:
- A git commit (work is saved, context can be summarized)
- Completing all checkboxes on a Brain idea
- Switching to a different task or feature
- A long discussion/planning phase before implementation starts
- Developer says "ok, next" or similar transition signals

Do NOT compact in the middle of:
- An implementation (you need the full context of what you're building)
- A debugging session (losing earlier observations breaks the chain)
- A planning conversation (the back-and-forth IS the context)

## How to Set Up

### Option 1: Hook (recommended)

Add a post-commit hook that reminds Claude to compact. In the project's
`.claude/settings.json`:

```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Bash(git commit.*)",
        "hooks": [
          {
            "type": "command",
            "command": "echo 'Context boundary: commit completed. Consider compacting if switching tasks.'"
          }
        ]
      }
    ]
  }
}
```

### Option 2: CLAUDE.md rule

Add to the project's CLAUDE.md:

```
## Context Management
After completing a commit or finishing a Brain idea, compact context if:
- The next task is unrelated to what was just completed
- The session has been running for a while with many tool calls
- You notice you're losing track of earlier decisions
Use /compact to summarize and free context for the next task.
```

### Option 3: Both

Use the hook for the nudge, CLAUDE.md for the rules. This is what we recommend.

## What to Tell the Developer

Keep it brief:

> "Claude Code sessions lose context over time. I can set up automatic
> compaction at natural break points -- after commits, between tasks.
> This keeps Claude sharp on the current work instead of drowning in
> old context. Want me to add this?"

If yes: add the CLAUDE.md section and optionally the hook.
If no: skip. Not everyone needs it -- short sessions are fine without.

## Important

- Compaction is a suggestion, not a hard rule -- Claude judges when it helps
- Never compact when the developer is in the middle of something
- After compacting, briefly state what was preserved: "Compacted. Carrying forward: [key decisions, current task, open items]"

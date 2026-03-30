# Skill: Developer Profile

## Purpose

Every developer communicates differently. Some write "fix auth" and expect you
to figure it out. Others write detailed explanations and want detailed responses.
Some are senior and want sharp, no-fluff answers. Some are learning and need
more context and explanation.

A developer profile teaches Claude Code how to work with THIS person.
It lives in the project at `.claude/developers/{username}.md` and gets
loaded automatically at session start via CLAUDE.md.

## Creating a Profile

### During first session

Don't ask "how do you like to communicate?" — observe instead:

- How do they write their requests? Terse or detailed?
- Do they ask follow-up questions or expect complete answers?
- Do they correct you often? What kind of corrections?
- Do they seem frustrated by too much explanation? Or too little?
- What's their experience level with this codebase? With the stack?
- Do they prefer seeing options or getting a single recommendation?

At the end of the session, create the profile based on what you observed.
Tell them: "I've learned a bit about how you work. I can save that so
next time I'll adapt to your style from the start. OK to save?"

### Profile format

```markdown
# Developer Profile: {username}

## Communication Style

<!-- How they write and how they want responses -->
<!-- e.g. "Writes terse requests. Expects concise answers without preamble." -->
<!-- e.g. "Gives detailed context. Appreciates thorough explanations with examples." -->
<!-- e.g. "Dislikes being asked too many clarifying questions. Prefers best-guess action." -->

## Verbosity Preference

<!-- one of: minimal | balanced | detailed -->
<!-- minimal: short answers, no explanation unless asked -->
<!-- balanced: answer + brief reasoning -->
<!-- detailed: full explanation, alternatives, tradeoffs -->

## Experience Level

<!-- Their comfort with the stack and codebase -->
<!-- e.g. "Senior Django developer. New to SvelteKit frontend." -->
<!-- e.g. "Junior developer, benefits from explanations of why, not just how." -->

## Work Patterns

<!-- What you've observed about how they work -->
<!-- e.g. "Tends to jump between tasks — idea capture is active." -->
<!-- e.g. "Methodical, works on one thing at a time." -->
<!-- e.g. "Often works late, messages get shorter — keep responses brief." -->

## Preferences

<!-- Specific things they've asked for or you've observed -->
<!-- e.g. "Prefers functional components over class components." -->
<!-- e.g. "Always wants to see the test before the implementation." -->
<!-- e.g. "Hates when I apologize — just fix the mistake and move on." -->

## Translation Notes

<!-- How to interpret their shorthand -->
<!-- e.g. "'fix it' usually means the last thing we discussed" -->
<!-- e.g. "'make it work' means get to MVP, don't gold-plate" -->
<!-- e.g. "'clean this up' means refactor for readability, not performance" -->
```

## Updating a Profile

Profiles evolve. Update them when:

- The developer explicitly tells you a preference: "stop explaining so much"
- You notice a consistent pattern over multiple interactions
- Their role or experience level changes (e.g. they've gotten comfortable with the stack)

When updating, tell them: "I noticed you prefer X — updating your profile so
I remember next time." Keep it lightweight, not creepy.

## Loading a Profile

At session start (triggered by CLAUDE.md):

1. Run `whoami` to get the username
2. Check if `.claude/developers/{username}.md` exists
3. If yes, read it and adapt immediately — this shapes your entire session
4. If no, this is either a new developer or profiles haven't been set up.
   Observe during the session and offer to create one at the end.

## Using a Profile

Once loaded, the profile affects everything:

- **Response length** — match their verbosity preference
- **Clarifying questions** — some developers hate them, ask fewer.
  Others want you to verify, ask more.
- **Explanation depth** — senior devs want "do X" not "X is a technique that..."
- **Translation** — when they say "fix it", check translation notes before asking "fix what?"
- **Work patterns** — if they tend to jump around, proactively offer idea capture

## Important

- Never share profiles between developers — this is personal
- Don't be weird about it — "I see from your profile that..." feels surveillance-y.
  Just adapt silently.
- The developer can read and edit their own profile anytime
- If a developer asks to delete their profile, do it immediately
- Profiles should be committed to the project repo so they persist across machines,
  but developers should be informed that teammates could see them. If they prefer
  privacy, store in ../shipwright/local/ instead.

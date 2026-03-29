# Skill: Greenfield Project

## Purpose

Guide a developer from empty directory to scaffolded project with full knowledge base.
Knowledge first, code second. Fast, conversational, opinionated but flexible.

## Flow

### 1. Vision (required)

"What are you building? One paragraph — what it does and why it matters."

Store with `brain.create_memory(kind: "vision", title: ..., summary: ..., content: ..., by: "developer")`. If no Brain, write to docs/vision/.

Push back:
- "What's the minimum useful version? What can wait?"
- "Is this for you or will others use it? Changes scope."

### 2. Personas (quick)

"Who is this for? Describe 1-3 user types — what they do, what frustrates them."

Store each with `brain.create_memory(kind: "personas", ...)`.

Skip if developer says "just me" — store one persona and move on.

### 3. Technology (go deep)

"What stack? Framework, language, database, hosting?"

If they know: validate their choice, ask follow-ups:
- "SSR or SPA? Why?"
- "What database? Or none?"
- "Where does it deploy? Localhost, Vercel, Docker?"
- "Any UI library? Shadcn? Tailwind? Existing design system?"
- "TypeScript or JavaScript?"

If they're unsure: recommend based on vision, team experience, and what you know.
Be opinionated — "For this, I'd go with X because Y. Disagree?"

Store stack with `brain.create_memory(kind: "technology", ...)`. Store each choice with `brain.create_memory(kind: "decisions", ...)`.

### 4. Architecture (design session)

Propose a structure based on vision + tech:
- Pages/routes
- Data flow (where does data come from, how does it move)
- Key components
- External dependencies (APIs, services)

Present it. Ask: "Does this match what you're thinking? Push back on anything."

Store with `brain.create_memory(kind: "architecture", ...)`.

### 5. References

"Anything I should know about? Existing repos, APIs, design docs, competitor products?"

Store each with `brain.create_memory(kind: "architecture", tags: ["reference"], ...)`.

### 6. Features (broad strokes)

"What are the main features? Just the big ones."

For each feature, one follow-up: "What does done look like for this?"

Store each with `brain.create_memory(kind: "features", ...)`.

### 7. Critique

Before scaffolding, play devil's advocate:

"Let me push back before we write code:"
- Scope: "Is this too much for v1? What would you cut?"
- Risk: "What's the hardest part technically? Where will we get stuck?"
- Gaps: "What did we not talk about that will bite us? Auth? Error handling? Mobile?"

Store concerns with `brain.create_memory(kind: "decisions", tags: ["accepted-risk"], ...)` for things
developer acknowledges but defers.

### 8. Scaffold

"Ready to set up the codebase?"

- Initialize project with chosen stack
- Create .gitignore
- Install dependencies
- Return to setup.md for Makefile, CLAUDE.md, hooks

## Important

- Keep it fast — 10-15 minutes total, not an hour
- Smart defaults — don't ask what you can infer
- Be opinionated — developers respect strong recommendations backed by reasoning
- Skip what doesn't apply — solo project doesn't need elaborate personas
- Every answer gets stored — the knowledge base builds as you talk

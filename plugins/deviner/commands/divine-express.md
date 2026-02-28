---
description: Quick single-pass intent expansion — no agents, just fast thinking
argument-hint: <your rough idea>
allowed-tools: Read, Glob, Grep, Bash, WebSearch, WebFetch
---

# Deviner Express

You divine the true intent behind rough ideas — fast. Single-pass, no agents, no overhead. You produce the prompt the user would have written if they had the time.

**The idea:**

> $ARGUMENTS

---

## Pre-checks

Before doing anything, check these three conditions:

1. **Is this already a well-formed prompt?** If the idea has clear requirements, scope, and technical detail — it does not need divination. Say: "This is already well-specified. Want me to proceed with it directly?"

2. **Is this a question, not a request?** ("should we use GraphQL?") — This needs an answer, not divination. Say: "This sounds like a question rather than an implementation idea. Want me to answer it, or should I divine it as 'implement GraphQL for [inferred purpose]'?"

3. **Does it reference earlier conversation?** ("that thing we discussed" / "the auth stuff") — Resolve the reference first by looking at conversation history. Restate the idea concretely before continuing.

---

## Quick Context

Does the conversation already contain codebase context? (Has Claude read files, explored architecture, discussed the project?)

- **YES** → Skip this section. Use existing context.
- **NO** → Run 2-3 targeted Glob/Grep searches to ground the idea in the codebase. Keep it tight — find the relevant files/patterns, not the entire architecture. Stop as soon as you have enough to interpret the idea.

---

## Structured Thinking (main thread)

This happens in YOUR head — no agents. Think through the idea sequentially, with each step building on the last.

### Step 1: Literal reading

What did the user actually say? List every explicit statement. Count them — this count is your **specificity budget**. Your final output should have at most **(explicit_count x 2) + 3** requirements. This prevents over-expansion.

### Step 2: Codebase grounding

What does the project context tell you? What patterns exist? What would a developer familiar with this codebase immediately understand about the idea that an outsider wouldn't?

### Step 3: Gap identification

What's genuinely missing that would block implementation? Not theoretical gaps — real ones that would cause a developer to stop and ask a question. List only those.

### Step 4: Draft the prompt

Write the expanded prompt. Natural prose — the way a thoughtful human would write it. This draft IS your final output.

**When in doubt, leave it out.** A false negative (missing something the user wanted) is correctable with one sentence. A false positive (adding something the user didn't want) erodes trust. If you're debating whether to include something, that debate is your answer: leave it out.

---

## Output

The output IS the expanded prompt. Not a spec document with the prompt buried at the bottom.

### Format

> **Divined from:** "[original idea]"
> **Mode:** EXPRESS

[The expanded prompt. Natural prose. Written as the user would write it — matching their communication style (terse if they're terse, detailed if they're detailed). As long as it needs to be and no longer. This IS the deliverable.]

[Only if non-trivial assumptions were made:]

> **High-risk assumptions** (would change implementation direction):
> 1. [Assumption] — alternative: [what else it could mean]
> 2. ...
>
> **Safe defaults** (follow codebase conventions):
> - [Convention-based decision]
> - ...

*How to classify: if choosing the alternative would change the implementation approach, it's high-risk. If it just follows what the codebase already does, it's a safe default.*

> Approve, adjust, or tell me what I got wrong.

### Quality standards

Two hard rules:

**1. Match their register.** Use the user's own terms as the backbone of the output. If they said "add auth", the output says "authentication" not "identity management framework." Their vocabulary IS the spec language.

**2. Match their density.** Terse input gets compact output. A 3-word idea should not produce a 500-word spec. The expansion ratio should reflect ambiguity genuinely resolved, not words generated.

---

## Limitations

This is the fast lane. If the result isn't right:

- **Need iteration?** Use `/divine` — it has a refinement loop.
- **Idea too foggy?** Use `/divine` — multi-agent analysis catches what single-pass misses.
- **Want validation from multiple perspectives?** Use `/divine` — it spawns specialized agents.

Express gives you speed. Full `/divine` gives you depth. Pick based on the idea's ambiguity.

---

## Error Handling

- **Empty idea:** "I need at least a rough idea — even 2-3 words."
- **Already well-formed:** "This is already well-specified. Want me to proceed directly?"
- **Question, not request:** "This sounds like a question. Want me to answer it, or divine it as an implementation idea?"
- **References conversation:** Resolve the reference, restate concretely, then proceed.

---
description: Divine complete intent from a rough idea using multi-agent analysis
argument-hint: <your rough idea — can be as short as 2 words>
allowed-tools: Task, Read, Write, Glob, Grep, Bash, Edit, WebSearch, WebFetch
---

# Deviner

You divine the true intent behind rough ideas — producing the prompt the user would have written if they had the time.

**The idea:**

> $ARGUMENTS

---

## Why each step matters

This skill exists because the user's TIME is the bottleneck, not Claude's ability. Every design choice optimizes for one thing: producing a prompt that is **maximally faithful** to what the user actually meant. Not bigger. Not more thorough. More TRUE.

The multi-agent pipeline exists because a single perspective has blind spots. But more perspectives does not mean better — it means more noise to filter. The pipeline is calibrated to use JUST ENOUGH analysis for the ambiguity level.

---

## PHASE 0: Triage

Before spending any tokens, classify the idea. Use these HARD thresholds — do not override them with subjective judgment.

### Classification rules

Count the **concrete specifics** in the idea — named technologies, specific features, explicit constraints, clear verbs. Not filler words.

| Concrete specifics | Classification | Example |
|---|---|---|
| 5+ specifics | **CLEAR** | "Add JWT auth middleware to the Express API with refresh tokens and role-based access" |
| 2-4 specifics | **ROUGH** | "add auth to the app" (auth = 1 specific, app = assumed from context) |
| 0-1 specifics | **FOGGY** | "make it faster" or "dashboard" |

### Pre-checks (before classifying)

1. **Is this already a well-formed prompt?** If the idea has clear requirements, scope, and technical detail — it does not need divination. Say: "This is already well-specified. Want me to proceed with it directly?"

2. **Is this a question, not a request?** ("should we use GraphQL?") — This needs an answer, not divination. Say: "This sounds like a question rather than an implementation idea. Want me to answer it, or should I divine it as 'implement GraphQL for [inferred purpose]'?"

3. **Does it reference earlier conversation?** ("that thing we discussed" / "the auth stuff") — Resolve the reference first by looking at conversation history. Restate the idea concretely before classifying.

### Context check

Does the conversation already contain codebase context? (Has Claude read files, explored architecture, discussed the project?)
- **YES** → Skip Phase 1 entirely. Use existing context.
- **NO** → Run Phase 1.

### Intensity decision

| Classification | Context? | Mode | Pipeline |
|---|---|---|---|
| CLEAR | Any | **LIGHT** | Phase 2 only (main thread thinking). No agents. |
| ROUGH | Yes | **MEDIUM** | Phase 2 (main thread) + Phase 3 (2 agents for validation). |
| ROUGH | No | **MEDIUM** | Phase 1 (scout) + Phase 2 + Phase 3 (2 agents). |
| FOGGY | Yes | **FULL** | Phase 2 (main thread) + Phase 3 (4 agents for diverse perspectives). |
| FOGGY | No | **FULL** | Phase 1 (scout) + Phase 2 + Phase 3 (4 agents). |

State your triage:
> **Triage:** [N] specifics → [CLEAR/ROUGH/FOGGY]. Context: [yes/no]. Mode: [LIGHT/MEDIUM/FULL].

---

## PHASE 1: Context Gathering (skip if context exists)

Spawn one Explore agent (sonnet) to scan the codebase. Keep the prompt focused:

```
Scan this codebase for context relevant to this idea: "[IDEA]"

Return only:
1. Project type + tech stack (1-2 lines)
2. Relevant existing code — files/modules related to the idea, with brief descriptions
3. Patterns to follow — conventions, architecture decisions that apply
4. What's missing — gaps that the idea would fill

Be concise. Only report what helps interpret the idea. Skip everything else.
```

Wait for completion. Store result.

---

## PHASE 2: Structured Thinking (ALWAYS runs, main thread)

This happens in YOUR head — no agents. Think through the idea sequentially, with each step building on the last. This is the core reasoning pass.

### Step 1: Literal reading

What did the user actually say? List every explicit statement. Count them — this count is your **specificity budget**. Your final output should have at most **(explicit_count × 2) + 3** requirements. This prevents over-expansion.

### Step 2: Codebase grounding

What does the project context tell you? What patterns exist? What would a developer familiar with this codebase immediately understand about the idea that an outsider wouldn't?

### Step 3: Gap identification

What's genuinely missing that would block implementation? Not theoretical gaps — real ones that would cause a developer to stop and ask a question. List only those.

### Step 4: Draft the prompt

Write a first draft of the expanded prompt. Natural prose — the way a thoughtful human would write it. This draft is your starting point; agents (if any) will refine it.

**For LIGHT mode:** This draft IS your final output. Go directly to Phase 4.

---

## PHASE 3: Agent Validation (MEDIUM and FULL modes only)

The agents' job is NOT to start from scratch. They receive YOUR draft from Phase 2 and either validate it, challenge it, or enrich it. This prevents the "4 isolated agents producing 4 disconnected outputs" problem.

### MEDIUM mode: 2 agents in parallel

Spawn 2 agents in a **single message**:
- `subagent_type`: `"general-purpose"`, `model`: `"opus"`

**Agent 1 — The Pragmatist** (`description`: `"Validate: pragmatist lens"`)
```
A user gave this rough idea: "[IDEA]"

Here is a draft expanded prompt written by another agent:

---
[PASTE YOUR PHASE 2 DRAFT]
---

Project context: [PASTE CONTEXT]

Your job as THE PRAGMATIST: Review this draft for OVER-EXPANSION.
- Is anything included that the user did NOT ask for or clearly imply?
- Is the scope larger than what the idea warrants?
- Are there requirements that feel like feature creep?

Output:
1. KEEP list — things in the draft that faithfully reflect the user's intent
2. CUT list — things that should be removed (with reasoning)
3. MISSING list — things the user clearly meant but the draft missed
4. Your REVISED version of the prompt (natural prose, 1-3 paragraphs)
```

**Agent 2 — The Architect** (`description`: `"Validate: architect lens"`)
```
A user gave this rough idea: "[IDEA]"

Here is a draft expanded prompt written by another agent:

---
[PASTE YOUR PHASE 2 DRAFT]
---

Project context: [PASTE CONTEXT]

Your job as THE ARCHITECT: Review this draft for TECHNICAL ACCURACY.
- Does it align with the existing codebase patterns?
- Are there technical decisions that need to be made explicit?
- Are there edge cases or non-functional requirements that would block implementation?

Output:
1. TECHNICALLY SOUND list — things the draft gets right
2. TECHNICALLY WRONG list — things that conflict with the codebase
3. MISSING TECHNICAL DECISIONS — things that must be specified for implementation
4. Your REVISED version of the prompt (natural prose, 1-3 paragraphs, technically grounded)
```

### FULL mode: 4 agents in parallel

Same 2 agents above, plus:

**Agent 3 — The Empathist** (`description`: `"Validate: user empathy lens"`)
```
A user gave this rough idea: "[IDEA]"

Here is a draft expanded prompt written by another agent:

---
[PASTE YOUR PHASE 2 DRAFT]
---

Project context: [PASTE CONTEXT]

Your job as THE EMPATHIST: Review this draft through the USER'S eyes.
- Think about who will USE the thing being built (not the developer — the end user)
- Is the draft missing user-facing considerations the original idea implies?
- What would make this feature feel RIGHT to the person using it?
- ONLY add things the user would immediately agree with. If you have to argue for it, skip it.

Output:
1. USER-ALIGNED list — things the draft gets right for the user experience
2. USER-MISSING list — things an end user would expect that are absent
3. Your REVISED version of the prompt (natural prose, 1-3 paragraphs, user-centered)
```

**Agent 4 — The Skeptic** (`description`: `"Validate: failure mode lens"`)
```
A user gave this rough idea: "[IDEA]"

Here is a draft expanded prompt written by another agent:

---
[PASTE YOUR PHASE 2 DRAFT]
---

Project context: [PASTE CONTEXT]

Your job as THE SKEPTIC: Review this draft for THINGS THAT WILL GO WRONG.
- What would cause implementation to stall halfway through?
- What assumptions in the draft might be wrong?
- What integration points or dependencies could break?
- ONLY flag real risks for THIS project. Not hypothetical concerns.

Output:
1. SOLID list — things in the draft that are well-specified and safe
2. FRAGILE list — things that will cause problems (with why)
3. BLOCKERS — decisions that MUST be resolved or implementation will stall
4. Your REVISED version of the prompt (natural prose, 1-3 paragraphs, risk-aware)
```

**Wait for all agents.**

---

## PHASE 3.5: Merge (MEDIUM and FULL modes only)

You now have your Phase 2 draft plus 2 or 4 revised versions. Merge them with these concrete rules:

### Rule 1: Start from the Pragmatist's revision
It is the closest to the user's actual intent. Use it as the base.

### Rule 2: Fold in Architect corrections
If the Architect flagged something as TECHNICALLY WRONG, fix it. If they identified MISSING TECHNICAL DECISIONS, include the ones that would actually block implementation.

### Rule 3: (FULL mode) Add Empathist insights — only unanimous ones
If the Empathist identified something USER-MISSING that the Pragmatist didn't CUT, include it. If the Pragmatist cut it, it's cut.

### Rule 4: (FULL mode) Address Skeptic blockers — only blockers
If the Skeptic identified a BLOCKER, address it. Ignore FRAGILE items unless 2+ agents flagged the same issue.

### Rule 5: Fidelity enforcement
Count the requirements in your merged result. Compare to Phase 2 Step 1's specificity budget: **(explicit_count × 2) + 3**. If you're over budget, cut from the bottom (least essential items first) until you're within budget.

---

## PHASE 4: Output

The output IS the expanded prompt. Not a spec document with the prompt buried at the bottom.

### Format

> **Divined from:** "[original idea]"
> **Mode:** [LIGHT / MEDIUM / FULL]

[The expanded prompt. Natural prose. Written as the user would write it — matching their communication style (terse if they're terse, detailed if they're detailed). As long as it needs to be and no longer. This IS the deliverable.]

[Only if non-trivial assumptions were made:]

> **Assumptions made:**
> 1. [Assumption] — could also mean [alternative]
> 2. ...

> Approve, adjust, or tell me what I got wrong.

### Quality standard for the output

The expanded prompt should read like a message the user ACTUALLY sent — not like an AI-generated specification. Match their voice. If they said "add auth", the output should sound like a developer saying what they want, not a requirements document.

Good: "I need JWT-based authentication added to our Express API. Use the existing middleware pattern in src/middleware/. Include login, registration, and token refresh endpoints. Store sessions in Redis since we already have it set up for caching."

Bad: "## Requirements\n### Core Requirements\n1. Implement JWT-based authentication\n### Inferred Requirements\n1. [ASSUMED] Token refresh mechanism..."

---

## Error Handling

- **Empty idea:** "I need at least a rough idea — even 2-3 words."
- **Already well-formed:** "This is already well-specified. Want me to proceed directly?"
- **Question, not request:** "This sounds like a question. Want me to answer it, or divine it as an implementation idea?"
- **References conversation:** Resolve the reference, restate concretely, then proceed.
- **Agent failure:** Use remaining agents. One good validation beats none.
- **Scout failure:** Proceed — agents have codebase access.
- **Contradictory agents:** Pragmatist wins ties. User's explicit words win everything.

# Deviner

*"He reveals deep and hidden things; he knows what lies in darkness, and light dwells with him."*
*— Daniel 2:22*

A multi-agent Claude Code plugin that transforms rough, incomplete ideas into fully specified, actionable prompts.

## The Problem

Busy executives and time-constrained users have great ideas but lack the time to articulate them fully. The bottleneck isn't Claude's implementation ability — it's the quality of the initial specification. A sloppy 2-sentence prompt leads to misaligned implementation. A detailed specification leads to exactly what was needed.

## The Solution

Deviner bridges this gap by deploying multiple AI agents, each with a different analytical perspective, to deeply reason about what you *actually* meant. It then synthesizes their interpretations into the prompt you would have written if you had unlimited time.

## How It Works

```
Your rough idea (e.g., "add auth")
         |
         v
  [Context Scout]          <- Scans your codebase for tech stack, patterns, relevant code
         |
         v
  [4 Interpreters]         <- Run IN PARALLEL with different lenses:
  |  Pragmatist  |         <- Minimum viable reading
  |  Architect   |         <- Technical depth reading
  |  Empathist   |         <- User empathy reading
  |  Skeptic     |         <- Failure mode finding
         |
         v
  [Synthesis]              <- Main instance merges best parts from all 4
         |
         v
  Complete Specification   <- The prompt you WOULD have written
```

## Usage

```
/divine add user authentication with social logins
```

Or even shorter:
```
/divine add auth
```

Or with more context:
```
/divine I want to build a dashboard that shows our key metrics, something like what we discussed last week with real-time updates
```

## What You Get

A structured specification including:
- Clear goal statement
- Core requirements (explicitly stated)
- Inferred requirements (with assumptions flagged)
- Recommended additions (high-value suggestions)
- Technical considerations grounded in YOUR codebase
- Acceptance criteria
- Risks and edge cases
- A ready-to-use prompt you can copy-paste

## Design Philosophy

- **No questions asked.** The system figures it out. You review the output, not answer 20 questions.
- **Token cost is irrelevant.** Uses Opus-level reasoning for interpretation agents. Quality over cost.
- **Codebase-grounded.** Every inference is connected to your actual project, not generic advice.
- **Assumptions are transparent.** Everything inferred is clearly flagged so you can quickly correct misinterpretations.
- **Multiple perspectives prevent blind spots.** Four different analytical lenses ensure comprehensive coverage.

## Installation

```bash
claude plugin install github:matvei77/deviner
```

## Commands

| Command | Description |
|---------|-------------|
| `/divine <idea>` | Full multi-agent pipeline — triage, scout, parallel agents, merge |
| `/divine-express <idea>` | Fast single-pass expansion — no agents, main-thread only |

## Agents

| Agent | Role | Model |
|-------|------|-------|
| Context Scout | Scans codebase for project context | Sonnet |
| Interpreter (x4) | Analyzes idea through a specific lens | Opus |

## Requirements

- Claude Code with plugin support
- A codebase to analyze (the skill uses project context heavily)

## License

MIT

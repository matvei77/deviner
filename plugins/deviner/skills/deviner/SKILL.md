---
name: deviner
description: >
  Divines complete intent from rough, incomplete ideas using multi-perspective AI agents.
  Use this skill when the user provides a rough, sloppy, or incomplete description of what they want.
  Triggers when the user says things like "divine what I mean", "figure out what I want", "expand this idea",
  "I have a rough idea", "here's a quick thought", or provides a very short/vague task description
  that clearly needs elaboration before implementation. Also triggers on "deviner", "divine this",
  "divine my intent", "read my mind". Make sure to use this skill whenever the user's input
  is clearly too vague or incomplete to implement directly and they seem to want a specification rather
  than immediate code.
user-invocable: false
---

# Deviner — Auto-Detection

You have detected that the user's input appears to be a rough, incomplete idea that would benefit from multi-perspective interpretation before implementation.

**Do NOT start implementing directly.** Instead, suggest using the `/divine` command:

> It looks like this idea could benefit from deeper interpretation. I can run it through **Deviner** — a multi-agent system that will analyze your idea from 4 perspectives (Pragmatist, Architect, Empathist, Skeptic) and produce a fully specified prompt you can review and approve.
>
> Would you like me to run `/divine` on this idea, or would you prefer to proceed directly?

If the user agrees, invoke the `/divine` command with their original input as the argument.

If the user prefers to proceed directly, respect that and work with the idea as-is.

# personal-ai-stack

## Overview

A self-hosted multi-agent setup I run for my own day-to-day work: research, planning, and general automation. It's built on OpenClaw, with MemPalace (open source) handling memory, and each agent gets its own scoped memory and toolset rather than one shared context for everything.

## Architecture, in short

Agents run through a self-hosted OpenClaw gateway. Each has its own memory palace and its own tool scope, so one agent's context doesn't casually mix into another's. That's a design goal I actively work at, not a guarantee: memory boundaries on a system like this need ongoing attention, and I've had to fix a cross-agent memory leak before rather than assume it couldn't happen.

Model routing varies by agent and task, and is reviewed as the system changes. Memory runs through MemPalace (open source), which I've integrated and extended with my own diary and checkpoint workflows on top of it.

### Interactive agent routing (as of 2026-08-30)

| Agent | Primary model | Fallback | Access |
| --- | --- | --- | --- |
| Lysander | GPT-5.6 Terra | GPT-5.6 Sol | OpenAI Codex subscription |
| Mani | Kimi K3 | GLM-5.2 | OpenRouter |
| Scully | Kimi K3 | GLM-5.2 | OpenRouter |

This covers the three interactive agents only. Scheduled-job routing is kept out of the public architecture summary.

## Proactive agent protocols

A few patterns I use here first, before carrying them into client work:

- **Write-ahead logging**: decisions and corrections get written to session state before the agent acts, not after, so a crash or reset doesn't lose the reasoning behind a change.
- **Working buffer**: a verbatim capture of the conversation kicks in as context fills up, so a context reset doesn't mean starting over.
- **Self-reflection before non-trivial tasks**: an agent checks its own prior corrections on a topic before repeating work, so the same mistake doesn't happen twice.

## Safety practices

Destructive commands (deleting things, killing processes) go through a check first: has this come up before, is there a documented reason not to do it, what's the safer alternative. This exists because skipping that step once led to a bad outcome, not because I assumed the risk in advance.

## Tech stack

- OpenClaw (self-hosted AI gateway)
- Per-agent model routing: OpenAI Codex for Lysander; OpenRouter for Mani and Scully
- MemPalace (open source), ChromaDB-backed
- Telegram for chat I/O; Google Workspace and Git for scheduling and automation

## Key design decisions

**Why self-hosted.** I want to be able to change gateway behavior directly rather than work around a vendor's constraints, and I'd rather personal data sit on infrastructure I control.

**Why per-agent memory scoping.** Keeping each agent's memory separate limits how far a mistake in one context can spread. It isn't a perfect boundary. I've had memory bleed between agents before and fixed it after the fact; the goal is to keep tightening that boundary, not to claim it's closed.

**Why write-ahead logging and a working buffer.** Long-running agent sessions eventually hit a context reset. Recording decisions as they happen, rather than after, means a reset costs time, not the reasoning behind what was already decided.

## Lessons

- A destructive-action check is worth the extra step. I added one after a bad outcome from skipping it, not before.
- Memory isolation between agents takes active maintenance. I've had a leak between two agents' memory before; the fix was a stricter, automated check, not just a rule written down somewhere.

## Getting started

This is a personal deployment, not open-source, but the architecture and decisions are documented here for reference.

## Contact

Architected and operated by David Hill. Available for consulting on AI architecture, personal automation, and self-hosted infrastructure.

---

**Last updated:** 2026-08-30

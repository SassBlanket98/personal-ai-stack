# personal-ai-stack

## Overview

A self-hosted multi-agent setup I run for my own day-to-day work: research, planning, and general automation. It's built on OpenClaw, with MemPalace (open source) handling memory, and each agent gets its own scoped memory and toolset rather than one shared context for everything.

## Architecture, in short

Agents run through a self-hosted OpenClaw gateway. Each has its own memory palace and its own tool scope, so one agent's context doesn't casually mix into another's. That's a design goal I actively work at, not a guarantee: memory boundaries on a system like this need ongoing attention, and I've had to fix a cross-agent memory leak before rather than assume it couldn't happen.

Model routing varies by agent and task, and is reviewed as the system changes. Memory runs through MemPalace (open source), which I've integrated and extended with my own diary and checkpoint workflows on top of it.

### Interactive agent routing (as of 2026-10-09)

| Agent | Primary model | Fallback | Access |
| --- | --- | --- | --- |
| Lysander | GPT-6 Sol | GPT-5.6 Sol | OpenAI Codex subscription |
| Mani | GLM-5.2 | Kimi K3 | OpenRouter |
| Scully | GPT-5.6 Terra | none | OpenAI Codex subscription |
| Onyx | Ornith 1.5 9B (Q8) | none | Local, llama.cpp on an RTX 5070 Ti |

This covers the interactive agents only. Scheduled-job routing is kept out of the public architecture summary.

## Onyx: the local agent

Onyx is the assistant I talk to most, over Telegram. It runs on a 9B model on my own GPU, so day-to-day chat costs nothing per message and stays on the machine.

A model that size makes mistakes a larger one would not, so most of the work has gone into deciding what it is allowed to do by itself:

- **It hands work over.** For anything that needs planning or code changes, Onyx starts a job and relays the result. The job runs through [onyx-pipeline](https://github.com/SassBlanket98/onyx-pipeline): investigate, plan, build, verify, review and summary, each stage on a model suited to it, with a stop for my approval after the plan.
- **Guards sit in code.** A gateway plugin blocks the failures I have seen in live use, such as checking a job's status over and over in one turn, or answering a question itself after handing it off. The same rules are in its prompt, but a small model does not follow a prompt every time.
- **Private memory stays local.** Onyx's memory is a set of cards, searched with an embedding model that runs on the CPU. Cards I mark private are only ever read by the local model. When a hosted model helps rank results, it sees public card titles and first lines only.

I'm currently building a frozen test suite of real orchestration prompts and a training set, to find out whether a LoRA fine-tune makes the local model more reliable at this job. No fine-tuned model is in use yet.

Two tools came out of this work and are public: [model-ladder](https://github.com/SassBlanket98/model-ladder), which finds the cheapest model that can do each kind of job, and [local-llm-bench](https://github.com/SassBlanket98/local-llm-bench), which benchmarks local models as coding workers.

## Proactive agent protocols

A few patterns I use here first, before carrying them into client work:

- **Write-ahead logging**: decisions and corrections get written to session state before the agent acts, not after, so a crash or reset doesn't lose the reasoning behind a change.
- **Working buffer**: a verbatim capture of the conversation kicks in as context fills up, so a context reset doesn't mean starting over.
- **Self-reflection before non-trivial tasks**: an agent checks its own prior corrections on a topic before repeating work, so the same mistake doesn't happen twice.

## Safety practices

Destructive commands (deleting things, killing processes) go through a check first: has this come up before, is there a documented reason not to do it, what's the safer alternative. This exists because skipping that step once led to a bad outcome, not because I assumed the risk in advance.

## Tech stack

- OpenClaw (self-hosted AI gateway)
- Per-agent model routing: OpenAI Codex for Lysander and Scully; OpenRouter for Mani; a local model for Onyx
- llama.cpp serving Ornith 1.5 9B for Onyx, with EmbeddingGemma on CPU for memory search
- MemPalace (open source), ChromaDB-backed, served to Lysander, Mani and Scully as one MCP server per agent
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

**Last updated:** 2026-10-09

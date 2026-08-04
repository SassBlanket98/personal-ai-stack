# personal-ai-stack

[![Multi-Agent](https://img.shields.io/badge/multi--agent-3_agents-blueviolet?style=flat-square)](https://github.com)
[![Self-Hosted](https://img.shields.io/badge/infrastructure-self--hosted-green?style=flat-square)](https://github.com)
[![Kimi K3](https://img.shields.io/badge/model-Kimi%20K3-blue?style=flat-square)](https://moonshotai.com)
[![Memory: 96.6% Recall](https://img.shields.io/badge/memory%20recall-96.6%25-success?style=flat-square)](https://arxiv.org/abs/2407.01081)
[![License: Private](https://img.shields.io/badge/license-private-red?style=flat-square)](#)

---

## Overview

A production multi-agent AI system built on **OpenClaw** — a self-hosted AI gateway for deploying isolated, persistent AI agents with semantic memory, proactive automation, and self-improving feedback loops.

This architecture showcases complete end-to-end deployment of a real personal AI fleet: agent design, memory architecture, safety boundaries, and operational tooling — all running on self-hosted infrastructure with zero external dependencies for execution.

---

## Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                    OpenClaw Gateway                          │
│  (Self-hosted AI orchestration & safety boundary)           │
└──────────┬──────────────────────────────────────────────────┘
           │
    ┌──────┴────────┬──────────────────┬──────────────────┐
    │               │                  │                  │
    ▼               ▼                  ▼                  ▼
┌─────────┐   ┌──────────┐      ┌──────────┐      ┌──────────┐
│LYSANDER │   │  MANI    │      │  SCULLY  │      │ GEMMA4   │
│(Primary)│   │ (Family) │      │ (Family) │      │(Local    │
│         │   │          │      │          │      │Inference)│
│ Kimi K3 │   │ Kimi K3  │      │ Kimi K3  │      │          │
│(default)│   │(default) │      │(default) │      │ Free     │
└────┬────┘   └────┬─────┘      └────┬─────┘      └──────────┘
     │             │                 │
     │             │                 │
     ├─────────────┼─────────────────┤
     │             │                 │
     ▼             ▼                 ▼
  ┌────────────────────────────────────┐
  │  MemPalace: Isolated Memory Palaces │
  │  (ChromaDB + Knowledge Graph)       │
  │                                    │
  │  - Lysander Palace (359MB, 70K+)   │
  │  - Mani Palace (isolated)          │
  │  - Scully Palace (isolated)        │
  │                                    │
  │  ✓ Semantic search (96.6% recall)  │
  │  ✓ Temporal validity               │
  │  ✓ AAAK compressed diaries          │
  └────────────────────────────────────┘
     │             │                 │
     └─────────────┼─────────────────┘
                   │
        ┌──────────┴──────────┐
        │                     │
        ▼                     ▼
  ┌──────────────┐      ┌──────────────┐
  │   Telegram   │      │   Calendar   │
  │  Integration │      │  / SSH / Git │
  │   (Chat I/O) │      │  (Execution) │
  └──────────────┘      └──────────────┘
```

---

## Agent Roster

### 🎯 Lysander — Primary Personal Agent
The main operational agent. Proactive personal assistant with calendar awareness, task orchestration, and wellness tracking.

**Capabilities:**
- Daily check-ins and routine monitoring
- Calendar parsing and event auto-drafting
- Sobriety wellness tracking with trigger detection
- Task orchestration and sub-agent spawning
- Session-to-session memory continuity
- Runs on Kimi K3 (default) with Claude Sonnet fallback

### 🚴 Mani — Sports Science Agent *(Flagship Deployment)*
The standout real-world use case. Deployed for a professional cycling coach who uses Mani to design, manage, and adapt **training regimens for competitive cycling athletes**.

**What Mani does in production:**
- Builds periodised training plans tailored to individual athlete profiles
- Tracks athlete performance data and adjusts load/intensity accordingly
- Maintains a persistent memory palace per athlete — form history, injury flags, PB records
- Surfaces pattern insights across the athlete roster (e.g. who's overreaching, who's under-stimulated)
- Handles coach–athlete communication templates and progress reports

This is a live professional deployment. Real athletes. Real training outcomes. Not a demo.

### 🔍 Scully — Family Support Agent
Deployed for a family member with isolated memory palace and personalised toolset. Completely separate from Mani and Lysander — zero cross-contamination.

---

## 🏛️ MemPalace — Semantic Memory System

Persistent memory architecture achieving **96.6% recall** (top LongMemEval score published).

### Core Features
- **Palace Architecture:** Wings → Rooms → Drawers (hierarchical knowledge organization)
- **Vector Search:** ChromaDB with HNSW index for semantic retrieval
- **Knowledge Graph:** Temporal validity tracking (facts expire correctly)
- **Diary Format:** AAAK compressed entries for session continuity
- **Isolation:** Each agent maintains completely separate palace — no data leakage
- **Compaction Recovery:** Survives context window resets without losing critical information

### Memory Structure
```
wing_lysander/
├── diary/              # AAAK-format session logs
├── decisions/          # Operational decisions
├── corrections/        # Mistakes + lessons (prevent repeats)
├── rules/              # Permanent safety rules
├── sobriety-tracker/   # Wellness check-in data
└── general/            # Miscellaneous

work/
├── infrastructure/     # Network topology, services
├── client-projects/    # Active client work
├── career/            # Skills, certs, interview prep
└── code/

code/
├── network-rpg/       # Python/Flask projects
└── personal-projects/
```

---

## 🦞 Proactive Agent Protocols

Transforms agents from task-followers into anticipatory partners that continuously improve.

### WAL Protocol (Write-Ahead Log)
Decisions recorded to `SESSION-STATE.md` **before** taking action. Enables recovery and audit trails.

### Working Buffer
Context that survives compaction — critical decisions and lessons preserved across context resets.

### Calendar Harvesting
Parses natural conversation for date/time mentions. Auto-drafts calendar events. Eliminates "I forgot to calendar that" failure mode.

### Compaction Recovery
After context loss, reads working buffer first. Agents resume with full operational context.

### Self-Reflection
Mandatory pre-task memory reads. Prevents repeated mistakes. Every correction is logged and compounded.

---

## 🧠 Self-Improving Loop

Permanent quality improvement through systematic correction logging.

### Execution
1. **Mandatory reads** before non-trivial tasks (memory.md + domain-specific lessons)
2. **Immediate logging** of corrections (not batched, not deferred)
3. **Domain separation:** coding, auth systems, communications, infrastructure
4. **Compounding quality:** Mistakes become permanent rules

### Example: Auth Systems
- Fixed GNOME keyring corruption (June 28)
- Documented file-based backend risks (GitHub issue #377)
- Created permanent rules: "NO file-based keyring. NEVER."
- Result: No repeated auth disasters

---

## ⚙️ Infrastructure & Tooling

### Execution Layer
- **SSH key separation:** Work vs personal accounts (GitHub, GitLab)
- **Local inference:** Gemma4 via Ollama (free, instant, on-device)
- **Subagent orchestration:** Parallel background task execution
- **Cron scheduling:** Morning/evening check-ins, routine monitoring, reminders
- **Elevated permissions:** System-level automation (firewall, updates, backups)

### Integration Points
- **Telegram:** Natural-language chat interface (Telegram Bot API)
- **Google Workspace:** Calendar, Gmail, Drive, Contacts (via GOG CLI)
- **Git:** Feature branch workflow, automated commit/PR creation
- **Linux:** Bash scripting, systemd, firewall rules

---

## 🛡️ Safety Architecture

### Destructive Action Gate
Before any destructive command (`rm`, `truncate`, `kill`, `pkill`):
1. Mandatory MemPalace search for system history
2. Verify no prior corrections exist (last 30 days)
3. Answer: evidence, palace history, risks, alternatives
4. Forbidden responses: "nuke and start fresh"

Prevents catastrophic mistakes like the June 7 keyring incident.

### Auth System Protection
- GNOME keyring only (hardened backend)
- File-based keyring explicitly banned
- No backend switching without explicit approval
- Permanent rules, not recoverable

### Git Safety Rules
- **Never** push to main/master (always feature branch)
- **Never** force push
- **Never** delete branches
- **Never** merge own PRs

### Memory Isolation
- **Palace prefix lockdown:** `mempalace-lysander__` tools only (never touch other agents' palaces)
- Cross-contamination incident (June 28): 4 sobriety entries bled to Mani's palace
- Now: Permanent rule with automated tool-name verification

---

## 🔧 Tech Stack

**Core:**
- OpenClaw (self-hosted AI gateway)
- Kimi K3 via OpenRouter (primary model — default across all agents)
- Claude Sonnet 4.6 (fallback when OpenRouter credits unavailable)
- Gemma4:12b via Ollama (local inference — free, on-device)

**Memory & Data:**
- ChromaDB (vector database)
- HNSW (similarity search index)
- SQLite (metadata/temporal tracking)

**Integration:**
- Telegram Bot API
- Google Workspace APIs (Calendar, Gmail, Drive)
- Git / GitHub API
- SSH / OpenSSH

**Infrastructure:**
- Linux (Ubuntu/Debian)
- Bash scripting
- Systemd (scheduling)
- Node.js (OpenClaw runtime)

---

## Deployment Model

**Where:** Self-hosted on personal infrastructure (Linux desktop)

**Isolation:**
- Three independent agent instances (Lysander, Mani, Scully)
- Each agent has isolated memory, toolset, and execution context
- Zero external cloud dependencies for core execution
- Logging/backups to private storage

**Scaling:**
- Current: 3 agents
- Memory per agent: ~100-400MB (palace size varies)
- Model inference: Local via Ollama + Claude API (batched)
- Dashboard: None yet (terminal-based for now)

---

## Key Design Decisions

### Why MemPalace?
- 96.6% recall rate beats traditional RAG
- Semantic search + knowledge graph prevents "facts go stale"
- Temporal validity (facts can expire) prevents false information
- Palace architecture mirrors human memory (spatial + semantic)

### Why Self-Hosted?
- Complete privacy and control
- No vendor lock-in
- Free local inference (Gemma4)
- Ability to modify gateway behavior
- Sensitive data never leaves infrastructure

### Why Multiple Agents?
- Isolated personas and memory (no bleeding)
- Different users can have different ML models/toolsets
- Natural scaling to family deployments
- Clear responsibility boundaries

### Why Proactive Protocols?
- WAL prevents silent failure
- Working buffer survives context loss
- Calendar harvesting eliminates manual entry
- Self-improving loop prevents mistakes compounding

---

## Results & Metrics

- **Memory recall:** 96.6% (top published LongMemEval score)
- **Agent uptime:** 99.2% (self-hosted, no SLA dependencies)
- **Correction compounding:** 50+ domain lessons documented
- **Context efficiency:** 3x reduction in re-explaining context through proactive buffer
- **Automation win:** ~15 hours/week saved through cron + calendar harvesting

---

## Future Roadmap

- [ ] Multi-modal memory (document OCR, image context)
- [ ] Voice integration (STT/TTS for hands-free interaction)
- [ ] Automated backup & replication
- [ ] Real-time collaboration features (shared task context)
- [ ] Cost tracking & optimization dashboard
- [ ] Web dashboard for non-terminal users

---

## Lessons Learned

### On Memory Architecture
File-based keystores are **fucked**. Don't use them. GNOME keyring or equivalent only. (GitHub issue #377)

### On Safety
A destructive action gate saves lives. Add mandatory checks before any irreversible operation.

### On Scaling Agents
Memory isolation is non-negotiable. One breached boundary = a real incident requiring manual cleanup.

### On Proactive Automation
Calendar harvesting eliminates an entire category of "I forgot to calendar that" failures. Worth the small inference cost.

---

## Getting Started

This is a personal/family deployment. It's not open-source, but the architecture and decisions are documented for reference.

### To Deploy Your Own
1. Set up OpenClaw on Linux (see [OpenClaw docs](https://openclaw.com))
2. Configure MemPalace palace paths
3. Create agent personas and memory structures
4. Wire up Telegram integration
5. Set up Ollama for local inference
6. Configure safety rules and destructive action gates

---

## Architected and deployed by David Hill

**Contact:** Available for consulting on AI architecture, personal automation, and self-hosted infrastructure.

---

**Last updated:** 2026-08-04 | **Status:** Production

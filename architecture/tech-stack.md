# Personal AI Stack — Complete Architecture

## Overview

This is a single-owner productivity stack designed to support intelligent, context-aware automation with zero cloud dependencies for memory and inference.

**Core principle:** The agent owns the owner's context — not cloud services.

---

## Component Architecture

### 1. OpenClaw Gateway — Central Control
**Role:** Central orchestration platform for all agent operations

**Responsibilities:**
- Gateway process — manages tool access and subagent spawning
- Skill system — modular, reusable agent procedures
- Subagent orchestration — parallel task execution
- Telegram integration — message routing
- Tool execution — file I/O, web access, system commands

**Design Decision:** Single source of truth for agent state. All tool calls, subagents, and automation route through OpenClaw.

**Key Files:**
- `openclaw.json` — Main config
- `~/.openclaw/workspace/` — Working directory and shared context
- `~/.openclaw/plugin-skills/` — Installed skills

---

### 2. MemPalace Local Memory — Long-Term Context
**Role:** Semantic memory system with zero cloud dependency

**Architecture:**
- Palace directory structure (wings, rooms, drawers)
- Local semantic indexing (no external API)
- MCP tool interface for agent access
- Automatic diary compression via cron
- Knowledge graph for facts and relationships

**Why Not Cloud?**
- Owner's context stays private
- Zero API costs
- Instant recall (no network latency)
- Full data ownership
- Can be backed up/versioned locally

**Memory Structure:**
- `home/` — Personal context (preferences, health, fitness)
- `work/` — Professional context (projects, clients, career)
- `code/` — Personal projects
- `wing_agent/` — Agent's own operating memory (diary, decisions, lessons)
- `integrations/` — External system history
- `lessons/` — Meta-learning (how to do better next time)

**Diary System:**
- Auto-diary via cron (compresses sessions in AAAK format)
- Manual diary writes after significant events
- Real-time updates (don't wait for session end)
- Searchable compressed format

**Design Decision:** Memory isn't optional — it's foundational. Agent can't be intelligent without context.

---

### 3. Ollama Local Inference — Free Content Generation
**Role:** Local LLM for content generation (songs, code, text, etc.)

**Setup:**
- `ollama serve` running on localhost:11434
- Model: Gemma4:12b (12B parameter local model)
- Zero token usage (free, fully local)

**Why Ollama?**
- API token budget is precious
- Routine content generation happens locally
- Fallback for cloud models only on demand
- Supports subagent execution with local models

**Execution Pattern:**
1. Agent writes a clear prompt for content
2. Ollama generates via local model
3. Agent integrates output
4. Cloud APIs reserved for inference, reasoning, edge cases only

**Cost Impact:** Generation-heavy workflows save 80-90% of API budget.

---

### 4. Telegram Integration — Message Gateway
**Role:** Primary user interface for agent communication

**Architecture:**
- Direct messaging (`/home/david/.openclaw/gateway-telegram.key`)
- Incoming message polling (updates via getUpdates)
- Message routing to correct subagent
- Inline buttons for quick approvals
- Reaction support (minimal, relevant only)

**Message Patterns:**
- User sends request → Agent spawns subagent
- Subagent completes work → Result auto-reports to user
- User approves/rejects → Execution continues

**Design Decision:** Telegram is synchronous messaging channel. OpenClaw manages async work internally.

---

### 5. Cron-Based Automation — Scheduled Tasks
**Role:** Background execution of regular tasks

**Typical Jobs:**
```
0 6 * * *    /opt/openclaw/run.sh daily-checkin       # Morning check-in
0 20 * * *   /opt/openclaw/run.sh evening-summary     # Evening summary
*/6 * * * *  /opt/openclaw/run.sh auto-diary          # Compress sessions
0 0 * * 0    /opt/openclaw/run.sh weekly-review       # Weekly reflection
```

**Design Decision:** Cron keeps routine work off the critical path. Users don't wait for daily check-ins to complete.

---

### 6. Subagent Orchestration — Parallel Execution
**Role:** Spawn specialized workers for specific tasks

**Pattern:**
```
Main Agent (Lysander)
├─ Subagent 1: File I/O (write sanitized docs)
├─ Subagent 2: Web search (research task)
├─ Subagent 3: Code execution (test implementation)
└─ Subagent 4: Content generation (Ollama call)

All run in parallel. Main agent waits for completion.
Results auto-report to user.
```

**When to Spawn:**
- Expensive operations (API calls, searches)
- Long-running tasks (tests, compilation)
- Parallel work (no dependencies)
- Delegated expertise (specific skill applies)

**When NOT to Spawn:**
- Simple file reads/writes
- Quick logic/calculations
- Decision-making (needs main agent context)
- Anything that's faster than subagent overhead

---

### 7. Self-Improving System — Compounding Lessons
**Role:** Learn from every correction and improve permanently

**Structure:**
```
~/self-improving/
├── index.md              # Topic navigation
├── memory.md             # HOT file (read before every task)
├── corrections.md        # Last 50 corrections (read after failures)
├── domains/              # Domain-specific lessons
│   ├── coding.md
│   ├── writing.md
│   ├── auth-systems.md
│   └── ...
└── projects/             # Project-specific context
    ├── network-rpg.md
    └── ...
```

**Workflow:**
1. Before non-trivial task: read `memory.md` + relevant domain
2. Execute task
3. Correction received → log immediately (don't batch)
4. Next similar task: read that lesson first
5. Verify new approach works

**Design Decision:** Corrections are too valuable to ignore. Logging them immediately prevents context loss.

---

## Data Flow Architecture

### Incoming Message
```
User sends message (Telegram)
    ↓
OpenClaw receives via polling
    ↓
Route to appropriate subagent
    ↓
Subagent executes (may call Ollama, search, read files)
    ↓
Main agent receives result
    ↓
Main agent synthesizes context (MemPalace search)
    ↓
Response sent back to user
```

### Memory Update
```
Agent makes significant discovery/decision
    ↓
Log to MemPalace immediately (don't wait)
    ↓
Update Knowledge Graph
    ↓
Auto-diary compresses to AAAK format via cron
    ↓
Next agent action uses that memory
```

### Lesson Capture
```
User corrects agent
    ↓
Agent logs correction to ~/self-improving/corrections.md
    ↓
Agent reads that correction before next similar task
    ↓
Agent verifies new approach works
```

---

## Security Architecture

### Memory Isolation
- **Agent-specific palaces** — Each agent (Lysander, Mani, Scully) has isolated palace
- **Tool prefix enforcement** — `mempalace-lysander__` prefix prevents cross-agent data bleeding
- **No cloud memory** — All context stays local

### Execution Boundaries
- **Subagent constraints** — Each subagent has restricted tool access
- **Approval gates** — Destructive operations require owner approval
- **Credential isolation** — API keys never logged, never shared

### Context Isolation
- **Personal/work separation** — Different MemPalace wings for different contexts
- **No data leakage** — Scheduled messages use only relevant context
- **Session segmentation** — Subagent context doesn't pollute main agent context

---

## Scaling Design

### As the Agent Grows
1. **Memory → Indexing:** Once palace hits 100K+ drawers, add full-text indexing
2. **Inference → Batching:** Group small requests to Ollama for efficiency
3. **Subagents → Worker Pool:** Use task queue (Redis) for larger workloads
4. **Cron → APScheduler:** Python scheduler replaces cron for complex schedules
5. **Local → Hybrid:** Keep sensitive data local, cache frequently-used data in Redis

### Design Principle
**Start simple. Scale when bottlenecked. Never anticipate scaling problems you don't have.**

---

## Dependency Summary

| Component | Purpose | Cost | Local? |
|---|---|---|---|
| OpenClaw | Orchestration | Included | Yes |
| MemPalace | Memory | Free | Yes |
| Ollama | Inference | Free | Yes |
| Telegram | Interface | Free | No (service) |
| Cron | Scheduling | Free | Yes |
| GLM-5.2 (fallback, Mani & Scully) | Fallback reasoning when Kimi K3 is unavailable | OpenRouter credits | No |

**Total recurring cost:** Essentially free (Telegram is free). OpenRouter credit usage for Kimi K3 and its GLM-5.2 fallback.

---

## Operational Checkpoints

### Daily
- [ ] Agent responds correctly to messages
- [ ] Memory searches return relevant context
- [ ] No errors in Telegram polling
- [ ] Auto-diary compressed successfully

### Weekly
- [ ] Review captured lessons
- [ ] Check MemPalace integrity (no orphaned drawers)
- [ ] Verify cron jobs running
- [ ] Backup palace directory

### Monthly
- [ ] Analyze OpenRouter usage (Kimi K3 and GLM-5.2 fallback)
- [ ] Review subagent performance
- [ ] Audit self-improving lessons for patterns
- [ ] Update skill documentation

---

## Example: Complete Message Flow

**User sends:** "Create a summary of my last 5 meetings"

**Flow:**
```
1. OpenClaw receives message
2. Main agent (Lysander) spawns subagent
3. Subagent:
   - Searches MemPalace for recent meeting notes
   - Finds 5 relevant meetings
   - Calls Ollama to summarize (local, free)
4. Subagent returns summaries
5. Lysander receives results
6. Lysander adds summary to MemPalace
7. User receives formatted summary via Telegram
```

**Cost:** $0 (MemPalace search is free, Ollama is free)
**Time:** ~5-10 seconds
**Latency:** All work happens in background, user sees result asynchronously

---

## Why This Stack?

**Single-owner AI needs different priorities than multi-tenant SaaS:**

1. **Context awareness > General capabilities** — Agent is better if it remembers your preferences, not broader if it knows everything
2. **Privacy > Convenience** — Your context stays local
3. **Cost efficiency > Speed** — 5-second response is fine if it costs $0
4. **Reliability > Cutting edge** — Proven tools (Ollama, MemPalace) over beta research
5. **Compound learning > One-shot accuracy** — Agent gets better every session because it learns

This stack prioritizes all five.

---

_This architecture is designed to support single-owner AI agents at full capability with zero cloud dependencies for memory and inference._

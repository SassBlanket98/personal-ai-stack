# MemPalace Architecture — Agent Memory System Design

**Purpose:** Long-term, semantic memory system for AI agents with zero cloud dependency.

**Design Pattern:** Local palace with structured wings, rooms, and drawers. Each agent has its own isolated palace (no cross-agent data bleeding).

---

## Wing Structure — Mandatory Filing Rules

### 🏠 `home` — Owner's Personal/Life Context
**Rule:** Anything about the owner's personal life, hobbies, preferences, health, home.

| Room | Purpose |
|---|---|
| `household` | Rules, routines, schedules, domestic context |
| `pets` | Pet information, care requirements, personalities |
| `health` | Health tracking, fitness, wellness, research |
| `finance` | Financial context, accounts, budgets |
| `preferences` | Personal preferences, dislikes, communication style |
| `general` | Misc personal that doesn't fit above |

### 💼 `work` — Professional Context
**Rule:** Work projects, client work, professional responsibilities, career development.

| Room | Purpose |
|---|---|
| `infrastructure` | Work environment, systems, topology, services |
| `client-projects` | Client work, project requirements, deliverables |
| `inventory` | Hardware/software/service inventory |
| `career` | CV, skills, certifications, professional goals |
| `general` | Misc work context |

### 💻 `code` — Personal Coding Projects
**Rule:** Personal coding projects, hobby code, side projects. NOT work code (that goes in `work`).

| Room | Purpose |
|---|---|
| `active-projects` | Current personal projects in development |
| `archived-projects` | Completed or paused personal projects |
| `general` | Misc coding context |

### 🏛️ `wing_agent` — Agent's Operating Memory
**Rule:** Internal agent stuff — diary, decisions, rules, system archive.

| Room | Purpose |
|---|---|
| `diary` | Compressed diary entries (AAAK format or similar) |
| `decisions` | Operational decisions (config changes, strategy shifts) |
| `rules` | Permanent rules and constraints |
| `system` | System archive — setup docs, migration records |
| `corrections` | Lessons learned from mistakes and corrections |
| `guard_rails` | Safety constraints and boundaries |
| `tracking` | Ongoing tracking data (wellness, goals, metrics) |
| `general` | Misc agent-internal stuff |

### 📦 `integrations` — External System Context
| Room | Purpose |
|---|---|
| `google-workspace` | Gmail, Calendar, Drive lessons learned |
| `email-systems` | Email account setup, migration history |
| `dev-tools` | Git, CI/CD, deployment systems |
| `general` | Misc integration context |

### 🧠 `lessons` — Meta-Learning System
| Room | Purpose |
|---|---|
| `execution` | How to execute tasks better |
| `communication` | How to communicate better with owner |
| `config` | Config-related lessons |
| `architecture` | Architecture design lessons |

### 🛠️ `skills` — Installed Skills Tracking
| Room | Purpose |
|---|---|
| `milestones` | Skill installation records |
| `patterns` | Patterns discovered while using skills |

---

## Filing Decision Tree

When creating a new memory drawer:

1. **Is it about the owner's personal life?** (pets, health, home, preferences)
   → `home/<room>`

2. **Is it about their professional work or career?** (projects, clients, CV, certs)
   → `work/<room>`

3. **Is it a personal coding project?** (hobby code, side projects)
   → `code/<room>`

4. **Is it internal agent stuff?** (diary, decisions, rules, corrections)
   → `wing_agent/<room>`

5. **Is it about an external system or integration?** (Google Workspace, Git, etc.)
   → `integrations/<room>`

6. **Is it a lesson about how to do things better?**
   → `lessons/<room>`

7. **Is it a skill installation milestone?**
   → `skills/milestones`

**If none fit → STOP and ask owner where it belongs.**
**NEVER create new wings without owner's explicit approval.**

---

## Key Design Decisions

### Why Separate Wings (Not One Big Pile)?
Each wing has a clear boundary. Personal, work, code, agent ops, integrations, and lessons live separately so:
- The agent won't confuse contexts (e.g., personal preferences don't leak into work decisions)
- Information stays organized even at scale (palace can grow to 100K+ drawers)
- Other agents can use the same palace structure without data collision
- The agent can reason about what type of context it's accessing

### Why AAAK Diary Format?
Compressed: `[date] [context] [decision/lesson] [tags]`. Keeps diary entries searchable and concise. Auto-diary cron jobs can summarize sessions without creating bloat.

### Why Zero Cloud?
All data lives locally in the palace directory. No API calls for memory. Semantic search happens locally. No third-party access to personal context.

### Why Drawers (Not Files)?
The "drawer" abstraction allows the memory system to:
- Store any type of data (text, structured, nested)
- Scale to 100K+ entries without filesystem performance degradation
- Support semantic indexing without maintaining separate index files
- Handle deletion/archival cleanly

---

## Maintenance Rules

**Periodic Cleanup:**
- Delete raw code chunks (code lives in repos, not memory)
- Delete raw transcript dumps (diary summaries capture the essence)
- Keep hand-curated context, documentation, and lessons
- Archive completed project records

**Data Integrity:**
- No cross-agent data bleeding (strict tool prefix enforcement)
- No auto-delete of important context without explicit request
- All deletions logged and manifest-saved
- Regular integrity checks to catch orphaned drawers

---

## Example Memory Query Patterns

**"What are the owner's preferences around messaging?"**
→ Query `home/preferences` for tone, communication style, frequency

**"What's the status of X project?"**
→ Query `work/client-projects` or `code/active-projects`

**"What lessons have we learned about config changes?"**
→ Query `lessons/config` or `wing_agent/corrections`

**"What integration issues have we had in the past?"**
→ Query `integrations/<system>` for past failures and solutions

---

_This architecture is designed to support long-term, context-aware AI agents without cloud dependency. Customize the wings to match your own context structure._

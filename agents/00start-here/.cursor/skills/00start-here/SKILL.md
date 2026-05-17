---
name: 00start-here
description: >-
  Reads ~/ai status fresh each session—INDEX first, then prioritized snapshots,
  activity-based daily logs, and local plans. Recommends which agents to run
  and drafts paste-ready prompts. Never writes status. Use when returning to
  ~/ai, asking what's next, catch-up, run order, or which agent to start.
---

# 00start-here workflow

## Phase 1 — INDEX (mandatory)

Read `~/ai/status/INDEX.md`.

Extract:

- Every `active` plan (agent-role, plan id, date)
- Stale `active` (old date vs today)
- Multiple agents that may conflict on same work
- Recently `done` (only if user wants history)

Build a **read queue**: which snapshots, log files, and plan files are worth opening.

## Phase 2 — Prioritized reads

| Priority | Source | When |
|----------|--------|------|
| 1 | `~/ai/status/agents/<role>/snapshot.md` | Plan `active` for that role |
| 2 | `~/ai/status/YYYY-MM-DD.md` | Last days that role actually logged (see below) |
| 3 | `~/ai/agents/<role>/plans/<plan>.md` | Snapshot + log unclear, or competing plans |

### Activity-based log lookback

**Do not** default to today + yesterday.

1. From INDEX, get latest `date` per agent-role/plan.
2. Open daily logs on dates that agent **actually logged**, going back until you can explain current state (often 1–3 log days, gaps OK).
3. After a long absence, start from the **last meaningful log day**, not the calendar today.
4. If unsure: ask the user.

## Phase 3 — Synthesize

Produce in chat:

### 1. Situation summary

2–4 sentences: what the ecosystem looks like right now.

### 2. Recommended run order

```markdown
1. **<agent-role>** — <why now>
2. **<agent-role>** — <why next>
```

### 3. Tensions / stalls

- Competing plans (same repo, opposing goals)
- `active` but no recent log
- Waiting / blocked items from snapshots

### 4. Paste-ready prompts (L2)

One fenced block per suggested agent:

```markdown
### Paste into <agent-role>

**Context:** <from plan + log + snapshot>

**Continue from:** <specific next step>

**In scope:** <paths/repos>

**Out of scope:** <do not touch>

**Ask me if:** <decisions needed>
```

Keep prompts concise; user pastes into that agent's new chat.

## Phase 4 — Close

- Do **not** write files under `~/ai/status/`.
- Do **not** offer to mark plans done.
- Ask if user wants deeper reads (e.g. a specific plan file) before they switch agents.

## Quick triggers

User may say informally: "what's next", "catch me up", "who should I run", "I'm back after two weeks" — same workflow.

---
name: agent-generator
description: >-
  Creates and configures new ~/ai agent roles by interviewing the user and
  scaffolding AGENTS.md, Cursor rules, skills, and shared status symlinks. Use
  when the user wants a new agent, a new role, agent setup, role definition,
  or to update an existing agent's workspace.
---

# Agent Generator workflow

## Phase 1 — Intake interview

Ask in order; skip only if the user already answered.

### 1. Role identity

- **Agent name** (folder slug under `~/ai/agents/`, e.g. `implementer`, `investigator`)
- **Display name** (how it refers to itself in chat)
- **One-line purpose** — why this agent exists

### 2. Role type

Pick the closest fit (or combine with user approval):

| Type | Typical work | Default tools emphasis |
|------|----------------|------------------------|
| **Coding** | Implement, refactor, test, debug in repos | Code edit, terminal, linters |
| **Investigator** | Explore, trace bugs, compare options, report | Read, search, run read-only commands |
| **Planner** | Break down work, specs, acceptance criteria | Docs, diagrams, status updates |
| **Reviewer** | Critique changes, security, style | Diff review, no drive-by edits |
| **Ops / automation** | Scripts, CI, hooks, tooling | Terminal, `~/ai/tools/` if granted |

### 3. Project aim

- What personal project(s) will this agent serve?
- Is it tied to one repo, many repos, or only `~/ai` meta-work?
- **Agent role name** for INDEX and status (folder slug; hyphenate if needed, e.g. `agent-generator`)
- Optional **repo/product context** in plan prose—not a status bucket

### 4. Success criteria

Ask: *"When this agent finishes a typical task, what should be true?"*

Examples to offer:

- Coding: tests pass, PR-ready diff, documented handoff
- Investigator: written findings with evidence and recommended next steps
- Planner: actionable checklist another agent or you can execute

Record success criteria in the new agent's `AGENTS.md`.

### Plan completion

- Only the **user** sets plans to `done`, `superseded`, or `cancelled`.
- Teach every new agent: never self-close plans; ask when criteria look met.

### 5. Autonomy level

Ask the user to choose (or map their words to a level):

| Level | Behavior |
|-------|----------|
| **L0 — Ask first** | Propose plan; wait for approval before every significant action |
| **L1 — Plan then act** | Present plan once; proceed unless user objects |
| **L2 — Act with checkpoints** | Execute; pause at milestones or before destructive/risky steps |
| **L3 — Full autonomy** | Execute end-to-end within scope; only escalate true blockers |

Encode the chosen level in the new agent's `00-role.mdc` and `AGENTS.md`.

### 6. Visibility (required)

Every agent is one of:

| Type | Publish to public repo? |
|------|-------------------------|
| **private** | Never (personal agents, e.g. family/calendar) |
| **public** | Yes — full agent folder except `plans/` always stay local |
| **mixed** | Partial — publish structure/skills; exclude paths in `publish-exclude` (e.g. config, `*.local.md`) |

Record in `AGENTS.md` as `**Visibility:** private | public | mixed`.

Default new agents to **private** until user says otherwise.

### 7. Access scope

Confirm explicitly:

- Agent home: `~/ai/agents/<name>/` only?
- Project repo path(s)?
- Read/write `~/ai/status/` (INDEX, daily log, own snapshot)?
- `~/ai/tools/` — yes/no?
- Anything **forbidden** (e.g. no git push, no prod deploy)

---

## Phase 2 — Scaffold the agent workspace

Create `~/ai/agents/<agent-name>/` with:

```
<agent-name>/
├── AGENTS.md
├── plans/                          # local negotiated intent (style varies by agent)
└── .cursor/
    ├── rules/
    │   ├── 00-role.mdc
    │   ├── 01-ecosystem.mdc
    │   └── 02-shared-tracking.mdc  → symlink to ~/ai/status/shared-tracking.mdc
    └── skills/
        └── <agent-name>/
            └── SKILL.md              # optional; when workflow is long
```

### Symlink command

From `<agent-name>/.cursor/rules/`:

```bash
ln -sf ../../../../status/shared-tracking.mdc 02-shared-tracking.mdc
```

**Exception — `00start-here`:** No shared-tracking symlink, no `plans/`, no writes under `~/ai/status/`. Use read-only rules + `02-status-formats.mdc` instead (copy pattern from existing `00start-here` workspace).

### `AGENTS.md` template for new agents

```markdown
# <Display name>

<One-line purpose>

## Role type
<Type> — <short explanation>

## Visibility
private | public | mixed

## Publish exclude
( mixed only — paths never pushed to public repo, e.g. config/, *.local.md )

## Success
<What done looks like, from intake>

## Autonomy
Level <L0-L3>: <behavior summary from intake>

## Scope
- **In:** <paths>
- **Out:** <forbidden or out-of-scope>

## On session start
1. State role and autonomy level.
2. Ask what is in scope this session (paths, project slug, permissions).
3. If granted, read `~/ai/status/agents/<agent-role>/snapshot.md`, INDEX rows for self, and recent `~/ai/status/YYYY-MM-DD.md`.
4. Read relevant local `plans/` files; compare to status before acting.
5. Follow role skill: `.cursor/skills/<agent-name>/SKILL.md` (if present).
```

### `00-role.mdc` for new agents

- `alwaysApply: true`
- Identity, autonomy level, startup checklist, pointer to skill
- **No** full-`~/ai` access unless user explicitly granted it in intake

### `01-ecosystem.mdc` for new agents

- Copy the permission model from the generator's ecosystem rule but **remove** generator-only privileges
- List only this agent's allowed paths from intake

### Optional skill

Add `.cursor/skills/<agent-name>/SKILL.md` when the role has a multi-step workflow (investigation steps, coding definition of done, review checklist).

---

## Phase 3 — Verify with the user

Present:

1. Folder path and files created
2. Role summary (type, aim, success, autonomy, scope)
3. How to start a Cursor session in that folder
4. Reminder: first message should name agent role, attached repos, and status permissions

Offer to open the new `AGENTS.md` or adjust autonomy/scope.

---

## Updating an existing agent

1. Read current `AGENTS.md` and rules
2. Ask what changed (role, autonomy, scope, success criteria)
3. Edit in place; do not break the shared-tracking symlink
4. Append a note to the project's `log.md` if the user grants status access

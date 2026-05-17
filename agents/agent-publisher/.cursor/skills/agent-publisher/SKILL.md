---
name: agent-publisher
description: >-
  Publishes ~/ai to ai-personal (private backup) and
  role-based-agent-model-harness (public MIT framework). Builds confirmable
  publish plans, respects agent Visibility and dual gitignore files. Use for
  GitHub sync, backup, public release, or repo setup.
---

# agent-publisher workflow

## Phase 1 — Assess

Read:

- `~/ai/publish-manifest.md`
- `~/ai/.gitignore` and `~/ai/.gitignore.public`
- Each `~/ai/agents/*/AGENTS.md` for **Visibility** and **Publish exclude**

Determine:

- Drift since last publish (if known)
- Which repo(s) this session targets

## Phase 2 — Publish plan (required)

Create `plans/NNN_<slug>.md` with:

```markdown
# Publish plan NNN — <title>

**Targets:** ai-personal | role-based-agent-model-harness | both

## Private (ai-personal)
- [ ] Paths added/changed/removed
- [ ] Exclusions per ~/ai/.gitignore

## Public (role-based-agent-model-harness)
- [ ] Agents included (public/mixed only)
- [ ] Paths excluded (plans, private agents, status logs, mixed excludes)
- [ ] Docs/LICENSE/README changes

## Commands (draft)
- git / gh steps you intend to run

## Risks
- <force push, large delete, secret near-miss>

## User confirmation
- [ ] Approved
```

**Stop.** Wait for user approval before executing.

## Phase 3 — Execute (after approval)

### Private — `ai-personal`

- Repo root = `~/ai` (or documented subtree)
- Apply `~/ai/.gitignore` only
- Include plans, status, all agents, tools

### Public — `role-based-agent-model-harness`

Assemble tree (staging dir or branch) using:

1. `~/ai/.gitignore.public`
2. Per-agent Visibility
3. **Never** copy `plans/`

Suggested public layout:

```
LICENSE          # MIT
README.md
docs/            # framework how-to
agents/
  00start-here/
  agent-generator/
  agent-publisher/
status/
  shared-tracking.mdc
```

Add `LICENSE` (MIT) and README if missing on first publish.

### Git habits

- Prefer new branch + PR for public if user wants; direct push to private when approved
- Tag public releases only when user requests (e.g. `v0.1.0`)
- Never commit secrets; scan before push

## Phase 4 — Close

When user ends session: log status per shared-tracking (INDEX, daily log, snapshot).

Update `publish-manifest.md` if visibility or repo policy changed (with user OK).

## Standing rules

If user says "always backup private on wrap-up", record in `plans/` or `01-ecosystem` note—still skip public without explicit OK.

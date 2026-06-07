---
name: interactive-test-creator
description: >-
  Writes runnable scripts that exercise customer workflows for human review.
  Human decides pass/fail from output, checklists, and artifacts — not
  automated CI oracles. Use for staging validation, UX workflows, manual
  smoke scripts, or exploratory customer journey tests.
---

# interactive-test-creator playbook

## Phase 1 — Workflow intake

Capture in plan:

| Field | Example |
|-------|---------|
| **Workflow name** | “New user checkout” |
| **Actor** | Customer, admin, … |
| **Environment** | local :3000, staging URL |
| **Preconditions** | logged in, cart has item |
| **Success (human)** | What a human should see/confirm at end |

Ask: “What would make **you** say this workflow passed?”

## Phase 2 — Choose script style

| Stack hint | Style |
|------------|--------|
| Web UI | Playwright/Puppeteer **headed** or curl + open URL + checklist |
| CLI product | shell script calling CLI, echo steps |
| API-only | script prints request/response; human verifies JSON |
| Mobile / manual-heavy | step list + deep links; minimal automation |

Prefer **simplest** script that still saves the human time.

## Phase 3 — Write script + RUNBOOK

Each workflow folder:

**`RUNBOOK.md`**

```markdown
# Workflow: <name>

## Prerequisites
- ...

## Run
\`\`\`bash
./run.sh
\`\`\`

## What you'll see
1. Step ... — verify: ...

## Human verdict
- [ ] PASS  [ ] FAIL
Notes:
```

**Script behavior**

- Print `=== Step N: <action> ===` before each action
- Print `VERIFY: <what human should check>`
- Optional: `read -p "Press Enter when verified..."`
- Capture artifacts: `--screenshot`, tee log to `artifacts/run-<timestamp>.log`
- On script error: print context; **do not** claim FAIL — human still decides

**Avoid** as sole outcome: `exit 1` on assertion without review prompt — prefer “UNEXPECTED: … please review”.

## Phase 4 — Checklist template

```markdown
| Step | Expected (human) | Actual | OK? |
|------|------------------|--------|-----|
| 1    | ...              |        |     |
```

User fills **Actual** and **OK?** after run.

## Phase 5 — Optional link to automated tests

If a workflow should **later** become CI:

- Note in plan: “candidate for automated-test-creator package N”
- Do not duplicate full pyramid here — interactive proves intent first

## Phase 6 — Close

- User runs script once; confirms usable
- User marks plan `done`
- Log status on session end

---

## Plan template

```markdown
# Plan NNN — <workflow> interactive

**Status:** draft | active | done
**Repo:** <path>

## Workflow definition
...

## Script path
interactive/workflows/<slug>/run.*

## Human success criteria
...

## Success
- [ ] Script runs; runbook clear
- [ ] User marked done
```

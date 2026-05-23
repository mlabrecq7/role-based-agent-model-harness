---
name: project-rescue
description: >-
  Test-gated coding rescue for troubled repos. Asks three mandatory questions,
  writes a failing system test before any change, then fixes deps/code with
  minimal-code bias. Code is bad; dependencies worse; untested code evil. Use
  for broken builds, dep hell, migration, or loss of control—not feature work.
---

# project-rescue playbook

## Phase 0 — Three questions (mandatory)

Record answers in the rescue plan. **Stop** if any answer is vague—clarify with the user.

| # | Question |
|---|----------|
| 1 | What are you trying to do, and what isn't working about it? |
| 2 | Can you point at the code that is specifically not working as expected? |
| 3 | What test shall I run to ensure that when I fix this, the code is working? |

**If no test exists:** design and write a **system test** that exercises the behavior end-to-end (or as close as the stack allows). Name the file/path in the plan.

## Phase 1 — System test gate (before ANY change)

TDD order:

1. Define user outcome (black-box).
2. Write system test in **target repo** that will pass when outcome is met.
3. **Run test → must FAIL for the proper reason** (same failure mode user reported).
4. Only then change code / deps / infra until test passes.

```text
- [ ] System test written in target repo (user agrees on path)
- [ ] Run command documented in plan
- [ ] Test FAILS
- [ ] Failure is the PROPER reason (matches §1 — not a broken test or unrelated error)
- [ ] Expected vs actual noted in plan (paste failure line)
```

**Do not proceed until all boxes are true.**

| Failure type | Action |
|--------------|--------|
| **Proper** — matches user’s broken behavior | Proceed to Phase 4 |
| **Wrong** — typo, missing env, assertion on implementation | Fix the **test** first |
| **Passes while bug exists** — test codifies the bug | Rewrite the test (never “fix” prod to match) |

If the test fails for the *wrong* reason, fix the test first.

## Phase 2 — Intake & rescue plan

Add to plan:

- Repo path, goal (stabilize | migrate to X)
- Answers to three questions
- Pointer to broken code (file:line or symbol)
- System test path and how to run it
- Phases for deps/code **after** gate

Get informal approval → plan status `active`.

## Phase 3 — Inventory (read-only until gate passed)

Same as before, but **no modifications** during inventory:

- Lockfiles, manifests, CI, Docker
- Direct vs transitive deps
- Classify: required | removable | replaceable | vendor | external

Bias: **remove** before **add**. Fewer lines beats clever abstractions.

## Phase 4 — Execute (only after gate)

Order of attack:

1. Remove dead deps/code (checkpoint if large)
2. Pin floats; shrink transitive surface
3. Minimal code changes to make system test pass
4. Re-run **full** test suite if repo has one; system test must stay green

Checkpoint before: mass delete, major bump, runtime change, new dependency.

**New dependency rule:** justify in plan why in-repo or pin is impossible.

## Phase 5 — Verify & close

```text
- [ ] System test passes
- [ ] Other tests pass or gaps documented
- [ ] Build/install from lockfile
- [ ] Remaining externals listed in plan
```

Success requires **system test green**. Ask user to mark plan `done`.

Log status when user ends session (shared-tracking).

---

## Rescue plan template

```markdown
# Plan NNN — <repo> rescue

**Status:** draft | active | done
**Repo:** <path>

## 1. What are you trying to do, and what isn't working?
<answer>

## 2. Broken code (pointer)
<file:line or symbol — user must be able to point>

## 3. Test that proves the fix
- **Existing test:** <path> — run: `<command>`
- **System test (written by agent):** <path> — run: `<command>`
- **Gate:** [ ] written  [ ] fails for right reason  [ ] passes at end

## Goal
stabilize | migrate to <target>

## Inventory
(read-only notes)

## Phases (after gate only)
### Deps / control
### Minimal code fix
### Verify

## Remaining externals

## Success criteria
- [ ] System test passed (required)
- [ ] User marked plan done
```

---

## System test guidance

### What to test

- **One contract** per rescue: the outcome the user needs (e.g. “daily story delivery is not blocked because storage is unavailable”).
- Test **fails** while that outcome is missing; **passes** when fixed — without changing the test to match broken behavior.

### How to write (black-box)

- Exercise the system at its **boundary**: HTTP API, CLI, browser flow, or job entrypoint — not internal classes.
- **No** mocking the failure path and asserting the failure happened (that only proves the bug, not the fix).
- **No** assertions on implementation details (function names, storage adapter types, internal error codes) unless the user’s contract is explicitly that API.
- Prefer the project’s existing test runner and conventions.

### How to run

Record in the plan:

```text
cd <target-repo>/<package> && <runner> <path-to-system-test>
```

Examples:

- `npm test -- src/__tests__/story-delivery.storage.system.test.ts`
- `pytest tests/system/test_rescue_storage.py -q`
- `go test ./... -run TestRescueStorage`

Gate step: run once, confirm **proper** failure, paste the failure message into the plan.

### Optional: deployed target

When local env files omit production secrets, allow an env var (e.g. `RESCUE_SYSTEM_BASE_URL`) so the same black-box test hits staging/production and fails for the **proper** reason there. Document in the plan.

### Placement

If the repo has no convention: `tests/system/test_rescue_<short-name>.py` or `__tests__/<feature>.system.test.ts`.

# Role-based agent model harness

MIT-licensed framework for building and running **role-based AI agents** in Cursor (and similar environments).

## What's in this repo

- **`agents/`** — shareable agent roles:
  - `00start-here` — session routing
  - `agent-generator` — scaffold new role agents
  - `project-rescue` — get a troubled codebase buildable and testable
  - `refactoragent` — focused refactors with clear scope
- **`status/shared-tracking.mdc`** — cross-agent plans and status conventions
- **`publish-manifest.md`** — what maps to private vs public GitHub repos

Personal plans, live daily logs, and private agents (e.g. publishing tooling) stay in your private backup repo (`ai-personal`), not here.

## Quick start

1. Copy or clone this repo (or just the `agents/` tree you need).
2. Open an agent folder in Cursor as the workspace root (e.g. `agents/00start-here/`).
3. Read that agent's `AGENTS.md` and `.cursor/rules/` — follow the skill for your task.

## Layout

```
agents/
  00start-here/
  agent-generator/
  project-rescue/
  refactoragent/
status/
  shared-tracking.mdc
publish-manifest.md
```

## License

MIT — see [LICENSE](LICENSE).

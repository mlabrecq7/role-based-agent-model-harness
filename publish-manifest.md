# Publish manifest

Maps `~/ai` to GitHub repos. Maintained by **agent-publisher**; confirm changes via publish plans.

## Repos

| Repo | Remote name (draft) | License | Source |
|------|---------------------|---------|--------|
| `ai-personal` | private backup | n/a | All of `~/ai/` minus `~/ai/.gitignore` |
| `role-based-agent-model-harness` | public framework | MIT | Filtered export via `~/ai/.gitignore.public` + agent Visibility |

## Agent visibility (see each `AGENTS.md`)

| Agent | Visibility |
|-------|------------|
| `00start-here` | public |
| `agent-generator` | public |
| `agent-publisher` | private — never export |
| *(other private agents)* | private — never export |

## Never publish (any repo)

- `**/plans/**`
- Agents with `Visibility: private`
- Secrets (see `~/ai/.gitignore`)

## Public export includes

- `shared-tracking.mdc` (via symlink targets / copy)
- Public + mixed agents (mixed: minus `## Publish exclude` paths)
- Framework docs (add under `docs/` in public tree as publisher defines)

## Ignore files

| File | Used for |
|------|----------|
| `~/ai/.gitignore` | `ai-personal` |
| `~/ai/.gitignore.public` | `role-based-agent-model-harness` assembly |

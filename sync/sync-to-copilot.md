---
name: sync-to-copilot
description: Sync all skills to GitHub Copilot — VS Code prompt files and Copilot CLI agents
---

Sync every skill from this repo's `skills/` directory to all enabled Copilot targets.
Configuration lives in `config/copilot.md`. Read it first to determine which scopes are active.

---

## Step 1 — Read configuration

Read `config/copilot.md` and note:
- Which scopes are enabled (`vscode_user`, `copilot_cli`, `project`, or any combination)
- Install paths for each active scope
- The `prompt_mode` to use (`agent` or `ask`)

If `project.enabled` is true but `project.path` is empty, ask the user for the target project root.

---

## Step 2 — Read all skills

Read every `.md` file in `skills/`. For each file extract:
- `name` — from frontmatter
- `description` — from frontmatter
- Body — everything after the closing `---` of the frontmatter, verbatim

Do not read or sync anything from `sync/`, `config/`, or `templates/`.

---

## Step 3 — VS Code user scope (`vscode_user`)

Resolve the install path for the current OS:

| OS | Path |
|---|---|
| macOS | `~/Library/Application Support/Code/User/prompts` |
| Windows | `%APPDATA%\Code\User\prompts` |
| Linux | `~/.config/Code/User/prompts` |

If the directory does not exist, create it.

For each skill write `<skill-name>.prompt.md` using this format:

```
---
description: '<description from skill frontmatter>'
mode: '<agent or ask — from config prompt_mode>'
---

<prompt body — verbatim>
```

Overwrite any existing file.

---

## Step 4 — Copilot CLI scope (`copilot_cli`)

For each skill write `~/.copilot/agents/<skill-name>.md` using this format:

```markdown
---
name: <skill-name>
description: <description from skill frontmatter>
---

<prompt body — verbatim>
```

Create `~/.copilot/agents/` if it does not exist. Overwrite any existing file.

This registers each skill as a named agent in Copilot CLI, accessible via:
- `copilot --agent <skill-name>` from the terminal
- `/agent` picker inside an interactive session

Also update `~/.copilot/AGENTS.md` to embed all skills as named sections (kept as a fallback
for tools that read AGENTS.md but do not support the agents directory):

```markdown
# Custom Skills

These skills are always available. Invoke them by typing their name (e.g. `/review`).

---

## /<skill-name>

<description from frontmatter>

<prompt body — verbatim>
```

Overwrite AGENTS.md entirely — do not append.

---

## Step 5 — Project scope (`project`)

Install path: `<project.path>/.github/prompts/`. Create it if it does not exist.

For each skill write `<skill-name>.prompt.md` using the same format as the VS Code scope.

---

## Step 6 — Report results

Print a summary table listing every skill and the status in each active scope:

| Skill | VS Code user | Copilot CLI | Project |
|---|---|---|---|
| `review` | ✓ written | ✓ embedded | — |
| `dev-start` | ✓ written | ✓ embedded | — |

Then remind the user:
- **VS Code**: skills appear in Copilot Chat via `/` (prompt picker) or agent mode
- **CLI**: invoke with `copilot --agent <name>` or `/agent` picker inside a session
- VS Code 1.96+ required for user-level `.prompt.md` files

---

## Notes

- Only files in `skills/` are synced. `sync/`, `config/`, and `templates/` are excluded.
- Preserve every prompt body exactly — do not rewrite, summarize, or add to it.
- Agent files live at `~/.copilot/agents/<name>.md` with name+description frontmatter.
- `~/.copilot/AGENTS.md` is kept as a fallback for instruction-based loading.

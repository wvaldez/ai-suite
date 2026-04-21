# Copilot Sync Configuration

Defines where `sync/sync-to-copilot.md` installs skill files.
Edit this file to change install targets. All scopes can be active simultaneously.

---

## Install scopes

```yaml
scopes:
  # VS Code user-level: skills available in Copilot Chat across ALL workspaces
  vscode_user:
    enabled: true
    paths:
      macos:   "~/Library/Application Support/Code/User/prompts"
      windows: "%APPDATA%\\Code\\User\\prompts"
      linux:   "~/.config/Code/User/prompts"
      # VS Code Insiders (uncomment if using Insiders build):
      # macos: "~/Library/Application Support/Code - Insiders/User/prompts"

  # Copilot CLI: each skill becomes a named agent at ~/.copilot/agents/<name>.md
  # Invoke with: copilot --agent <name>  or  /agent picker inside a session
  copilot_cli:
    enabled: true
    agents_dir: "~/.copilot/agents"
    agents_md_fallback: "~/.copilot/AGENTS.md"

  # Project-level: skills available only inside a specific workspace
  project:
    enabled: false
    # Path to the target project root. Prompts land at <path>/.github/prompts/
    path: ""
```

---

## Prompt mode

Controls how skills appear in VS Code Copilot Chat:

```yaml
prompt_mode:
  # 'agent' — skill appears as an agent-mode prompt (can run tools, multi-step)
  # 'ask'   — skill appears as a regular /ask prompt (simpler, no tool calls)
  default: agent
```

---

## How each scope works

### VS Code (vscode_user)
Each skill becomes a `<name>.prompt.md` file with `mode: agent` frontmatter.
Accessible in Copilot Chat via the `/` prompt picker or agent mode.
Requires VS Code 1.96+ with the GitHub Copilot Chat extension.

### Copilot CLI (copilot_cli)
Each skill becomes `~/.copilot/agents/<name>.md` with name+description frontmatter.
The CLI reads this directory and registers each file as a named agent.

Invoke with:
- `copilot --agent <name>` from the terminal
- `/agent` picker inside an interactive session

`~/.copilot/AGENTS.md` is also written as a fallback for instruction-based loading.

### Project (project)
Each skill becomes a `.github/prompts/<name>.prompt.md` file in the target project.
Only available inside that workspace.

---

## Notes

- After editing this file, re-run `sync-to-copilot` to apply changes.
- The `~/.zshrc` line `export COPILOT_CUSTOM_INSTRUCTIONS_DIRS="$HOME/.copilot"` must
  be present for CLI auto-loading. It was added automatically during the first sync.

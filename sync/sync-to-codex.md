---
name: sync-to-codex
description: Sync all skills to an AGENTS.md file for OpenAI Codex CLI in a target project
---

Sync every skill from this repo's `skills/` directory into a target project as a single `AGENTS.md` file for the OpenAI Codex CLI.

## Steps

1. Ask the user for the target project root path if not already known from context.
2. Read every `.md` file in `skills/`.
3. Create or overwrite `<target>/AGENTS.md` with this structure:

```
# AGENTS.md

<skill name>

> <description from skill frontmatter>

<prompt body — everything after the frontmatter block, unchanged>

---

## <next skill name>

...
```

4. If an `AGENTS.md` already exists in the target project, overwrite only the skills section. Preserve any project-specific content already in the file by appending skills after a `## Skills` heading.
5. After writing the file, confirm how many skills were included.

## Notes

- Only process files in `skills/`. Do not sync anything from `sync/`.
- Preserve each prompt body exactly — do not rewrite, summarize, or add to it.
- `AGENTS.md` has no YAML frontmatter — plain markdown only.
- Codex CLI also reads `~/.codex/AGENTS.md` for global instructions. If the user wants these skills available globally across all projects, write to that path instead.
- The combined file must stay under 32 KiB — warn the user if it approaches this limit.

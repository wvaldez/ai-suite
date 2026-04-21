---
name: sync-to-copilot
description: Sync all skills to GitHub Copilot prompt files in a target project
---

Sync every skill from this repo's `skills/` directory into a target project as GitHub Copilot prompt files.

## Steps

1. Ask the user for the target project root path if not already known from context.
2. Read every `.md` file in `skills/`.
3. For each skill, create a file at `<target>/.github/prompts/<skill-name>.prompt.md` using this format:

```
---
description: '<description value from the skill frontmatter>'
agent: 'agent'
---

<prompt body — everything after the frontmatter block, unchanged>
```

4. If `.github/prompts/` does not exist in the target project, create it.
5. If a prompt file already exists for a skill, overwrite it.
6. After writing all files, list what was created or updated.

## Notes

- Only process files in `skills/`. Do not sync anything from `sync/`.
- Preserve the prompt body exactly — do not rewrite, summarize, or add to it.
- The `agent: 'agent'` frontmatter key is required by VS Code Copilot Chat for the file to appear as an agent prompt.
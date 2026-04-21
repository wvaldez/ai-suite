---
name: sync-to-cursor
description: Sync all skills to Cursor rules in a target project
---

Sync every skill from this repo's `skills/` directory into a target project as Cursor rule files.

## Steps

1. Ask the user for the target project root path if not already known from context.
2. Read every `.md` file in `skills/`.
3. For each skill, create a file at `<target>/.cursor/rules/<skill-name>.mdc` using this format:

```
---
description: '<description value from the skill frontmatter>'
alwaysApply: false
---

<prompt body — everything after the frontmatter block, unchanged>
```

4. If `.cursor/rules/` does not exist in the target project, create it.
5. If a rule file already exists for a skill, overwrite it.
6. After writing all files, list what was created or updated.

## Notes

- Only process files in `skills/`. Do not sync anything from `sync/`.
- Preserve the prompt body exactly — do not rewrite, summarize, or add to it.
- `alwaysApply: false` means Cursor will apply the rule intelligently based on context. Set to `true` only if the skill should be active in every session.
- The `description` field is what Cursor uses to decide when to apply the rule — keep it specific.

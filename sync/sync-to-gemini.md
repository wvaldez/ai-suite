---
name: sync-to-gemini
description: Sync all skills to a Gemini Code Assist instructions file in a target project
---

Sync every skill from this repo's `skills/` directory into a target project as a single Gemini Code Assist instructions file.

## Steps

1. Ask the user for the target project root path if not already known from context.
2. Read every `.md` file in `skills/`.
3. Create or overwrite the file at `<target>/.gemini/instructions.md` with this structure:

```
# Skills

## <skill name>

> <description from skill frontmatter>

<prompt body — everything after the frontmatter block, unchanged>

---

## <next skill name>

...
```

4. If `.gemini/` does not exist in the target project, create it.
5. After writing the file, confirm how many skills were included.

## Notes

- Only process files in `skills/`. Do not sync anything from `sync/`.
- Preserve each prompt body exactly — do not rewrite, summarize, or add to it.
- All skills are combined into a single file. Gemini Code Assist reads `.gemini/instructions.md` as workspace-level custom instructions.
- Use a `---` horizontal rule as a separator between skills for readability.

# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Purpose

Provider-agnostic AI skill definitions. Skills are pure markdown prompts that work with Claude Code. Sync skills in `sync/` translate them into the format expected by other AI coding assistants.

## Structure

```
skills/                        ← workflow skills (the actual capabilities)
sync/                          ← meta-skills that sync skills/ to other AI assistants
templates/                     ← reusable document templates referenced by skills (e.g. plan.md)
config/                        ← configuration consumed by skills at runtime
  copilot.md                   ← Copilot install scope settings
  ticket-managers/
    atlassian.md               ← Jira/acli config (set enabled: true to activate)
    azure-devops.md            ← Azure DevOps MCP config (set enabled: true to activate)
```

## Ticket manager configuration

Skills that interact with a ticket system (e.g. `dev-start`) read `config/ticket-managers/` to determine which tool to use. Exactly one file should have `enabled: true` at a time. The enabled file provides all connection settings, state/transition names, and branch type mappings the skill needs — so the skill body stays tool-agnostic.

To add a new ticket manager, create `config/ticket-managers/<name>.md` following the same structure as the existing files.

## Skill file format

Every file in `skills/` must have this frontmatter:

```markdown
---
name: skill-name
description: One-line description of what the skill does
---

Prompt body here.
```

The body is a pure prompt — no provider-specific syntax.

## Adding a new skill

Create a `.md` file in `skills/` with the required frontmatter. Then run the relevant sync skill to propagate it to other assistants.

## Adding a new AI assistant target

Create a `sync/sync-to-<assistant>.md` file that describes how to read `skills/` and write the target format. Follow `sync/sync-to-copilot.md` as a reference.

<!-- sync/ and config/copilot.md are meta-skills for repo maintainers.
     They are not needed in normal development sessions — do not load them proactively. -->
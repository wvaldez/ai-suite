---
name: dev-start
description: Full development kickoff workflow — read ticket, assign, branch, and produce a reviewed implementation plan
---

Kick off development for a ticket from first principles. This skill drives the entire pre-coding phase: read the ticket, assign it, move it to in progress, create and check out the branch, then produce a structured implementation plan ready for review or immediate execution.

---

## Step 1 — Load the ticket manager configuration

Read every file in `config/ticket-managers/` from the ai-skills repo. Find the one with `enabled: true`. That file defines which tool and settings to use for all ticket operations in this session.

Store the resolved values as:
- `{TM_TOOL}` — `acli` or `azure-devops-mcp`
- `{TM_SITE}` or `{TM_ORG}` — the base URL
- `{TM_PROJECT}` — default project key or project name
- `{TM_IN_PROGRESS}` — transition name or state name for active development
- `{TM_BRANCH_TYPE_MAP}` — work item type → branch prefix mapping

If no config has `enabled: true`, ask the user:
> "No ticket manager is configured. Is this a Jira or Azure DevOps project? I'll walk you through enabling the right config in `config/ticket-managers/`."

If more than one has `enabled: true`, stop and tell the user only one should be enabled at a time.

---

## Step 2 — Read the ticket

Ask the user for the ticket ID (e.g., `PROJ-123` or `12345`).

### If TM_TOOL is `acli` (Jira)

```bash
acli jira --action getIssue --issue {TICKET_ID}
```

Capture: title, description, acceptance criteria, reporter, current assignee, current status, issue type, labels, and any linked issues.

### If TM_TOOL is `azure-devops-mcp` (Azure DevOps)

Call the MCP tool `get_work_item` with `id: {TICKET_ID}`.

Capture: title, description, acceptance criteria, assigned to, state, work item type, tags, and related work items.

Print a concise summary of what you read so the user can confirm you understood the ticket correctly before proceeding.

---

## Step 3 — Assign the ticket

Ask the user: "Who should this be assigned to?" (default: current user — say "me" to use yourself).

### Jira

```bash
acli jira --action assignIssue --issue {TICKET_ID} --userId {USERNAME}
```

### Azure DevOps

Call MCP `update_work_item` with `id: {TICKET_ID}` and `assignedTo: {USERNAME}`.

---

## Step 4 — Move the ticket to In Progress

### Jira

```bash
acli jira --action transitionIssue --issue {TICKET_ID} --transition "{TM_IN_PROGRESS}"
```

If that transition name fails, list available transitions first and pick the one representing active development:

```bash
acli jira --action getTransitions --issue {TICKET_ID}
```

### Azure DevOps

Call MCP `update_work_item` with `id: {TICKET_ID}` and `state: "{TM_IN_PROGRESS}"`.

---

## Step 5 — Determine the base branch

Ask the user:

> "What is the base branch for this ticket? (develop / master / main / other)"

If they say "other", ask for the exact branch name. Store as `{BASE_BRANCH}`.

---

## Step 6 — Create and check out the branch

Derive the branch prefix from the ticket's work item type using `{TM_BRANCH_TYPE_MAP}`. If the type is not in the map, default to `feature`.

Branch name format: `{type}/{TICKET_ID}-{slug}`
- `{slug}` = 3–5 word kebab-case summary of the ticket title
- Example: `feature/PROJ-123-add-user-export`

```bash
git fetch origin {BASE_BRANCH}
git checkout -b {BRANCH_NAME} origin/{BASE_BRANCH}
```

Confirm the branch was created and is currently checked out.

---

## Step 7 — Produce the implementation plan

Using all information gathered from the ticket, fill out every section of the plan template at `templates/plan.md` in the ai-skills repo. Write the completed plan to `PLAN.md` at the root of the target project.

Populate:
- **Summary** — distill the ticket's intent in 2–4 sentences.
- **Scope** — explicitly state what is and is not being changed.
- **Acceptance Criteria** — restate from the ticket, add any implied criteria.
- **Implementation Changes** — enumerate files and components likely touched, grouped by concern. Infer from the codebase if you have access; otherwise list by feature area.
- **Testing Strategy** — describe how manual and automated testing will validate the changes.
- **Unit Tests to Add** — name specific test scenarios with expected outcomes.
- **Open Questions & Risks** — flag any unknowns, missing requirements, or risky assumptions.
- **Rollback Plan** — describe how to revert safely if needed.
- **Notes** — capture any decisions made during planning or relevant references.

---

## Step 8 — Commit the plan

```bash
git add PLAN.md
git commit -m "plan({TICKET_ID}): implementation plan for {TICKET_TITLE}"
```

Follow the project's existing commit convention if one exists; use the format above as fallback.

---

## Step 9 — Report to the user

Print a summary:

- Ticket manager: `{TM_TOOL}`
- Ticket: `{TICKET_ID}` — `{TICKET_TITLE}`
- Assigned to: `{ASSIGNEE}`
- Status: moved to In Progress
- Branch: `{BRANCH_NAME}` (from `{BASE_BRANCH}`)
- Plan: committed to `PLAN.md`

Then ask:

> "The plan is ready. Would you like to review it together, adjust any section, or proceed straight to implementation?"

Do not start writing code until the user explicitly says to proceed.

---

## Error handling

| Problem | Action |
|---|---|
| `acli` not installed | Tell user: `brew install acli` |
| Azure DevOps MCP not connected | Tell user to connect the MCP server in their AI tool's settings |
| Transition / state name not found | List available options and ask the user to pick |
| `PLAN.md` already exists on branch | Ask before overwriting |

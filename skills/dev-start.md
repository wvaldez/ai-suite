---
name: dev-start
description: Full development kickoff workflow — read ticket, assign, branch, and produce a reviewed implementation plan
argument-hint: "[TICKET_ID] [base-branch]"
disable-model-invocation: true
allowed-tools: Bash(git *) Bash(acli *) Read Write
---

Kick off development for a ticket from first principles. This skill drives the entire pre-coding phase: read the ticket, assign it, move it to in progress, create and check out the branch, then produce a structured implementation plan ready for review or immediate execution.

**Arguments:** `$ARGUMENTS[0]` = ticket ID (optional), `$ARGUMENTS[1]` = base branch (optional).

---

## Step 1 — Load the ticket manager configuration

Read every file in `config/ticket-managers/` from the ai-skills repo. Find the one with `enabled: true`. That file defines which tool and settings to use for all ticket operations in this session.

Store the resolved values as:
- `{TM_TOOL}` — `acli` or `azure-devops-mcp`
- `{TM_SITE}` or `{TM_ORG}` — the base URL
- `{TM_PROJECT}` — default project key or project name
- `{TM_IN_PROGRESS}` — transition name or state name for active development
- `{TM_IN_REVIEW}` — transition name or state name for awaiting review
- `{TM_BRANCH_TYPE_MAP}` — work item type → branch prefix mapping

If no config has `enabled: true`, ask the user:
> "No ticket manager is configured. Is this a Jira or Azure DevOps project? I'll walk you through enabling the right config in `config/ticket-managers/`."

If more than one has `enabled: true`, stop and tell the user only one should be enabled at a time.

Also load `config/ticket-managers/ops.md` — it contains the commands for all ticket operations.

---

## Step 2 — Read the ticket

Ticket ID: use `$ARGUMENTS[0]` if provided, otherwise ask the user (e.g., `PROJ-123` or `12345`).

Using `{TM_TOOL}`, run the **Read ticket** operation from `config/ticket-managers/ops.md`.

Capture: title, description, acceptance criteria, assignee, status, issue type, labels, linked items. Print a concise summary for the user to confirm before proceeding.

---

## Step 3 — Assign the ticket

Ask the user: "Who should this be assigned to?" (default: current user — say "me" to use yourself).

Using `{TM_TOOL}`, run the **Assign ticket** operation from `config/ticket-managers/ops.md`.

---

## Step 4 — Move the ticket to In Progress

Using `{TM_TOOL}`, run the **Move to In Progress** operation from `config/ticket-managers/ops.md`.

If the transition name fails (Jira), run the **List transitions** operation and pick the one representing active development.

---

## Step 5 — Determine the base branch

Base branch: use `$ARGUMENTS[1]` if provided, otherwise ask:
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

Using all information gathered from the ticket, fill out every section listed in `templates/plan.md`. Write the completed plan to `PLAN.md` at the root of the target project.

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

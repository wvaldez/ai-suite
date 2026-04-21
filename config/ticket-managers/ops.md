# Ticket Manager — Operations Reference

Use the operation that matches `{TM_TOOL}` from the enabled ticket manager config.

---

## Read ticket

**Jira (`acli`):**
```bash
acli jira --action getIssue --issue {TICKET_ID}
```

**Azure DevOps (MCP):** Call `get_work_item` with `id: {TICKET_ID}`

---

## Assign ticket

**Jira:**
```bash
acli jira --action assignIssue --issue {TICKET_ID} --userId {USERNAME}
```

**Azure DevOps:** Call `update_work_item` with `id: {TICKET_ID}` and `assignedTo: {USERNAME}`

---

## Move to In Progress

**Jira:**
```bash
acli jira --action transitionIssue --issue {TICKET_ID} --transition "{TM_IN_PROGRESS}"
```

**Azure DevOps:** Call `update_work_item` with `id: {TICKET_ID}` and `state: "{TM_IN_PROGRESS}"`

---

## Move to In Review

**Jira:**
```bash
acli jira --action transitionIssue --issue {TICKET_ID} --transition "{TM_IN_REVIEW}"
```

**Azure DevOps:** Call `update_work_item` with `id: {TICKET_ID}` and `state: "{TM_IN_REVIEW}"`

---

## List transitions (Jira only — use when a transition name fails)

```bash
acli jira --action getTransitions --issue {TICKET_ID}
```

---

## Add comment

**Jira:**
```bash
acli jira --action addComment --issue {TICKET_ID} --comment "{TEXT}"
```

**Azure DevOps:** Call `add_work_item_comment` with `id: {TICKET_ID}` and `text: "{TEXT}"`

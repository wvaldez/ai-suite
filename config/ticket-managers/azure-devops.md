# Ticket Manager — Azure DevOps

```yaml
enabled: false
tool: azure-devops-mcp
```

---

## Connection

```yaml
organization: "https://dev.azure.com/your-org"
project: "YourProject"
```

---

## Workflow states

```yaml
# State names used to move a work item through the board.
# These vary by process template (Agile, Scrum, CMMI).
in_progress_state: "Active"      # Agile: "Active"    | Scrum: "In Progress" | CMMI: "Active"
in_review_state:   "In Review"   # Agile: "In Review" | Scrum: "In Review"   | CMMI: "In Review"
done_state: "Closed"             # Agile: "Closed"    | Scrum: "Done"        | CMMI: "Closed"
```

---

## Field mappings

```yaml
# Azure DevOps work item types that map to each branch prefix
branch_type_map:
  "User Story": feature
  Bug:          fix
  Task:         chore
  Feature:      feature
  Epic:         feature
  Issue:        fix
```

---

## MCP tool reference

All operations use the Azure DevOps MCP server. The MCP server must be connected before running any skill.

| Operation | MCP action |
|---|---|
| Read work item | `get_work_item` with `id: {TICKET_ID}` |
| Assign work item | `update_work_item` with `assignedTo: {USERNAME}` |
| Move to In Progress | `update_work_item` with `state: "{in_progress_state}"` |
| Move to In Review | `update_work_item` with `state: "{in_review_state}"` |
| Add comment | `add_work_item_comment` with `text: "{TEXT}"` |
| List work items | `list_work_items` with `project: "{project}"` |

---

## Notes

- Set `enabled: true` to activate. Only one ticket manager should be enabled at a time.
- `in_progress_state` must match exactly what your ADO process template calls it.
- If the MCP server is not connected, tell the user to add it via their AI tool's MCP settings.

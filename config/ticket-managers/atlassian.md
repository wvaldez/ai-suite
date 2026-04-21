# Ticket Manager — Atlassian (Jira)

```yaml
enabled: false
tool: acli
```

---

## Connection

```yaml
site: "https://your-org.atlassian.net"
project_key: "PROJ"
```

---

## Workflow states

```yaml
# Transition name used to move a ticket to active development.
# Run `acli jira --action getTransitions --issue <any-issue>` to list your project's transitions.
in_progress_transition: "In Progress"
in_review_transition: "In Review"
done_transition: "Done"
```

---

## Field mappings

```yaml
# Jira issue types that map to each branch prefix
branch_type_map:
  Story:   feature
  Bug:     fix
  Task:    chore
  Hotfix:  hotfix
  Spike:   chore
```

---

## CLI reference

All operations use the Atlassian CLI (`acli`). Install: `brew install acli`.

| Operation | Command |
|---|---|
| Read ticket | `acli jira --action getIssue --issue {TICKET_ID}` |
| Assign ticket | `acli jira --action assignIssue --issue {TICKET_ID} --userId {USERNAME}` |
| List transitions | `acli jira --action getTransitions --issue {TICKET_ID}` |
| Move to In Progress | `acli jira --action transitionIssue --issue {TICKET_ID} --transition "{in_progress_transition}"` |
| Move to In Review | `acli jira --action transitionIssue --issue {TICKET_ID} --transition "{in_review_transition}"` |
| Add comment | `acli jira --action addComment --issue {TICKET_ID} --comment "{TEXT}"` |

---

## Notes

- Set `enabled: true` to activate. Only one ticket manager should be enabled at a time.
- `project_key` is used as the default when the ticket ID has no prefix.
- `in_progress_transition` and `in_review_transition` must match exact transition names in your Jira workflow.

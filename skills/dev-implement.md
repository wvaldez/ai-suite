---
name: dev-implement
description: Execute the implementation plan — code changes, tests, local validation, commit, push, and PR creation
---

Execute the implementation defined in `PLAN.md`. This skill picks up where `dev-start` left off: it reads the plan, applies all code changes, validates compilation and tests at each stage, guides local testing, then commits, pushes, and opens a PR.

---

## Step 1 — Load context

Read the following from the current project:

1. `PLAN.md` — the full implementation plan produced by `dev-start`
2. The current git branch name — confirm it matches the plan's ticket ID
3. The project's language, framework, build tool, and test runner — infer from lock files, project files, and existing test structure

If `PLAN.md` is missing, stop and tell the user:
> "No PLAN.md found in the project root. Run `dev-start` first to generate the implementation plan."

Print a one-line confirmation: the ticket, branch, and the number of implementation changes listed in the plan.

---

## Step 2 — Implement changes

Work through the **Implementation Changes** table in `PLAN.md` one concern at a time. For each group:

1. State which files you are about to touch and what you will do.
2. Apply the changes.
3. After each group, run a compilation/build check (see below) and fix any errors before moving to the next group.

Do not batch all changes at once. Complete one concern, validate, then continue.

### Compilation check by ecosystem

| Ecosystem | Command |
|---|---|
| .NET / C# | `dotnet build` |
| Node / TypeScript | `tsc --noEmit` or `npm run build` |
| Java / Maven | `mvn compile -q` |
| Java / Gradle | `./gradlew compileJava` |
| Python | `python -m py_compile **/*.py` |
| Go | `go build ./...` |
| Rust | `cargo check` |

If no build command is detected, ask the user: "What command builds this project?"

If compilation fails, fix the errors before proceeding — do not continue to the next concern with a broken build.

---

## Step 3 — Write unit tests

Using the **Unit Tests to Add** table from `PLAN.md`, create test files or add test cases to existing files. For each test scenario listed:

1. Write the test case covering the scenario and expected outcome.
2. Follow the project's existing test conventions (naming, folder structure, assertion style).
3. Do not write tests that duplicate existing coverage — check existing test files first.

---

## Step 4 — Run the full test suite

Run all tests (existing + new):

| Ecosystem | Command |
|---|---|
| .NET | `dotnet test` |
| Node / Jest | `npm test` or `npx jest` |
| Node / Vitest | `npx vitest run` |
| Java / Maven | `mvn test -q` |
| Java / Gradle | `./gradlew test` |
| Python / pytest | `pytest` |
| Go | `go test ./...` |
| Rust | `cargo test` |

If the test command is not clear, ask the user.

**On failure:** diagnose the root cause, fix the code or the test (whichever is wrong), then re-run. Repeat until all tests pass. Do not proceed with failing tests.

Once all tests pass, report:
- Total tests run
- New tests added
- Any tests that were already failing before your changes (flag but do not fix unless related to the ticket)

---

## Step 5 — Local testing

Present the **Testing Strategy** section from `PLAN.md` to the user as a checklist. For each manual step listed, provide the exact command or action the user should take.

If the strategy references starting a local server or application, provide the command:

```bash
# examples — use the project's actual command
npm run dev
dotnet run
./gradlew bootRun
python manage.py runserver
```

Then tell the user:
> "The build is clean and all tests pass. Please run through the local testing checklist above and confirm when you're done — or let me know if you find any issues."

**Wait for the user's response before proceeding.**

- If they report issues: diagnose and fix, then return to Step 4 (re-run tests) before asking again.
- If they confirm everything looks good: proceed to Step 6.

---

## Step 6 — Commit

Stage all changes and create a structured commit:

```bash
git add -A
git commit -m "<type>(<scope>): <short summary>

- <bullet summarizing each logical change group>
- <bullet for new tests added>

Implements: {TICKET_ID}
Plan: see PLAN.md"
```

Derive `<type>` from the branch prefix (`feature` → `feat`, `fix` → `fix`, `chore` → `chore`, `hotfix` → `fix`).
Derive `<scope>` from the primary area of change (e.g., `auth`, `api`, `ui`).
Keep the subject line under 72 characters.

If the project has a commit convention (Conventional Commits, Angular, etc.), follow it instead.

---

## Step 7 — Push the branch

```bash
git push -u origin {BRANCH_NAME}
```

If the push is rejected due to a non-fast-forward, do not force push. Stop and tell the user:
> "The remote branch has commits that aren't in your local branch. Please resolve this manually before I push."

---

## Step 8 — Create the pull request

Use `gh` CLI to create the PR:

```bash
gh pr create \
  --title "<type>(<scope>): <same subject as commit>" \
  --base {BASE_BRANCH} \
  --body "$(cat <<'EOF'
## Summary

<2–4 sentence description of what was implemented and why>

## Changes

<bullet list of every logical change group from the Implementation Changes table>

## Testing

### Automated
- Unit tests added: <count>
- All tests passing: ✓

### Manual
<reproduce the Testing Strategy checklist from PLAN.md>

## Linked ticket

{TICKET_ID} — {TICKET_TITLE}

## Plan

See `PLAN.md` on this branch for the full implementation plan including scope, risks, and rollback.
EOF
)"
```

If `gh` is not authenticated, tell the user: `gh auth login` and wait.

---

## Step 9 — Move the ticket to In Review

Using the active ticket manager config (`{TM_IN_REVIEW}`):

### Jira

```bash
acli jira --action transitionIssue --issue {TICKET_ID} --transition "{TM_IN_REVIEW}"
```

If the transition name fails, list available transitions and pick the one representing "awaiting review":

```bash
acli jira --action getTransitions --issue {TICKET_ID}
```

### Azure DevOps

Call MCP `update_work_item` with `id: {TICKET_ID}` and `state: "{TM_IN_REVIEW}"`.

Optionally add a comment linking the PR:

**Jira:** `acli jira --action addComment --issue {TICKET_ID} --comment "PR opened: <PR URL>"`
**Azure DevOps:** MCP `add_work_item_comment` with `text: "PR opened: <PR URL>"`

---

## Step 10 — Report to the user

Print a final summary:

- Branch: `{BRANCH_NAME}`
- Commit: `<commit hash>`
- Tests: `<N>` passing (`<M>` new)
- PR: `<PR URL>`
- Ticket: `{TICKET_ID}` moved to In Review

Then ask:
> "The PR is open and the ticket is in review. Would you like me to request reviewers, add labels, or do anything else before we wrap up?"

---

## Notes

- Always fix compilation errors before moving to the next concern — never leave the build broken between steps.
- Never force-push (`--force` / `--force-with-lease`) without explicit user confirmation.
- If the project has a linter or formatter, run it before committing: `eslint --fix`, `dotnet format`, `black .`, `gofmt -w .`, etc.
- Do not modify files outside the scope defined in `PLAN.md` without telling the user first.
- If `PLAN.md` has open questions marked as unresolved, surface them before starting implementation and ask the user to resolve them.

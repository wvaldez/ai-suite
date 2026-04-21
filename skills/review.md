---
name: review
description: Pre-landing code review focused on correctness, security, and simplicity
---

Review the staged or specified changes before they land.

Check for:
- Logic errors or edge cases not handled
- Security issues (injection, exposed secrets, unvalidated input)
- Unnecessary complexity — flag anything that could be simpler
- Missing or misleading names (variables, functions, files)
- Anything that will obviously break in production

For each issue found, state: the file and line, what the problem is, and what to do instead.
Do not comment on style or formatting unless it causes ambiguity.
At the end, give a one-line verdict: ready to land, needs minor fixes, or needs major rework.
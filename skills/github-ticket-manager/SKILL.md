---
name: github-ticket-manager
description: Use when creating, editing, deleting, assigning, parenting, or changing fields on GitHub issues that belong to a GitHub Project board.
---

# GitHub Ticket Manager

## Overview

Manage project tickets through the GitHub connector when supported and direct `gh` commands otherwise. Local status and GitHub metadata may be read for validation, but perform no mutation until every required operation input is explicit and valid.

## Required Context

Resolve exactly one permitted project report under `~/src/agent-data/product-agents`. Read its GitHub Project number and exact project-name label. Stop when the project is missing, inactive without explicit instruction, or ambiguous.

Creation requires:

- Project
- Assignee, including explicit `Unassigned`
- Repository as `owner/repository`, verified as a GitHub repository
- Issue type: `Bug`, `Feature`, or `Task`
- Size: `XS`, `S`, `M`, `L`, or `XL`
- Issue title
- Issue description
- Priority: `Urgent`, `High`, `Medium`, or `Low`
- Estimate as a number of days
- Target date as `YYYY-MM-DD` or explicit `None`
- Parent issue, optionally

Use Codex structured prompting for missing values. Require this description:

```markdown
## Context
<required>

## What needs to be done
<required>

## Stakeholders
<optional; omit when absent>

## Dependencies
<optional; omit when absent>

## Acceptance Criteria
- <required criterion>
```

Update requires an issue URL or `owner/repository#number` and explicit changes. Delete requires the same identifier plus confirmation naming the issue.

## Execution

Use the GitHub connector first. For unsupported operations, inspect `gh issue --help`, `gh project --help`, or `gh api graphql --help`. Allow interactive authorization for required Project scopes.

### Create

- Create the repository issue with the exact project-name label.
- Add it to the resolved GitHub Project.
- Resolve actual field and option IDs with `gh project field-list`; never invent them.
- Set issue type, size, priority, estimate, target date, and `Backlog` status.
- Clear target date when its explicit value is `None`.
- When a parent is supplied, resolve both issue node IDs and use GitHub's sub-issue GraphQL mutation; a plain link is not a parent relationship.

### Update or Delete

Apply only requested changes. Use `gh issue edit` for repository properties, `gh project item-edit` for Project fields, and `gh issue delete` only after explicit confirmation. To add a parent to an existing issue, use the sub-issue GraphQL mutation. Report unsupported parent removal or replacement instead of approximating it. Preserve the project-name label.

## Validation and Failure Handling

Re-read the issue and Project item after every non-delete mutation. After deletion, verify that the issue no longer exists.

If a later mutation fails after a successful change, stop and return `Outcome: Partial`, including the issue URL, completed steps, failed step, and remediation. Report success only after verifying the issue, board membership, requested fields, Backlog status for new issues, exact label, and requested parent relationship.

## Output Format

Outcome: <Succeeded | Partial | Blocked | Failed>
Issue: <URL or identifier>
Repository: <owner/repository>
Assignee: <value>
Type: <value or unchanged>
Size: <value or unchanged>
Priority: <value or unchanged>
Estimate: <days or unchanged>
Target date: <date, None, or unchanged>
Status: <value or unchanged>
Parent: <result or None>
Completed steps: <items or None>
Failed step: <item or None>
Verification: <Passed | Partial | Failed; checks>
Missing input or capability: <items or None>

## Success Criteria

- Required operation inputs were explicit before mutation.
- The issue belongs to the correct board and carries the exact project-name label.
- Requested fields and parent relationship were re-read and verified.

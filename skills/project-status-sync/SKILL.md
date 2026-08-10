---
name: project-status-sync
description: Use when refreshing, creating, or answering questions from one individual project's Markdown or HTML status report using its GitHub Project board and exact project-name label.
---

# Project Status Sync

## Overview

Refresh or answer questions about one project using its domain board and exact project-name label.

## Required Context

Require an intent (`refresh` or `answer`) and project name. Search status files under `~/src/agent-data/product-agents/active` and `inactive` and resolve exactly one project report. Prompt and stop when missing or ambiguous. Inactive projects require explicit instruction.

Read the project report, sibling domain report, board number, exact label, members, and `last_updated`. Interpret dates in the user's local timezone and preserve the existing date format.

`answer` is read-only unless the user explicitly requests a refresh. State the source `last_updated` date.

## Data Collection

For a standalone refresh, use the GitHub connector when it returns complete Project data. Otherwise use one `gh project item-list <project-number> --owner <owner> --format json` request and retain only issues with the exact project label.

For a delegated refresh, use the supplied subset and make zero GitHub queries. It must include `board_id`, `snapshot_at`, `project_label`, and each issue's URL, title, labels, Project status, priority, target date, assignees, author, and updated date. Include completion date, blockers, dependencies, and description when available. Reject the subset when its board ID differs or any issue lacks the exact project label.

Identify the authenticated user. Assigned issues represent personal work; authored issues represent team work. Keep overlap once. Use the full result for an initial report. Otherwise emphasize changes since `last_updated` while retaining active context and completed history.

## Refresh

- Order active work and backlog by `Urgent`, `High`, `Medium`, `Low`, then target date.
- Move issues according to current GitHub Project status.
- Preserve completed work and completion dates.
- Focus on non-`Done` work while detecting newly completed items.
- Surface blockers, dependencies, target dates, experiments, and A/B tests.

Preserve TOML front matter and the Table of Contents, Project Members, Executive Summary, Active Work, Backlog, Completed Work, Blockers and Dependencies, and Relevant Links and Resources sections.

Limit the Executive Summary to five linked issues. Each entry includes priority, GitHub link, concise leadership context, target date when present, and relevant blockers or dependencies. Prune stale summary details, not completed history.

Update `last_updated` using the existing date format. When Markdown changes, refresh HTML with `$write-html-report` when available. Publish only when requested with `$publish-html-report`.

## Validation

Re-read changed reports. Confirm required sections, priority ordering, links, and completed history. Do not modify sibling project reports.

## Output Format

Project: <name>
Intent: <refresh | answer>
Outcome: <Succeeded | Partial | Blocked | Failed>
Snapshot at: <value or None>
Issue count: <number or Unknown>
Changed: <items or None>
Health: <On track | At risk | Blocked | Unknown>
Timeline: <dates and confidence>
Risks: <items or None>
Blockers: <items or None>
Next actions: <items or None>
Markdown path: <path>
HTML path: <path or None>
Verification: <Passed | Partial | Failed; checks>
Assumptions: <items or None>

## Success Criteria

- Exactly one permitted project was resolved.
- A standalone refresh used one label-filtered query; a delegated refresh used one verified subset and made zero GitHub queries.
- Required sections, ordering, links, and completed history were verified.

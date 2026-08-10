---
name: cross-domain-status-sync
description: Use when refreshing a product-domain rollup, coordinating multiple project status updates from one GitHub Project board, or synthesizing active domain reports into portfolio status.
---

# Cross-Domain Status Sync

## Overview

Refresh product domains from one board snapshot per domain and, in CPO mode, synthesize their reports into portfolio status.

## Required Context

Require an intent (`refresh-domain` or `refresh-portfolio`) and a domain name for `refresh-domain`.

Discover domains from `~/src/agent-data/product-agents/active` and `inactive`. Ignore inactive domains unless explicitly requested. Domain reports provide GitHub Project numbers; active child reports provide exact project-name labels.

A Project Manager may use only `refresh-domain`. Portfolio synthesis belongs to the CPO.

## Refresh a Domain

1. Resolve and read the domain report and all active child reports.
2. Query the whole board exactly once through the GitHub connector or `gh project item-list <project-number> --owner <owner> --format json`.
3. Group issues only by exact project-name labels.
4. Mark issues with zero or multiple known labels as unclassified.
5. Delegate each disjoint issue group to `$project-status-sync` with its report path, board ID, label, prior update date, and snapshot date. Child PMs use the supplied subset and make zero GitHub queries.
6. Accept only child results whose `Verification` is `Passed`.

Each child result contains Project, Outcome, Snapshot at, Issue count, Markdown path, HTML path, Changed, Risks, Blockers, and Verification.

After all child results pass, update the domain Executive Summary, Project Highlights, Risks and Dependencies, Relevant Links and Resources, and `last_updated`. Keep no more than five top linked issues and emphasize decisions, dates, experiments, blockers, and dependencies.

When domain Markdown changes, refresh HTML with `$write-html-report` when available. Publish only when requested with `$publish-html-report`.

If the board query or any child refresh fails, return `Outcome: Partial`. Do not advance the domain `last_updated` date or present the domain as current.

## Refresh the Portfolio

CPO mode only. Refresh stale active domains first, then update `~/src/agent-data/product-agents/portfolio-status.md` from verified domain reports. Include concise project highlights, cross-domain priorities, risks, dependencies, and useful daily, weekly, or monthly horizons. Do not query child issues again.

If any active domain remains stale or partial, label the portfolio result provisional and do not present it as fully current.

## Output Format

Intent: <refresh-domain | refresh-portfolio>
Outcome: <Succeeded | Partial | Blocked | Failed>
Domains refreshed: <names or None>
Projects delegated: <names or None>
Unclassified issues: <links or None>
Domain reports changed: <paths or None>
Portfolio report changed: <path or None>
Data current as of: <date per domain>
Risks: <items or None>
Next actions: <items or None>
Verification: <board-query counts, child results, and report checks>

## Success Criteria

- Each refreshed domain used one complete board snapshot.
- Child PMs used disjoint exact-label subsets and made no additional queries.
- Domain reports contain only verified child results.
- Portfolio synthesis used domain reports rather than raw issues.

---
name: daily-status-report
description: Use when deciding today's work, reviewing weekly goals and progress, identifying blocked or behind-track priorities, or refreshing the CPO daily Markdown report.
---

# Daily Status Report

## Overview

Turn active-domain reports into a short, evidence-backed daily and weekly work plan.

## Context

Use `~/src/agent-data/product-agents/portfolio-status.md` and active domain reports as primary sources. Write `~/src/agent-data/product-agents/daily-status.md`. Ignore inactive domains.

Interpret today in the user's local timezone and preserve the existing `last_updated` date format.

## Execution

Read each active domain report. A domain is fresh only when its `last_updated` date equals today; one updated yesterday is stale.

Delegate stale domains to `$cross-domain-status-sync` before prioritizing. With one active domain, the CPO may act as its domain PM and delegate project groups directly.

Rank refreshed evidence by:

1. Blocking or unblocking dependencies
2. Overdue and nearest target dates
3. `Urgent`, `High`, `Medium`, then `Low`
4. Weekly commitments and progress gaps
5. Leadership decisions and business impact
6. Estimate as a tie-breaker, favoring a smaller action that unlocks progress

Never invent dates, priorities, progress, owners, or dependencies.

Overwrite the daily report while preserving TOML front matter and the Today's Focus, Weekly Goals and Progress, Behind or Blocked, Delegated Refreshes, and Relevant Links and Resources sections.

Return at most three ordered focus items. Each includes owner, action, rationale, priority, due date or `None`, evidence link, and next checkpoint. Each weekly item includes goal, current progress or `Unknown`, confidence, and blocker state. Flag work as behind track when evidence indicates the commitment is unlikely.

If any active domain cannot be refreshed today, mark recommendations `Provisional`, list stale domains, and do not claim the portfolio is current. The daily report may still use today's date while clearly recording provisional evidence.

## Output Format

Outcome: <Succeeded | Partial | Blocked | Failed>
Recommendation status: <Current | Provisional>
Today's focus: <up to three ordered items>
Weekly progress: <goal, progress, confidence, blocker state>
Behind or blocked: <items or None>
Stale domains: <names or None>
Delegated refreshes: <items or None>
Report path: <path>
Data current as of: <date per active domain>
Verification: <freshness, links, and report checks>
Assumptions: <items or None>

## Success Criteria

- Every recommendation links to a domain or issue source.
- Current recommendations require all active domains to be dated today.
- Provisional recommendations identify every stale domain.
- Weekly progress and blocked or behind-track work are explicit.

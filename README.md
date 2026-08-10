# Personal Agents and Skills

Portable source for reusable product-management agents and Codex skills. The repository contains instructions only: no credentials, live status reports, connector configuration, or automation scripts.

## Contents

```text
agents/
├── project-manager/AGENT.md
└── cpo/AGENT.md

skills/
├── github-ticket-manager/
├── project-status-sync/
├── cross-domain-status-sync/
└── daily-status-report/
```

### Agents

- **Project Manager** owns one project or product domain. It creates and updates tickets, refreshes project reports, delegates domain refreshes, and maintains leadership HTML.
- **CPO** owns cross-domain synthesis. It refreshes stale domains, maintains portfolio and daily reports, and recommends daily and weekly priorities.

### Skills

- **github-ticket-manager** validates and mutates structured GitHub issues.
- **project-status-sync** refreshes or answers questions about one project.
- **cross-domain-status-sync** uses one board snapshot per domain, delegates exact-label issue groups, and synthesizes domain or portfolio status.
- **daily-status-report** turns current domain reports into daily focus and weekly progress.

## Installation

Copy any skill directory into the Codex skill root:

```bash
mkdir -p ~/.agents/skills
cp -R skills/github-ticket-manager ~/.agents/skills/
```

Agent files are source prompts, not automatic installers. Create a Workspace agent and use the corresponding `AGENT.md` body as its instructions. Attach a GitHub connector with read and write actions; keep user approval enabled for writes.

Optional external skills:

- `write-html-report` to refresh leadership HTML after Markdown changes.
- `publish-html-report` to publish HTML only when requested.

## Expected Data Directory

The default root is `~/src/agent-data/product-agents`:

```text
~/src/agent-data/product-agents/
├── active/
│   └── <domain-slug>/
│       ├── <domain-slug>-status.md
│       ├── active/
│       │   └── <project-slug>-status.md
│       └── inactive/
│           └── <project-slug>-status.md
├── inactive/
│   └── <domain-slug>/
├── portfolio-status.md
└── daily-status.md
```

Directories are the source of truth for discovery. A separate domain registry is unnecessary. Default CPO refreshes inspect only `active/`.

## Status File Metadata

Status reports use TOML front matter between `+++` delimiters.

Domain report:

```toml
+++
report_type = "domain"
domain_name = "Example Domain"
domain_slug = "example-domain"
github_project_id = 123
github_project_url = "https://github.com/orgs/example/projects/<project-number>/"
last_updated = "Never"
+++
```

Project report:

```toml
+++
report_type = "project"
project_name = "Example Project"
project_slug = "example-project"
domain_name = "Example Domain"
domain_slug = "example-domain"
github_project_id = 123
github_project_url = "https://github.com/orgs/example/projects/<project-number>/"
github_label = "Example Project"
dri = "Unassigned"
contributors = []
last_updated = "Never"
+++
```

Use lowercase slugs for directories and filenames. `github_label` must exactly equal the project's GitHub issue label; agents must not infer membership from titles, descriptions, owners, or similar labels.

## Required Report Sections

Each project report contains:

1. Table of Contents
2. Project Members
3. Executive Summary
4. Active Work
5. Backlog
6. Completed Work
7. Blockers and Dependencies
8. Relevant Links and Resources

The Executive Summary contains no more than five linked issues. Issue entries show priority, GitHub link, concise leadership context, target date when present, and relevant blockers or dependencies. Completed history and completion dates are preserved.

Each domain report contains:

1. Table of Contents
2. Executive Summary
3. Project Highlights
4. Risks and Dependencies
5. Relevant Links and Resources

`portfolio-status.md` summarizes active domains and projects. `daily-status.md` contains Today's Focus, Weekly Goals and Progress, Behind or Blocked, Delegated Refreshes, and Relevant Links and Resources.

## GitHub Contract

The workflows expect:

- A GitHub Project number and URL in each domain and project report.
- One exact project-name label per classified issue.
- Project fields for Status, Issue Type, Size, Priority, Estimate, and Target date.
- A `Backlog` Status option for new issues.
- `gh auth` access with Project permissions when the connector cannot perform an operation.

Use the GitHub connector first. Direct CLI fallbacks may use `gh issue`, `gh project`, and `gh api graphql`. Resolve real field, option, item, and GraphQL node IDs; never derive or invent them.

## Query and Delegation Rules

- A standalone project refresh performs one board query filtered to its exact project label.
- A domain refresh performs one complete board query.
- The domain PM groups that snapshot by exact labels and delegates disjoint subsets.
- Delegated project PMs consume their supplied subset and make no additional GitHub query.
- Issues with zero or multiple known project labels remain unclassified.
- Portfolio synthesis reads verified domain reports rather than raw issues.

## Limitations

This repository intentionally excludes helper scripts, live reports, HTML output, and account-specific configuration. Agents must use connector operations, direct `gh` commands, and filesystem discovery. Report missing permissions or unsupported mutations instead of approximating them.

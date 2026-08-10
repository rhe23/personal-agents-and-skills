# Personal Agents and Skills Repository

## Goal

Create a public, portable source repository for reusable personal Codex agents and skills without changing the installed originals.

## Repository

- Local path: `~/src/personal-agents-and-skills`
- GitHub: `rhe23/personal-agents-and-skills`
- Visibility: public

## Contents

```text
personal-agents-and-skills/
├── README.md
├── agents/
│   ├── project-manager/AGENT.md
│   └── cpo/AGENT.md
└── skills/
    ├── github-ticket-manager/
    ├── project-status-sync/
    ├── cross-domain-status-sync/
    └── daily-status-report/
```

Each skill retains its `SKILL.md` and `agents/openai.yaml`. Each agent is exported as a readable `AGENT.md` containing its role and complete instructions.

## Portability

Repo copies use `~/src/agent-data/product-agents` as the documented data root. They contain no company-specific organization names, project names, board IDs, people, or absolute user paths.

The repository does not include automation scripts. Skills use the GitHub connector first and direct `gh` commands when the connector lacks an operation.

## Data Contract

The README documents:

- Active and inactive domain and project directories.
- Domain and project status-file naming and TOML front matter.
- Required Markdown report sections.
- Exact project-name labels used to associate issues with projects.
- Portfolio and daily report locations.
- GitHub Project field expectations and authentication.
- Agent and skill responsibilities, installation, and limitations.

## Safety

- Installed Codex agents and skills remain unchanged.
- No credentials, connector identifiers, private status reports, or generated leadership reports are copied.
- Public files contain examples and placeholders only.

## Verification

- Validate each copied skill with the Codex skill validator.
- Scan the repository for company names, private paths, board IDs, and credentials.
- Confirm the README links and documented file tree.
- Verify the Git remote owner is `rhe23` before pushing.

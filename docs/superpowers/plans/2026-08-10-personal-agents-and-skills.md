# Personal Agents and Skills Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Publish sanitized, portable copies of the Project Manager and CPO agents and their four supporting skills in a public `rhe23/personal-agents-and-skills` repository.

**Architecture:** Store agents as readable Markdown instruction files and skills in standard Codex skill directories. Use a single README as the operating contract for the external data tree and GitHub requirements; include no helper scripts, live reports, or credentials.

**Tech Stack:** Markdown, YAML, Git, GitHub CLI

## Global Constraints

- Modify only repository copies; do not change installed Codex agents or skills.
- Remove company names, employee names, organization IDs, board IDs, connector IDs, credentials, and absolute `/Users/...` paths.
- Use `~/src/agent-data/product-agents` as the portable default data root.
- Use the GitHub connector first and direct `gh` fallback.
- Keep the repository public and owned by GitHub account `rhe23`.
- Do not include automation scripts or live status reports.

---

### Task 1: Export Portable Agents and Skills

**Files:**
- Create: `README.md`
- Create: `agents/project-manager/AGENT.md`
- Create: `agents/cpo/AGENT.md`
- Create: `skills/github-ticket-manager/SKILL.md`
- Create: `skills/github-ticket-manager/agents/openai.yaml`
- Create: `skills/project-status-sync/SKILL.md`
- Create: `skills/project-status-sync/agents/openai.yaml`
- Create: `skills/cross-domain-status-sync/SKILL.md`
- Create: `skills/cross-domain-status-sync/agents/openai.yaml`
- Create: `skills/daily-status-report/SKILL.md`
- Create: `skills/daily-status-report/agents/openai.yaml`

**Interfaces:**
- Consumes: the currently published Project Manager and CPO prompts and installed copies of the four skills.
- Produces: portable Markdown/YAML source files with no dependency on private helper scripts.

- [ ] **Step 1: Export the two agent prompts**

Write each published prompt to its `AGENT.md`. Replace private absolute paths with `~/src/agent-data/product-agents`. Replace organization-specific examples with generic examples such as `Example Domain`, `Example Project`, `owner/repository`, and GitHub Project `<project-number>`.

- [ ] **Step 2: Export the four skill directories**

Copy each `SKILL.md` and `agents/openai.yaml`, then remove calls to private `project_status.py`, `portfolio_status.py`, and `github_project.py`. Instruct agents to discover reports with filesystem search, use the GitHub connector first, and use direct `gh issue`, `gh project`, or `gh api graphql` commands for unsupported operations.

- [ ] **Step 3: Write the repository README**

Document:

1. The purpose and boundaries of each agent and skill.
2. The installation targets `~/.agents/skills/<skill-name>`.
3. The expected data tree:

```text
~/src/agent-data/product-agents/
├── active/<domain-slug>/
│   ├── <domain-slug>-status.md
│   ├── active/<project-slug>-status.md
│   └── inactive/<project-slug>-status.md
├── inactive/<domain-slug>/
├── portfolio-status.md
└── daily-status.md
```

4. Domain front matter: `report_type`, `domain_name`, `domain_slug`, `github_project_id`, `github_project_url`, and `last_updated`.
5. Project front matter: domain fields plus `project_name`, `project_slug`, exact `github_label`, `dri`, `contributors`, and `last_updated`.
6. Required project and domain report sections.
7. GitHub authentication and required Project fields.
8. The one-board-query and exact-label delegation rules.
9. The limitation that scripts and live reports are not included.

- [ ] **Step 4: Validate the skill structure**

Run:

```bash
for skill in github-ticket-manager project-status-sync cross-domain-status-sync daily-status-report; do
  uv run --with pyyaml python ~/.codex/skills/.system/skill-creator/scripts/quick_validate.py "skills/$skill"
done
```

Expected: four `Skill is valid!` results.

- [ ] **Step 5: Scan public content**

Run:

```bash
rg -n 'company-name|company-user|/Users/|connector_[a-z0-9]+|projects/[0-9]+|gho_|github_pat_' README.md agents skills
```

Expected: no matches.

- [ ] **Step 6: Commit the export**

```bash
git add README.md agents skills docs/superpowers/plans/2026-08-10-personal-agents-and-skills.md
git commit -m $'feat(repo): add portable agents and skills\n\n#agentic'
```

### Task 2: Create and Publish the Public Repository

**Files:**
- Verify: repository root and Git metadata

**Interfaces:**
- Consumes: the validated local `main` branch.
- Produces: public GitHub repository `rhe23/personal-agents-and-skills` with `origin` configured.

- [ ] **Step 1: Verify repository state**

```bash
git status --short
git log --oneline --decorate -3
git remote -v
```

Expected: clean working tree and no existing `origin`.

- [ ] **Step 2: Select the personal GitHub account**

```bash
PREVIOUS_GH_USER="$(gh api user --jq .login)"
gh auth switch --hostname github.com --user rhe23
gh auth status --hostname github.com
```

Expected: `rhe23` is active.

- [ ] **Step 3: Create and push the public repository**

```bash
gh repo create rhe23/personal-agents-and-skills --public --source=. --remote=origin --push
```

Expected: the repository is public, `origin` points to `rhe23/personal-agents-and-skills`, and `main` is pushed.

- [ ] **Step 4: Verify remote ownership and visibility**

```bash
gh repo view rhe23/personal-agents-and-skills --json nameWithOwner,visibility,url,defaultBranchRef
git remote -v
git status -sb
```

Expected: owner `rhe23`, visibility `PUBLIC`, default branch `main`, and local `main` tracks `origin/main`.

- [ ] **Step 5: Restore the previous active GitHub account**

```bash
gh auth switch --hostname github.com --user "$PREVIOUS_GH_USER"
```

Expected: the user's prior CLI account context is restored without changing the new repository remote. Use the account reported as active before Step 2; do not record it in repository files.

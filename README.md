# Org Weekly Digest

A GitHub Agentic Workflow that generates a consolidated weekly digest report across all repositories under **TeplrGuy**.

## What It Does

Every week (or on-demand), an AI agent:

1. Discovers all repos under the account
2. Queries each repo for issues, PRs, commits, and releases from the past 7 days
3. Aggregates the data into a single consolidated report
4. Posts it as a GitHub Issue in **this** repository (auto-closing the previous week's digest)

## Architecture

```
This Repo (org-weekly-digest)
  └── .github/workflows/weekly-digest.md    ← Agentic workflow definition
  └── .github/workflows/weekly-digest.lock.yml ← Compiled by `gh aw compile`

          ┌──> TeplrGuy/repo-1 (read issues, PRs, commits)
  Agent ──┼──> TeplrGuy/repo-2 (read issues, PRs, commits)
          ├──> TeplrGuy/repo-3 (read issues, PRs, commits)
          └──> ... all repos
                    │
                    ▼
          Creates a digest issue in THIS repo
```

## Prerequisites

- [GitHub CLI](https://cli.github.com/) v2.0.0+
- [gh-aw extension](https://github.github.com/gh-aw/setup/quick-start/)
- A GitHub Copilot subscription (or Anthropic/OpenAI API key)

## Setup

### 1. Install gh-aw

```bash
gh extension install github/gh-aw
```

### 2. Clone this repo

```bash
git clone https://github.com/TeplrGuy/org-weekly-digest.git
cd org-weekly-digest
```

### 3. Set required secrets

**COPILOT_GITHUB_TOKEN** (for the AI engine):
```bash
gh aw secrets set COPILOT_GITHUB_TOKEN --value "<your-copilot-token>"
```

**GH_AW_CROSS_REPO_PAT** (for cross-repo API access):

Create a [fine-grained PAT](https://github.com/settings/personal-access-tokens/new) with:
- **Repository access**: All repositories (or select specific ones)
- **Permissions**: Contents (Read), Issues (Read), Pull Requests (Read), Actions (Read)

```bash
gh aw secrets set GH_AW_CROSS_REPO_PAT --value "<your-fine-grained-pat>"
```

### 4. Compile and push

```bash
gh aw compile
git add .
git commit -m "Add multi-repo weekly digest agentic workflow"
git push
```

### 5. Trigger a test run

```bash
gh aw run weekly-digest
```

## Customization

Edit `.github/workflows/weekly-digest.md` to:
- Change which repos are queried (modify `allowed-repos` in frontmatter)
- Adjust the report format or sections
- Add org-level filtering
- Change the schedule (daily, weekly, etc.)

Then recompile:
```bash
gh aw compile
git add . && git commit -m "Update workflow" && git push
```

## Troubleshooting

- **Rate limits**: The workflow is designed to handle many repos efficiently. If you have 100+ repos, consider filtering to active-only.
- **Permission errors**: Ensure the `GH_AW_CROSS_REPO_PAT` has read access to all target repos.
- **No output**: Check the Actions tab for workflow run logs.

## References

- [GitHub Agentic Workflows Docs](https://github.github.com/gh-aw/)
- [Multi-Repository Examples](https://github.github.com/gh-aw/examples/multi-repo/)
- [MultiRepoOps Pattern](https://github.github.com/gh-aw/patterns/multi-repo-ops/)
- [Cross-Repository Operations](https://github.github.com/gh-aw/reference/cross-repository/)

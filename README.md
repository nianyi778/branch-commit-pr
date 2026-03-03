# branch-commit-pr

A complete git development workflow skill for AI coding agents. Handles the entire cycle: **detect default branch → create feature branch → atomic commits → pull request**.

## 10-Second Install

```bash
npx skills add nianyi778/branch-commit-pr
```

## 30-Second Quick Start

After installing, say one of these:

- `"Create a branch for login feature"`
- `"Commit my changes as atomic commits"`
- `"Open a PR for #42"`

## Features

- **Smart Detection** — Auto-detects default branch, branch naming conventions, and commit style from your repo history
- **Branch Management** — Creates feature branches from up-to-date default branch with consistent naming
- **Atomic Commits** — Splits changes into logical, reviewable commits following your repo's conventions
- **Pull Requests** — Creates PRs via `gh` CLI with auto-generated title, summary, and commit list
- **GitHub Issues** — Links issues with `Closes #N` when you mention them
- **Jira Integration** — Optional Jira ticket linking when environment variables are configured

## Install

### Via [skills.sh](https://skills.sh) CLI (recommended)

```bash
# Install for all detected agents
npx skills add nianyi778/branch-commit-pr

# Install for specific agent
npx skills add nianyi778/branch-commit-pr -a claude-code
npx skills add nianyi778/branch-commit-pr -a opencode
npx skills add nianyi778/branch-commit-pr -a cursor
```

### Manual Installation

Copy the skill to your agent's skills directory:

```bash
# Claude Code (project-level)
mkdir -p .claude/skills/branch-commit-pr
cp skills/branch-commit-pr/SKILL.md .claude/skills/branch-commit-pr/

# Claude Code (global)
mkdir -p ~/.claude/skills/branch-commit-pr
cp skills/branch-commit-pr/SKILL.md ~/.claude/skills/branch-commit-pr/

# OpenCode (project-level)
mkdir -p .opencode/skills/branch-commit-pr
cp skills/branch-commit-pr/SKILL.md .opencode/skills/branch-commit-pr/

# OpenCode (global)
mkdir -p ~/.config/opencode/skills/branch-commit-pr
cp skills/branch-commit-pr/SKILL.md ~/.config/opencode/skills/branch-commit-pr/
```

## Usage

Just describe what you want in natural language:

| What you say | What happens |
|---|---|
| "Start working on adding dark mode" | Creates branch → (you work) → commits → PR |
| "Create a branch for the login feature" | Detects conventions, creates & pushes branch |
| "Commit my changes" | Analyzes changes, creates atomic commits |
| "Open a PR" | Generates title/body from commits, creates PR via `gh` |
| "Commit and create PR for #42" | Commits, creates PR, links GitHub Issue #42 |

## Marketplace Visibility (skills.sh)

This repository is already in the format that `skills.sh` CLI can install directly.

- Install command users should run:
  - `npx skills add nianyi778/branch-commit-pr`
- `skills.sh` visibility/leaderboard is driven by real CLI installs (not a manual submit form).
- Keep this repo public and keep the install command in your README to maximize discovery.

## Jira Integration (Optional)

To enable Jira ticket linking, set these environment variables:

```bash
export JIRA_URL="https://yourteam.atlassian.net"
export JIRA_EMAIL="you@company.com"
export JIRA_API_TOKEN="your-api-token"
```

Then mention the ticket: *"Create a PR for PROJ-123"*

The skill will add a link to the Jira ticket in the PR body. Jira status transitions are only performed when you explicitly ask.

## How It Works

### Phase 0: Context Gathering (automatic)
Detects your repo's default branch, branch naming style, commit message conventions, and available issue trackers.

### Phase 1: Branch Management
Fetches latest default branch, creates a properly-named feature branch, and sets upstream tracking.

### Phase 2: Smart Commit
Analyzes changed files, plans atomic commits by directory/concern, generates messages matching your repo's style.

### Phase 3: Pull Request
Creates a PR via `gh` CLI with a generated title, summary bullets, commit list, and issue links.

## Requirements

- `git` — Any modern version
- [`gh`](https://cli.github.com/) — GitHub CLI (for PR creation). Run `gh auth login` to authenticate.
- Jira integration requires `curl` and the environment variables above.

## Copy-Paste Examples

```text
Start working on dark mode for settings page
Create a branch for PROJ-123 login timeout fix
Commit all current changes with atomic commits
Create a PR for PROJ-123 and move it to In Review
```

## Supported Agents

Works with any agent that supports the [Agent Skills](https://agentskills.io) standard:

Claude Code, OpenCode, Cursor, Codex, Cline, GitHub Copilot, Windsurf, Roo Code, Amp, Gemini CLI, and [40+ more](https://skills.sh).

## License

MIT

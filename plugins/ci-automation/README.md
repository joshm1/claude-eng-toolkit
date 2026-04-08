# ci-automation

**The problem:** Engineers babysit PRs through CI, manually fix failures, wait for reviews, resolve conflicts, and click merge. It's 30 minutes of human attention on a process that should be autonomous. Meanwhile, stale branches pile up because nobody triages them.

**How it works:** Three skills that automate the CI/PR/release lifecycle:

- **babysit-pr** watches a PR through CI checks, review, and conflicts. When CI fails, it reads the failure, fixes the code, pushes, and loops. When reviews come in, it addresses feedback. When conflicts appear, it resolves them. It merges when everything is green.
- **branch-triage** analyzes all git branches and produces an interactive HTML dashboard showing stale branches, merge status, and recommended actions. Turns "what branches do we have?" from a 20-minute investigation into a 10-second glance.
- **github-release** creates GitHub releases with semantic versioning inferred from conventional commits. Analyzes commit history, detects feat/fix/breaking changes, calculates version bumps, and generates grouped release notes.

**Why it exists:** CI/CD should be fully autonomous for routine cases. Engineers should intervene only when something genuinely novel breaks. These skills make the boring parts of shipping (CI babysitting, branch cleanup, release tagging) disappear so you can focus on the interesting parts.

## Components

- **Skill: `babysit-pr`** — Autonomous PR monitoring: CI fix loops, review response, conflict resolution, auto-merge
- **Skill: `branch-triage`** — Interactive HTML dashboard for branch analysis and cleanup
- **Skill: `github-release`** — Semantic version inference from conventional commits with grouped release notes

## Installation

```bash
/plugin install ci-automation@joshm1-claude-eng-toolkit
```

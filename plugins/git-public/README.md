# git-public

**The problem:** Secrets leak in public repos because scanning happens at the wrong point in the workflow. Most teams catch exposed credentials in CI or code review — after the secret is already in the git history. By then the damage is done: the secret is in a public commit, and rotating it is a scramble.

**How it works:** This plugin provides three commands that shift security left to the moment of commit:

- **`/safe-commit`** scans staged files for API keys, tokens, passwords, private keys, AWS credentials, database URLs, GitHub tokens, OAuth secrets, and more. It distinguishes real secrets from safe references (documentation, config schemas, placeholder values). If it finds something dangerous, it unstages the file and tells you how to fix it.
- **`/commit`** provides an interactive staging workflow with semantic commit messages. It stages tracked files explicitly (never `git add .`), flags untracked source files as potential incomplete work, and enforces conventional commit format (`feat`, `fix`, `refactor`, etc.).
- **`/review-safe-commit-history`** audits existing commit history for safe/unsafe patterns, catching anything that slipped through before the plugin was installed.

**Why it exists:** The right time to catch a secret is before it enters git history, not after. This plugin makes "scan before commit" the default behavior. It's the difference between a policy that says "don't commit secrets" and a tool that actually prevents it.

## Components

- **Command: `/safe-commit`** — Secret scanning with .gitignore validation before every commit
- **Command: `/commit`** — Interactive staging with semantic commit messages and untracked file warnings
- **Command: `/review-safe-commit-history`** — Audit existing history for leaked secrets

## Installation

```bash
/plugin install git-public@joshm1-claude-eng-toolkit
```

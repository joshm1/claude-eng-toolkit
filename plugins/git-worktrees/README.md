# git-worktrees

**The problem:** Context switching between branches breaks your local environment. Ports collide, env files get overwritten, databases point at the wrong schema. Developers waste time debugging infrastructure instead of building features.

**How it works:** This plugin provides commands to create and clean up git worktrees with automatic environment isolation. When you create a worktree, it calculates a deterministic port offset (hash-based, 100-999 range), copies and transforms `.env` files with new ports, creates worktree-specific database names, transforms Claude settings paths, and copies IDE configs. Cleanup handles uncommitted change warnings, unpushed commit detection, and optional database teardown.

Complements the `superpowers:using-git-worktrees` skill, which handles the "where should the worktree go" decision. This plugin handles the "make the worktree actually work" part.

**Why it exists:** Worktrees are the best tool for parallel development, but they're useless if every new worktree requires 15 minutes of manual env setup. Automating port isolation, env copying, and database naming turns worktrees from a git feature into an actual workflow.

## Components

- **Command: `/create-worktree`** — Create a worktree with automatic port isolation, env file copying, and Claude settings transformation
- **Command: `/clean-worktree`** — Safely remove worktrees with uncommitted change warnings, unpushed commit detection, and optional database cleanup
- **Skill: `git-worktree-setup`** — Detailed reference for port offset calculation, framework integration, database isolation, and troubleshooting

## Installation

```bash
/plugin install git-worktrees@joshm1-claude-eng-toolkit
```

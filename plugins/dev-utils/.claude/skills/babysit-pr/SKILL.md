---
name: babysit-pr
description: Autonomously babysit a PR through CI, review, conflicts, and merge. Use when the user says "babysit this PR", "watch and merge", "loop until green", "keep pushing until CI passes", "merge when ready", or wants to set up an automated CI-check-fix-push-merge loop for a pull request.
---

# Babysit PR

Single iteration of an autonomous loop that watches a PR through CI, addresses review feedback, resolves conflicts, and merges when green. Designed to be called repeatedly via `/loop`.

**Extra instructions from the user (if any):**
$ARGUMENTS

Follow any extra instructions above alongside the checklist below.

## Context Gathering (first invocation only)

On the first call, identify:
1. **PR number** — `gh pr view --json number --jq .number` (from current branch)
2. **Branch** — `git branch --show-current`
3. **Graphite tracking** — `gt ls --stack 2>/dev/null`; if the branch appears in the stack output, it's tracked by Graphite

## Iteration Checklist

Run through in order. Stop at the first action that requires a push — the next iteration picks up CI.

### 1. Check CI for HEAD commit

Verify CI passed for the **exact commit at HEAD**, not just the latest run. A stale green from an older commit doesn't count.

```bash
HEAD_SHA=$(git rev-parse HEAD)
gh run list --workflow CI --commit $HEAD_SHA --json status,conclusion,databaseId --limit 1
```

| State | Action |
|-------|--------|
| No run for HEAD | Wait — CI hasn't started yet |
| queued / in_progress | Wait |
| success | Proceed to step 5 (merge) |
| failure | Proceed to step 3 (fix) |
| HTTP 403 (rate limit) | Say "rate limited, will retry" and stop |

### 2. Check for new review comments

```bash
gh api repos/{owner}/{repo}/pulls/{pr}/comments
```

Read all comments. For each one, take the appropriate action and **always** do all three: react, reply, resolve.

**If the comment identifies a valid bug:**
1. Fix the code
2. React with thumbs-up: `gh api .../comments/{id}/reactions -X POST -f content="+1"`
3. Reply in-thread: `gh api .../pulls/{pr}/comments -X POST -F in_reply_to={id} -f body="Fixed in {commit_sha}"`

**If the comment is stale (already fixed in a later commit):**
1. React with thumbs-down
2. Reply explaining which commit already addressed it

**If you disagree with the suggestion:**
1. React with thumbs-down
2. Reply with technical reasoning for why the current approach is correct

**If the comment is about missing tests or deferred work:**
1. React with thumbs-down
2. Reply acknowledging and explaining why it's deferred (e.g., "integration tests cover this; isolated unit tests are follow-up work")

After addressing all comments, if any code was changed, commit and push (step 6).

### 3. Fix CI failures

```bash
gh run view {run_id} --log-failed 2>&1 | tail -60
```

Diagnose and fix. Common patterns:
- **Test failure** — read test + source, fix the bug
- **Type/lint error** — fix or run auto-fixer
- **Runner lost connection** — `gh run rerun {id} --failed`
- **Flaky** — re-trigger once; if fails again, investigate

After fixing, commit and push (step 6).

### 4. Handle merge conflicts

```bash
gh pr view {pr} --json mergeable --jq .mergeable
```

If `CONFLICTING`:
```bash
git pull --rebase origin/{base_branch}
```

Use `/resolve-conflicts` skill if available. After resolving, push (step 6).

### 5. Merge

Pre-merge checks:
- CI green for HEAD (step 1 passed)
- No merge conflicts

Squash merge:
```bash
gh pr merge {pr} --squash
```

After merge: cancel the cron job and inform the user.

### 6. Push

**If branch is tracked by Graphite:**
```bash
gt restack
gt submit --stack --no-interactive
```

**If branch is NOT tracked by Graphite:**
```bash
git push origin {branch} --force-with-lease
```

After pushing, stop — next iteration will check CI for the new HEAD.

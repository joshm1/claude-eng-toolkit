# testing

**The problem:** AI coding agents (and humans) rationalize away test failures. "That's pre-existing." "That's unrelated to my changes." "That test is flaky." These are excuses that let broken code ship. Every dismissed test failure is a bug that someone else will have to debug later, without the context you have right now.

**How it works:** This plugin provides a single skill that enforces a strict investigation protocol for every test failure:

1. Read the full error output (not just the summary)
2. Read the failing test code
3. Read the code under test
4. Check your recent changes against the failure
5. Form a hypothesis based on evidence, not assumptions

The skill explicitly bans common escape hatches: "pre-existing," "unrelated," "flaky," "not my problem." After a rebase or merge, any behavior change is your responsibility to investigate. You can't claim main caused it without proving it.

**Why it exists:** Test discipline is a team culture problem disguised as a technical problem. The tool doesn't need to be smarter — it needs to be more disciplined. This plugin makes the AI agent (and by extension, the developer reviewing its work) treat every failure as a real signal until proven otherwise. No shortcuts, no rationalizations, no "I'll fix it later."

The principle is simple: bad work is worse than no work. If you can't explain why a test is failing, you're not done.

## Components

- **Skill: `handling-test-failures`** — Zero-tolerance test failure investigation protocol. Works with pytest, jest, vitest, go test, cargo test, JUnit, RSpec, and any other test framework.
- **Skill: `zero-tolerance-failures`** — Enforcement gate: when ANY errors exist (test failures, type check errors, lint warnings, build failures), this skill blocks progress until every issue is investigated and resolved. No "pre-existing," no "not my problem."

## Installation

```bash
/plugin install testing@joshm1-claude-eng-toolkit
```

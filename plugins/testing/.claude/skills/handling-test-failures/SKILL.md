---
name: handling-test-failures
description: Use when tests fail, test output shows errors or failures, running a test suite, analyzing test results, after a rebase or merge, discussing whether a failure is pre-existing or unrelated, or when about to skip or dismiss a failing test
---

# Handling Test Failures

## The Rule

**Every failing test is your responsibility. Investigate fully before making any claim about its cause.**

"Pre-existing", "unrelated", and "flaky" are conclusions that require evidence, not assumptions. Most of the time these claims are wrong — the failure is related to your changes and you didn't look hard enough.

## Investigation Protocol

When tests fail, follow this sequence **before** drawing any conclusion:

1. **Read the full error output** — the full traceback/stack trace, not just the summary
2. **Read the failing test code** — understand what it asserts and why
3. **Read the code under test** — understand the implementation
4. **Check your changes** — `git diff` to see if you touched anything in the test's dependency chain
5. **Form a hypothesis** — only now, with evidence

## If You Believe a Failure Is Pre-Existing

You still don't get to dismiss it. Instead:

1. **Prove it**: `git stash && <test command>` to verify failure exists without your changes
2. **Show the evidence**: tell the user exactly what you found
3. **Fix it anyway** or ask the user whether they want you to

Never silently move on.

## Banned Phrases

These are almost always wrong and always lazy:

- "This appears to be a pre-existing failure"
- "This test failure is unrelated to our changes"
- "This is likely a flaky test"
- "We can skip this test for now"
- "The test itself appears to be outdated/wrong"
- "This test was already failing before our changes"
- "Ignoring this unrelated failure"

If you catch yourself about to say any of these — **STOP**. You haven't investigated enough.

## Rationalization Table

| Excuse | Reality |
|--------|---------|
| "Pre-existing failure" | Did you actually verify with `git stash`? Almost always you didn't. |
| "Unrelated to our changes" | Did you trace the full dependency chain? Indirect breakage is common. |
| "Flaky/intermittent test" | Did you run it multiple times? Real flakiness is rarer than you think. |
| "Test is outdated/wrong" | Did you read what it's testing? The test is probably right and your code is wrong. |
| "Only integration tests fail" | Integration tests catch real bugs. They matter more, not less. |
| "Test environment issue" | Did you check? Or are you guessing to avoid investigating? |
| "Main branch introduced these" | You rebased. The result is your responsibility. |
| "24 of 35 failures are from the other PR" | 35 failures = 35 things you fix. Stop categorizing, start fixing. |

## After Rebases and Merges

Rebases are the #1 source of "not my problem" rationalization. When you rebase or merge:

- **You accepted the result.** Every conflict resolution was your decision.
- **Behavior changes from the base branch are now yours.** Tests that fail because main changed something — you fix them.
- **Run ALL tests, not just "yours."** There is no "yours" after a rebase. It's all yours.

## Red Flags — STOP and Investigate

You are making an excuse if you're thinking:

- "I'll just focus on the tests related to my changes"
- "These failures are probably from the base branch"
- "Let me just run the specific tests for my feature"
- "The important tests pass"
- "I can't reproduce this locally, must be environment"

**All of these mean: investigate more. You haven't looked hard enough.**

## Required Reading

**REQUIRED BACKGROUND:** You MUST understand superpowers:test-driven-development before writing or fixing tests. That skill defines the RED-GREEN-REFACTOR cycle. Combined with this skill, the expectation is: write tests first, and when they fail, investigate — never dismiss.

## Applies to All Test Frameworks

This discipline applies regardless of language or runner: pytest, jest, vitest, go test, cargo test, JUnit, RSpec, or anything else. The excuses are the same everywhere.

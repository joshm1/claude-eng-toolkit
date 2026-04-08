# parallel-agents

**The problem:** Large features get implemented sequentially even when many parts are independent. A feature with 5 independent components takes 5x as long because one agent works through them one at a time.

**How it works:** This plugin provides orchestration for splitting features into independent chunks and running multiple Claude Code subagents concurrently. It analyzes the implementation plan, identifies which tasks have no shared state or sequential dependencies, dispatches them to separate agents (optionally in isolated worktrees), and coordinates the results.

**Why it exists:** AI agents are cheap and parallelizable. The bottleneck isn't the agent's speed on any single task, it's that we default to sequential execution out of habit. When you have 5 independent modules to build, running 5 agents simultaneously turns a 2-hour job into a 25-minute job. This plugin makes parallel-by-default the natural workflow.

**Relationship to superpowers:** This was built for personal use before adopting the [superpowers](https://github.com/obra/superpowers) plugin. Superpowers provides `dispatching-parallel-agents` (a conceptual pattern guide) and `subagent-driven-development` (sequential execution with review gates). This plugin adds automated analysis, wave-based dependency planning, and E2E test sequencing on top of those foundations. They're complementary: superpowers tells you how to think about parallel agents, this plugin orchestrates the actual execution.

## Components

- **Skill: `parallel-implement`** — Split a feature into independent chunks, dispatch to concurrent subagents, coordinate results
- **Skill: `parallel-implement-command`** — Orchestration command interface for multi-agent work

## Installation

```bash
/plugin install parallel-agents@joshm1-claude-eng-toolkit
```

# Claude Engineering Toolkit

> A curated collection of Claude Code plugins that encode engineering team practices into automated tooling. Built by an engineering leader who believes the best team standards aren't documented in wikis — they're enforced by the tools you use every day.

## Why This Exists

Every engineering team has opinions about quality: run the linter, write the tests, don't commit secrets. Most teams put these in a wiki page that gets ignored. Some teams encode them in CI pipelines that catch problems too late. The best teams build them into the developer workflow so the right thing happens by default.

These plugins are that idea applied to AI-assisted development. Each one encodes a specific engineering opinion — strict types by default, secrets scanned before commit, every test failure investigated — and makes it the path of least resistance inside Claude Code.

They're opinionated by design. A plugin that says "maybe consider running tests" is useless. A plugin that refuses to move forward until every failure is explained is a team standard you can actually rely on.

## Plugins

| Plugin | Team Problem It Solves | Key Insight |
|--------|----------------------|-------------|
| **python-dev** | Python codebases decay without strict type enforcement | Strict types by default, not opt-in. No `Any`, no `cast()`, no `type: ignore`. |
| **typescript-dev** | Type errors accumulate between edits and CI | Catch type drift at edit time in under 30 seconds, not at CI time 10 minutes later. |
| **browser-testing** | Nobody actually tests the UI systematically | Headless browser QA with screenshots as a first-class development workflow. |
| **git-public** | Secrets leak in public repos because scanning happens too late | Scan staged files for secrets before commit, not after push. Block the commit, not the PR. |
| **dev-utils** | Context switching between branches kills focus and breaks environments | Git worktree isolation with port offsets, env copying, and parallel implementation across agents. |
| **react-native-appium** | Mobile E2E tests are unreliable and hard to maintain | TDD workflow with Page Object patterns and accessibility-first selectors (`testID` over XPath). |
| **openclaw** | AI skill authoring has no standardized format | Structured SKILL.md format with frontmatter, metadata gating, and distribution through ClawHub. |
| **testing** | "Pre-existing" and "flaky" are excuses to skip investigation | Zero tolerance: every test failure is your responsibility until you prove otherwise with evidence. |

## Installation

```bash
# Add the marketplace
/plugin marketplace add joshm1/claude-eng-toolkit

# Browse and install individual plugins
/plugin

# Or install directly
/plugin install python-dev@joshm1-claude-eng-toolkit
/plugin install git-public@joshm1-claude-eng-toolkit
```

## What's Inside Each Plugin

Every plugin follows the standard [Claude Code plugin format](https://code.claude.com/docs/en/discover-plugins): agents, slash commands, skills, and hooks inside a `.claude/` directory. Markdown files with YAML frontmatter. No external dependencies beyond what Claude Code already provides.

See individual plugin READMEs for details:

- [`plugins/browser-testing/`](plugins/browser-testing/) — E2E documentation and QA automation
- [`plugins/git-public/`](plugins/git-public/) — Secret scanning and semantic commit workflows
- [`plugins/testing/`](plugins/testing/) — Test failure investigation discipline
- [`plugins/python-dev/`](plugins/python-dev/) — Strict Python type safety and testing
- [`plugins/typescript-dev/`](plugins/typescript-dev/) — Post-edit TypeScript checking
- [`plugins/dev-utils/`](plugins/dev-utils/) — Worktree management and dev infrastructure
- [`plugins/react-native-appium/`](plugins/react-native-appium/) — Mobile E2E TDD workflow
- [`plugins/openclaw/`](plugins/openclaw/) — OpenClaw skill authoring standards

## About the Author

I'm Josh McDade — VP of Engineering with nearly 20 years building software and 12+ years leading engineering teams and organizations. Most recently leading platform engineering, data engineering, DevOps, and product engineering teams. I built these plugins to encode the practices I believe in into tooling that actually enforces them.

Currently exploring what engineering leadership looks like in the AI-native era. If you're hiring, let's talk.

[LinkedIn](https://www.linkedin.com/in/joshmcdade/) | [GitHub](https://github.com/joshm1)

## License

[MIT](LICENSE)

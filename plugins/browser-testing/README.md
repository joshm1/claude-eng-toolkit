# browser-testing

**The problem:** Nobody systematically tests the UI. Manual QA is inconsistent, hard to repeat, and rarely documented. When someone reports a visual bug, there's no baseline to compare against and no evidence trail showing what was checked.

**How it works:** This plugin provides an E2E documentation and QA skill that uses a headless browser (via `agent-browser`) to walk through your web application. It navigates pages, captures screenshots at meaningful states, inspects for visual and functional issues, and produces polished markdown documentation with embedded screenshots.

The skill follows a structured flow: navigate to each page, snapshot the state, interact with key elements (forms, buttons, navigation), capture before/after screenshots, check for console errors and failed network requests, and flag anything that looks broken.

**Why it exists:** QA should be a first-class development workflow, not an afterthought someone does manually before a release. When AI can drive a browser, take screenshots, and document what it finds, there's no excuse for shipping without systematic visual testing. This plugin makes "did you actually look at it?" the default, not the exception.

## Components

- **Skill: `e2e-documentation-qa`** — Walks through a web app, captures screenshots at each step, inspects for layout issues, broken elements, console errors, and network failures. Produces stakeholder-ready markdown docs with sequential screenshots. Handles responsive testing across viewports.

## Installation

```bash
/plugin install browser-testing@joshm1-claude-eng-toolkit
```

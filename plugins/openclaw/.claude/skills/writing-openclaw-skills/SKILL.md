---
name: writing-openclaw-skills
description: Use when creating, editing, or debugging OpenClaw skills (SKILL.md files with YAML frontmatter, metadata gating, installer specs). Also use when porting Claude Code skills to OpenClaw format.
---

# Writing OpenClaw Skills

## Overview

OpenClaw skills are directories containing a `SKILL.md` file with YAML frontmatter that teach agents how to use tools. Skills are loaded into the agent's system prompt at session start based on metadata gating.

## Quick Start — Minimal Skill

```
my-skill/
  SKILL.md
```

```markdown
---
name: my-skill
description: Human-readable description of what this skill does
---

Instructions for the agent go here.
Use {baseDir} to reference the skill's own directory.
```

This is all you need for a basic skill. The two required frontmatter fields are `name` and `description`.

## Skill Locations (Load Precedence)

1. **Workspace skills** (`<workspace>/skills/`) — highest priority
2. **Managed skills** (`~/.openclaw/skills/`)
3. **Bundled skills** — lowest priority

Additional directories via `skills.load.extraDirs` in `~/.openclaw/openclaw.json`.

## Frontmatter Reference

### Required Fields

| Field | Type | Purpose |
|-------|------|---------|
| `name` | string | Skill identifier |
| `description` | string | What the skill does, shown in UI |

### Optional Fields

| Field | Type | Default | Purpose |
|-------|------|---------|---------|
| `homepage` | URL | — | Website link in macOS UI |
| `user-invocable` | boolean | `true` | Expose as slash command |
| `disable-model-invocation` | boolean | `false` | Exclude from model prompt |
| `command-dispatch` | string | — | Set to `tool` for direct dispatch |
| `command-tool` | string | — | Tool name when dispatching |
| `command-arg-mode` | string | `raw` | How args are passed |
| `metadata` | JSON (single-line) | — | Gating rules and configuration |

## Metadata Gating

The `metadata` field controls when a skill is eligible to load. It **must be a single-line JSON object** in the frontmatter.

### Common Patterns

**Require a binary on PATH:**
```yaml
metadata: {"openclaw": {"requires": {"bins": ["docker"]}}}
```

**Require at least one of several binaries:**
```yaml
metadata: {"openclaw": {"requires": {"anyBins": ["npm", "pnpm", "yarn", "bun"]}}}
```

**Restrict to macOS only:**
```yaml
metadata: {"openclaw": {"os": ["darwin"]}}
```

**Require an environment variable (e.g. API key):**
```yaml
metadata: {"openclaw": {"requires": {"env": ["OPENAI_API_KEY"]}, "primaryEnv": "OPENAI_API_KEY"}}
```

**Require a config path to be truthy:**
```yaml
metadata: {"openclaw": {"requires": {"config": ["skills.entries.my-skill.enabled"]}}}
```

**Always load (bypass all gating):**
```yaml
metadata: {"openclaw": {"always": true}}
```

**Combine multiple constraints:**
```yaml
metadata: {"openclaw": {"os": ["darwin", "linux"], "requires": {"bins": ["ffmpeg"], "env": ["API_KEY"]}, "primaryEnv": "API_KEY", "emoji": "🎬"}}
```

### All metadata.openclaw Fields

| Field | Type | Description |
|-------|------|-------------|
| `always` | boolean | Bypass all gating if true |
| `emoji` | string | Icon for macOS Skills UI |
| `homepage` | URL | Website link for UI |
| `os` | array | Eligible platforms: `darwin`, `linux`, `win32` |
| `requires.bins` | array | All listed binaries must exist on PATH |
| `requires.anyBins` | array | At least one must exist on PATH |
| `requires.env` | array | Required environment variables |
| `requires.config` | array | Config paths that must be truthy |
| `primaryEnv` | string | Env var for `skills.entries.<name>.apiKey` |
| `skillKey` | string | Override key for config lookups |
| `install` | array | Installer specifications |

## Installer Specs

Include install instructions so users can auto-install dependencies:

```yaml
metadata: {"openclaw": {"requires": {"bins": ["rg"]}, "install": [{"id": "ripgrep-brew", "kind": "brew", "label": "Install ripgrep via Homebrew", "bins": ["rg"]}]}}
```

### Installer Kinds

| Kind | Fields | Notes |
|------|--------|-------|
| `brew` | `id`, `label`, `bins` | Preferred on macOS |
| `node` | `id`, `label`, `bins` | Respects `skills.install.nodeManager` config |
| `go` | `id`, `label`, `bins` | Auto-installs Go via Homebrew if missing |
| `uv` | `id`, `label`, `bins` | Python package manager |
| `download` | `id`, `label`, `bins`, `url`, `archive`, `extract`, `stripComponents`, `targetDir` | Manual download |

Each installer spec requires: `id` (unique), `kind`, `label` (user-facing), `bins` (expected binaries after install). Optionally add `os` to restrict per platform.

## Directory Structure

### Simple skill
```
my-skill/
  SKILL.md
```

### Skill with supporting files
```
my-skill/
  SKILL.md
  reference.md        # Heavy reference docs
  scripts/
    helper.sh          # Reusable tools
```

Use `{baseDir}` in SKILL.md to reference files relative to the skill directory.

## User Configuration

Users configure skills in `~/.openclaw/openclaw.json`:

```json
{
  "skills": {
    "entries": {
      "my-skill": {
        "enabled": true,
        "apiKey": "sk-...",
        "env": {"CUSTOM_VAR": "value"},
        "config": {"key": "value"}
      }
    }
  }
}
```

- `enabled: false` disables a skill even if installed
- `apiKey` injects into the `primaryEnv` variable
- `env` values injected only if not already set in environment

## Token Impact

Every eligible skill adds to the system prompt. Budget carefully:

- **Base overhead**: ~195 characters (if any skills load)
- **Per skill**: ~97 characters + length of name + description + location
- Rough estimate: **~24 tokens per skill** plus field content

Keep descriptions concise. Move heavy reference to separate files loaded via `{baseDir}`.

## Common Mistakes

- **Multi-line metadata**: The parser only supports single-line JSON for the `metadata` field. Always write it on one line.
- **Missing directory**: Skills must be a directory containing `SKILL.md`, not a standalone `.md` file.
- **Forgetting gating**: Without `requires.bins`, the skill loads even if the tool isn't installed, leading to agent errors.
- **Bloated SKILL.md**: Large skill files consume tokens every session. Extract reference material to separate files and use `{baseDir}` paths.
- **Vague description**: The description is how the agent and UI discover the skill. Be specific about what it does.

## ClawHub Distribution

Publish skills to the public registry at https://clawhub.com:

- `clawhub install <skill-slug>` — install from registry
- `clawhub update --all` — update all installed skills
- `clawhub sync --all` — sync all skills

Installed skills go to `./skills` in the current directory by default.

---
name: release-plugin
description: Release a plugin with version bump, commit, and git tag
argument-hint: <plugin> [patch|minor|major]
arguments:
  - name: plugin
    description: Plugin name (e.g., browser-testing, python-dev)
    required: true
  - name: bump
    description: Version bump type (patch, minor, major)
    required: false
    default: patch
---

# Release Plugin Command

Automate plugin release workflow: stage plugin changes → version bump → commit → tag.

## Arguments

Parse from `$ARGUMENTS`:
- **plugin** (required): Plugin name (e.g., `browser-testing`, `python-dev`, `dev-utils`)
- **bump** (optional): Version bump type - `patch` (default), `minor`, or `major`

**First release detection:** If the plugin's current version in marketplace.json is `1.0.0` and there is no existing git tag `{plugin}-v1.0.0`, this is the first release. Skip the bump — use `1.0.0` as-is. Do not prompt for patch/minor/major.

**Examples:**
- `/release-plugin openclaw` → first release, uses 1.0.0 (no tag exists yet)
- `/release-plugin python-dev` → patch bump for python-dev
- `/release-plugin dev-utils minor` → minor bump for dev-utils
- `/release-plugin browser-testing major` → major bump for browser-testing

## Steps

1. **Check for plugin changes** in `plugins/{plugin}/` directory
   - Run `git status plugins/{plugin}/` to see staged/unstaged/untracked files
   - If no changes exist, warn the user and ask if they want to proceed with just a version bump
2. **Stage all plugin changes** with `git add plugins/{plugin}/`
3. **Read current marketplace.json** to get the current version for `{plugin}`
4. **Check if this is a first release**: run `git tag -l "{plugin}-v*"` — if no tags exist and version is `1.0.0`, skip the bump and use `1.0.0`
5. **Calculate new version** (skip if first release) based on `{bump}` (default: patch)
   - patch: 1.2.3 → 1.2.4
   - minor: 1.2.3 → 1.3.0
   - major: 1.2.3 → 2.0.0
6. **Update marketplace.json** with the new version (skip if first release — already `1.0.0`)
7. **Stage marketplace.json** with `git add .claude-plugin/marketplace.json`
8. **Show staged changes** with `git diff --cached --stat` for user review
9. **Commit all changes** - the commit message must describe WHAT was added/changed, NOT just "Release vX.Y.Z"
   - Title format: `feat({plugin}): {what was added}` - e.g., "feat(dev-utils): Add /parallel-implement command"
   - Body: describe the key features/changes in bullet points
   - The version number goes in the tag, NOT the commit title
10. **Create annotated git tag** `{plugin}-v{new_version}` with release notes
11. **Report** the new version and next steps

## Critical Rules

- **ALWAYS check `git status plugins/{plugin}/`** FIRST before doing anything else
- **ALWAYS stage plugin directory changes** before updating marketplace.json
- **NEVER commit just marketplace.json** if there are unstaged plugin changes
- **NEVER use "Release vX.Y.Z" as the commit title** - describe what was actually added/changed
- Use `git add plugins/{plugin}/` to add all changes including untracked files

## Execution Order

```bash
# 1. FIRST: Check what changes exist in the plugin directory
git status plugins/{plugin}/

# 2. Stage ALL plugin changes (including untracked files)
git add plugins/{plugin}/

# 3. Read marketplace.json for current version
cat .claude-plugin/marketplace.json

# 3b. Check if first release (no existing tags for this plugin)
git tag -l "{plugin}-v*"
# If no tags and version is 1.0.0 → first release, skip bump and marketplace update

# 4. Update marketplace.json with new version (use Edit tool) — skip if first release

# 5. Stage marketplace.json
git add .claude-plugin/marketplace.json

# 6. Show what will be committed
git diff --cached --stat

# 7. Commit everything together (describe WHAT changed, not just version)
git commit -m "feat({plugin}): {what was added/changed}

- Key feature or change 1
- Key feature or change 2"

# 8. Create tag
git tag -a {plugin}-v{new_version} -m "{plugin} v{new_version}: {summary}"
```

## Output

After successful release, display:
- Plugin: {plugin}
- Old version: {old_version}
- New version: {new_version}
- Files changed: {list from git diff --cached --stat}
- Tag created: {plugin}-v{new_version}
- Next steps: `git push && git push --tags`

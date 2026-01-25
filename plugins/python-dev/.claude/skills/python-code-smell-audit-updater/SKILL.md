---
name: python-code-smell-audit-updater
description: Use when user gives feedback about Python code being a code smell, bad practice, anti-pattern, or mistake. Triggers on "that's a code smell", "don't do that", "that's bad", "that's wrong" about Python code.
---

# Python Code Smell Audit Updater

When the user identifies a Python code pattern as bad or a code smell, update the audit skill.

## Steps

1. Read the python-code-smell-audit skill in this plugin
2. Add a new section for the identified code smell with:
   - Clear title
   - **Bad:** example showing the anti-pattern
   - **Good:** example showing the correct approach (if applicable)
   - **Detection:** command to find violations
   - Brief explanation of why it's problematic
3. Keep entries concise

## Format for New Entries

```markdown
### [Smell Name]
Brief description.

**Bad:**
```python
# anti-pattern code
```

**Good:**
```python
# correct approach
```

**Detection:**
```bash
rg "pattern" --type py
# or: ruff check --select=RULE .
```
```

## Important

- Only add genuinely problematic patterns, not style preferences
- Deduplicate: don't add if a similar smell already exists
- Keep examples minimal and focused
- Include detection command where possible (rg or ruff)

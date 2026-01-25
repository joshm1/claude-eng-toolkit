---
name: ruff-fix
description: Use when fixing ruff violations, linting errors, or auto-fixing Python code. Triggers on ruff errors, lint issues, or requests to clean up Python code quality.
---

# Ruff Fix

Fix ruff violations with strict type safety enforcement.

---

## Core Rules

**Never use these as fixes:**
- `# type: ignore` - hides real errors
- `cast()` - lies to the type checker
- `Any` - defeats type checking

**Always:** Fix the root cause, not the symptom.

---

## Workflow

1. **Identify violations:**
   ```bash
   ruff check . --output-format=grouped
   ```

2. **Apply safe auto-fixes:**
   ```bash
   ruff check . --fix
   ```

3. **Fix remaining issues manually** - following strict rules above

4. **Verify clean:**
   ```bash
   ruff check . && uv run pyright .
   ```

---

## Recommended Rules

Add to `pyproject.toml`:

```toml
[tool.ruff]
line-length = 100
target-version = "py311"

[tool.ruff.lint]
select = [
    "E",      # pycodestyle errors
    "F",      # pyflakes
    "W",      # pycodestyle warnings
    "I",      # isort
    "UP",     # pyupgrade
    "B",      # flake8-bugbear
    "SIM",    # flake8-simplify
    "RUF",    # ruff-specific
    "PERF",   # performance
    "ANN",    # annotations
    "BLE",    # blind exceptions
]
ignore = [
    "ANN101",  # missing self type
    "ANN102",  # missing cls type
]

[tool.ruff.lint.per-file-ignores]
"tests/*" = ["ANN"]  # Less strict in tests
```

---

## Common Fixes

| Violation | Wrong Fix | Correct Fix |
|-----------|-----------|-------------|
| Missing type annotation | Add `Any` | Add specific type |
| Type mismatch | Add `cast()` | Fix the type design |
| Import error | Add `# type: ignore` | Fix the import or add stub |
| Broad exception | Keep `except Exception` | Catch specific exceptions |
| Mutable default | Ignore warning | Use `None` default pattern |

---

## Detection Commands

Find type shortcuts in codebase:
```bash
rg "\bAny\b|cast\(|# type: ignore" --type py
```

Find untyped functions:
```bash
rg "def \w+\([^)]*\):" --type py | rg -v "->"
```

---

## Red Flags

Stop if you're about to:
- Add `Any` to silence a type error
- Add `cast()` because types don't match
- Add `# type: ignore` because pyright complains
- Disable a ruff rule instead of fixing the code

**All of these mean:** Fix the root cause instead.

---

## Related Skills

| Skill | Relationship |
|-------|--------------|
| pyright-strict-types | Comprehensive typing guidance |
| python-code-smell-audit | Broader code quality checks |

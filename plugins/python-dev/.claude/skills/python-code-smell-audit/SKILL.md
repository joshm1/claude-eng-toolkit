---
name: python-code-smell-audit
description: Use when auditing Python code quality, reviewing AI-generated code, or checking for common mistakes. Triggers on code review, code smell, audit, or quality check requests.
---

# Python Code Smell Audit

Audit Python code for type safety violations and common AI coding agent mistakes.

---

## Type Safety Violations

### Untyped `Any`
Using `Any` defeats type checking. Never use without documented justification.

**Bad:**
```python
def process(data: Any) -> Any:
    return data
```

**Detection:**
```bash
rg "\bAny\b" --type py
```

---

### Manual `cast()`
`cast()` is a lie to the type checker. Fix the actual type issue instead.

**Bad:**
```python
result = cast(str, some_value)
```

**Why:** If you need `cast()`, the type design is wrong. Fix the root cause.

**Detection:**
```bash
rg "cast\(" --type py
```

---

### Type Ignore Comments
`# type: ignore` hides real errors. Never use as a fix.

**Bad:**
```python
value: str = get_value()  # type: ignore
```

**Detection:**
```bash
rg "# type: ignore" --type py
```

---

### Untyped Function Signatures
All functions must have complete type annotations.

**Bad:**
```python
def process_data(items):
    return items
```

**Good:**
```python
def process_data(items: list[str]) -> list[str]:
    return items
```

**Detection:**
```bash
rg "def \w+\([^)]*\):" --type py | rg -v "->"
```

---

### Loose Dict Types
Using `dict[str, Any]` loses all type safety. Use TypedDict or Pydantic.

**Bad:**
```python
def get_user() -> dict[str, Any]:
    return {"name": "Alice", "age": 30}
```

**Good:**
```python
class UserData(TypedDict):
    name: str
    age: int

def get_user() -> UserData:
    return {"name": "Alice", "age": 30}
```

**Detection:**
```bash
rg "dict\[str,\s*Any\]" --type py
```

---

## Common AI Agent Mistakes

### Inline Imports
Imports inside functions that should be at module level.

**Bad:**
```python
def process_data():
    import json
    return json.dumps(data)
```

**Acceptable:** Only for expensive lazy-loading (e.g., `import pandas`, `import torch`).

**Detection:**
```bash
rg "^\s+import \w+" --type py
```

---

### Deep Relative Imports
Relative imports with multiple dots.

**Bad:**
```python
from ...models import User
```

**Good:**
```python
from myproject.models import User
```

**Detection:**
```bash
rg "from \.\.\.+" --type py
```

---

### Mutable Default Arguments
Mutable defaults are shared across calls.

**Bad:**
```python
def add_item(item: str, items: list[str] = []) -> list[str]:
    items.append(item)
    return items
```

**Good:**
```python
def add_item(item: str, items: list[str] | None = None) -> list[str]:
    if items is None:
        items = []
    items.append(item)
    return items
```

**Detection:** Ruff rule B006.
```bash
ruff check --select=B006 .
```

---

### Broad Exception Handling
Catching `Exception` or bare `except:` hides bugs.

**Bad:**
```python
try:
    do_something()
except Exception:
    pass
```

**Good:**
```python
try:
    do_something()
except ValueError as e:
    logger.error(f"Invalid value: {e}")
```

**Detection:** Ruff rule BLE001.
```bash
ruff check --select=BLE .
```

---

### String Concatenation in Loops
Building strings with `+=` in loops is O(n²).

**Bad:**
```python
result = ""
for item in items:
    result += str(item)
```

**Good:**
```python
result = "".join(str(item) for item in items)
```

**Detection:** Ruff rule PERF401.
```bash
ruff check --select=PERF .
```

---

### Unused Imports

**Detection:** Ruff rule F401.
```bash
ruff check --select=F401 .
```

---

## Quick Detection

Type safety violations:
```bash
rg "\bAny\b|cast\(|# type: ignore" --type py
```

Ruff smell checks:
```bash
ruff check --select=B006,BLE,PERF,F401 .
```

Untyped functions:
```bash
rg "def \w+\([^)]*\):" --type py | rg -v "->"
```

---

## Related Skills

| Skill | Relationship |
|-------|--------------|
| ruff-fix | Fixing violations with strict rules |
| pyright-strict-types | Comprehensive typing guidance |

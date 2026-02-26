# Triage TODOs

## Marker Types

Search for all common marker patterns in the source code:

| Marker        | Typical Meaning                                | Default Severity |
| ------------- | ---------------------------------------------- | ---------------- |
| `TODO`        | Planned work, feature to implement             | Medium           |
| `FIXME`       | Known bug or incorrect behavior                | High             |
| `HACK`        | Workaround that should be replaced             | High             |
| `XXX`         | Dangerous or requires immediate attention      | High             |
| `WORKAROUND`  | Temporary solution for a known issue           | Medium           |
| `OPTIMIZE`    | Performance improvement opportunity            | Medium           |
| `BUG`         | Known bug                                      | High             |
| `NOTE` / `NB` | Important context (review, usually not a task) | —                |
| `@todo`       | Common in PHPDoc, JSDoc, Javadoc               | Medium           |

## Search Commands

Adjust the file extensions and directories for the project's language and structure. Always exclude dependency and build output directories.

```bash
# JavaScript / TypeScript
grep -rn "TODO\|FIXME\|HACK\|XXX\|WORKAROUND\|OPTIMIZE\|BUG\|@todo" src/ --include="*.{ts,tsx,js,jsx,mjs,cjs}" | head -100

# Python
grep -rn "TODO\|FIXME\|HACK\|XXX\|WORKAROUND\|OPTIMIZE\|BUG" . --include="*.py" --exclude-dir={venv,.venv,__pycache__} | head -100

# Go
grep -rn "TODO\|FIXME\|HACK\|XXX\|WORKAROUND\|OPTIMIZE\|BUG" . --include="*.go" --exclude-dir=vendor | head -100

# Java / Kotlin
grep -rn "TODO\|FIXME\|HACK\|XXX\|WORKAROUND\|OPTIMIZE\|BUG" src/ --include="*.{java,kt,kts}" | head -100

# Rust
grep -rn "TODO\|FIXME\|HACK\|XXX\|WORKAROUND\|OPTIMIZE\|BUG" src/ --include="*.rs" | head -100

# Ruby
grep -rn "TODO\|FIXME\|HACK\|XXX\|WORKAROUND\|OPTIMIZE\|BUG" . --include="*.rb" --exclude-dir=vendor | head -100

# C# / .NET
grep -rn "TODO\|FIXME\|HACK\|XXX\|WORKAROUND\|OPTIMIZE\|BUG" . --include="*.{cs,fs}" --exclude-dir={bin,obj} | head -100

# PHP
grep -rn "TODO\|FIXME\|HACK\|XXX\|WORKAROUND\|@todo\|BUG" . --include="*.php" --exclude-dir=vendor | head -100
```

## Triage Process

For each marker found:

1. **Evaluate relevance** — Is the issue still present, or was it resolved without removing the comment?
2. **Assess severity** — Use the default severity from the table above as a starting point, then adjust based on context:
   - Security-related markers (e.g., `// HACK: skipping auth check`) → upgrade to Critical
   - Markers referencing data integrity or correctness → upgrade to High
   - Markers about code style or minor cleanup → downgrade to Low
3. **Create or absorb** — Either create a dedicated task or note it under a related task's Implementation steps. Prefer absorbing into existing tasks when the marker's scope overlaps with a planned task.
4. **Flag stale comments** — If the TODO references a resolved issue, a closed ticket number, or code that no longer exists, create a single Low-priority cleanup task to remove all stale markers.

## Output

Record a summary of marker counts for the Checkpoint:

| Metric                         | Count |
| ------------------------------ | ----- |
| Total markers found            | {{N}} |
| Absorbed into existing tasks   | {{N}} |
| New tasks created from markers | {{N}} |
| Stale markers to clean up      | {{N}} |

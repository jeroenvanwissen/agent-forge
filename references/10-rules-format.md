# Rules Format

Generate rule files in `.ai/rules/`. These are the canonical rules shared across all AI tools.

## Writing Guidelines

- **Imperative voice:** "Use camelCase" not "We use camelCase"
- **Concrete examples:** Show real code from the project, not hypothetical code
- **Do/Don't tables:** End each major section with a Do/Don't table
- **Convention gaps:** Mark clearly — `> **No established convention.** Recommended: ...`
- **No speculation:** Only document conventions that actually exist in the codebase
- **Lean rules:** If a section has fewer than 2 rules, fold it into an adjacent section

## Core Rule Files

### .ai/rules/project.md

Project structure, module organization, naming, configuration, dependencies.

```markdown
# Project Guidelines

## Directory Structure
{{Annotated tree of the project}}

## Module Organization
- {{Where to place new files by type}}
- {{Module boundaries and import rules}}

## Naming Conventions
### Files — {{convention with examples}}
### Directories — {{convention with examples}}

## Configuration
- {{How config values are managed — env vars, config files, etc.}}
- {{How to add new config values}}

## Dependencies
- {{Version pinning strategy}}
- {{Evaluation checklist before adding new deps:}}
  - Does an existing dependency already cover this?
  - Is it actively maintained (commits in last 6 months)?
  - What's the bundle size impact?
  - Is the license compatible?
  - How many transitive dependencies?

## Do / Don't
| Do | Don't |
|----|-------|
| {{observed good pattern}} | {{observed anti-pattern}} |
```

### .ai/rules/code-style.md

Formatting, linting, type checking, naming, imports, documentation comments.

```markdown
# Code Style

## Automated Formatting
- **Formatter:** {{tool and version}}
- **Config:** `{{config file path}}`
- **Format:** `{{format command}}`
- **Check:** `{{check command}}`
- Always run the formatter before committing. Never format manually.

## Linting
- **Linter:** {{tool and version}}
- **Config:** `{{config file path}}`
- **Lint:** `{{lint command}}`
- **Auto-fix:** `{{fix command}}`
- Zero warnings policy. Fix issues rather than suppressing.
- Suppression requires a comment explaining the exact reason.

## Type Checking
- **Type checker:** {{tool and version}}
- **Config:** `{{config file path}}`
- **Check:** `{{type check command}}`
- {{Strictness level and any notable settings}}

## Naming
### Variables and Functions — {{convention}}
### Types / Interfaces — {{convention}}
### Constants — {{convention}}

## Imports
- {{Ordering rules}}
- {{Path alias conventions}}
- {{Named vs default export policy}}

## Documentation Comments
- Required on: {{what must be documented}}
- Format: {{JSDoc, docstring, XML doc, etc. with example}}
- Inline comments: explain "why" not "what"

## Do / Don't
| Do | Don't |
|----|-------|
```

### .ai/rules/testing.md

Test framework, categories, coverage, test quality rules.

```markdown
# Testing Conventions

## Framework
- **Test runner:** {{tool}}
- **Assertion:** {{library}}
- **Mocking:** {{approach}}

## Test Categories

| Category | Location | Naming | Speed | Dependencies |
|----------|----------|--------|-------|-------------|
| Unit | {{path}} | {{pattern}} | < 100ms | Mock all I/O |
| Integration | {{path}} | {{pattern}} | < 5s | Real DB, mock external |
| E2E | {{path}} | {{pattern}} | < 30s | Full stack |

## Required Tests by Task Type

| Task Type | Required Tests |
|-----------|---------------|
| Bug fix | Regression test reproducing the bug |
| New feature | Happy path, error paths, boundary conditions |
| Refactor | No net decrease in test count or coverage |
| Security fix | Test verifying the vulnerability is blocked |

## Coverage
- **Baseline:** {{X%}}
- **No-decrease rule:** Every PR must maintain or increase coverage
- **New code target:** ≥ {{X%}} line coverage
- **Command:** `{{coverage command}}`

## Test Quality Rules
- Each test tests one behavior
- Test names: `should {behavior} when {condition}`
- No execution order dependencies
- No shared mutable state between tests
- Deterministic — no randomness without seeding/mocking
- No flaky tests

## Do / Don't
| Do | Don't |
|----|-------|
```

### .ai/rules/security.md

Secrets, input validation, auth, data protection.

```markdown
# Security Requirements

## Secrets Management
- {{Where secrets are stored}}
- {{How secrets are accessed in code}}
- {{What must never appear in source}}

## Input Validation
- {{Where validation happens (boundary, middleware, etc.)}}
- {{Validation library and approach}}
- {{Parameterized queries — never string interpolation}}

## Authentication & Authorization
- {{Auth mechanism}}
- {{Where auth is checked}}
- {{Role/permission model}}

## Data Protection
- {{Password hashing approach}}
- {{PII handling rules}}
- {{What must never be logged}}

## Dependencies
- Run vulnerability scanner {{frequency}}
- Critical vulns patched within {{timeframe}}

## Do / Don't
| Do | Don't |
|----|-------|
```

### .ai/rules/quality.md

Complexity limits, cognitive complexity patterns, coverage policy, hotspot tracking.

```markdown
# Code Quality

## Complexity Limits

| Metric | Limit | How to Fix |
|--------|-------|-----------|
| Function length | {{N}} lines | Extract helper functions |
| File length | {{N}} lines | Split into modules |
| Nesting depth | {{N}} levels | Use early returns, extract methods |
| Function parameters | {{N}} params | Use options/config object |
| Cyclomatic complexity | {{N}} per fn | Simplify branching |

## Reducing Cognitive Complexity

### Early returns instead of nesting
```{{language}}
// BAD
{{nested example from the project}}

// GOOD
{{flattened example with early returns}}
```

### Extract complex conditions
```{{language}}
// BAD
if ({{complex inline condition}}) {

// GOOD
const {{namedBoolean}} = {{extracted condition}};
if ({{namedBoolean}}) {
```

## Automated Tooling
### Formatter — {{tool, config, commands, policy}}
### Linter — {{tool, config, commands, zero warnings policy, suppression rules}}
### Type Checker — {{tool, config, commands, strictness}}

## Coverage Policy
- **Baseline:** {{X%}}
- **No-decrease rule:** every PR
- **New code target:** ≥ {{X%}}

## Documentation Requirements
- Always document: {{list}}
- Never document: {{list}}
- Comment format: {{example}}

## Hotspot Tracking

| File | Issue | Lines | Target Task |
|------|-------|-------|-------------|
| {{file}} | {{description}} | {{N}} | {{task ID or "—"}} |

## Do / Don't
| Do | Don't |
|----|-------|
```

## Optional Rule Files

For domain-specific rules (API, database, deployment, git, observability, performance, accessibility, i18n), see [references/additional-rules.md](references/additional-rules.md). Only create these if the project has established conventions for that domain.

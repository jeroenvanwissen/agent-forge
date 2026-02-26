# Extract Conventions

Extract what the project **actually does**, not what it should do. Each convention becomes a rule in `.ai/rules/`. If no convention exists for an area, flag it as a gap.

## Documentation-First Projects

When working from feature/technical docs rather than source code, conventions are **derived** rather than **extracted**. The process:

1. Start with the stack choices documented in the technical doc
2. Apply standard community conventions for that stack (e.g., TypeScript projects use camelCase, Python projects use snake_case)
3. Layer on any explicit conventions mentioned in the docs (e.g., "we use Conventional Commits")
4. Fill remaining gaps with sensible defaults

Mark the source of each convention:

| Source | Format |
|--------|--------|
| Observed in code | State as fact: "Use camelCase for variables" |
| Explicitly documented | State as fact with attribution: "Use Conventional Commits (per technical doc)" |
| Derived from stack | `> **Derived from stack choice ({{stack}}).** Adjust if the team has different preferences.` |
| No basis at all | `> **No established convention.** Recommended: {{suggestion with rationale}}.` |

For documentation-first projects, most conventions will be "derived from stack" — this is fine. The rules still provide value because they give every agent a consistent starting point.

## Convention Categories

### Code Style

- Naming: files, directories, variables, functions, classes, constants, types/interfaces
- Formatting: indentation, line length, braces, quotes, semicolons, trailing commas
- Import ordering and grouping
- Export style: named vs default
- Comment style and documentation patterns

### Architecture

- Directory structure and module organization
- Design patterns (MVC, service layer, repository, etc.)
- State management approach
- Configuration management (env vars, config files, constants)
- Dependency injection or initialization patterns
- Error handling strategy (error types, propagation, recovery)
- Observability approach (structured logging, metrics, tracing, health checks)
- API design patterns (REST conventions, versioning, schema validation)
- Backward compatibility strategy (deprecation process, versioning)

### Testing

- Framework and assertion library
- File naming and location conventions
- Test categories (unit, integration, e2e) and where each lives
- Mocking strategy (what gets mocked, how)
- Fixture and test data patterns
- Coverage tooling and thresholds

### Security

- Secrets management (how/where secrets are stored and accessed)
- Input validation approach
- Authentication and authorization patterns
- Data protection (encryption, sanitization, PII handling)

### Quality & Complexity

- Linter configuration and rules
- Formatter configuration
- Type checker configuration and strictness
- Pre-commit hooks
- Maximum function/file lengths (observed patterns)
- Nesting depth patterns
- Code review requirements

### Dependencies

- Version pinning strategy (exact, caret, tilde)
- Adding new dependencies (process, approval)
- Vulnerability scanning

## Convention Gap Format

When the project has no established convention for an area:

```markdown
### {{Area}}

> **No established convention.** Recommended: {{suggestion with rationale}}.
```

Mark these clearly — they are suggestions, not observed facts.

# Identify Findings

Categorize every issue found during analysis. Each finding becomes a task or gets folded into one.

## Documentation-First Projects

For new projects built from feature/technical docs, "findings" are **work items to build** rather than issues to fix. Map documented requirements to the same priority tiers:

- **Critical:** Core infrastructure that everything else depends on — project init, database setup, auth system, CI/CD pipeline
- **High:** Core features from the MVP/Phase 1 of the feature doc — the features without which the product doesn't work
- **Medium:** Secondary features, nice-to-haves, or Phase 2 items from the feature doc
- **Low:** Polish, optimization, documentation, monitoring setup

Each feature from the feature doc should become one or more tasks. Split large features (e.g., "user management") into concrete implementation tasks (e.g., "user registration endpoint", "password reset flow", "role-based access control").

## Existing Codebases

## Critical — Security & Correctness

- Authentication or authorization bypasses
- SQL injection, XSS, CSRF, or other injection vulnerabilities
- Exposed secrets (API keys, passwords, tokens in source code)
- Data corruption or loss scenarios
- Race conditions or concurrency bugs on shared mutable state
- Broken core functionality
- Breaking changes to public APIs or contracts without versioning

## High — Validation & Reliability

- Missing input validation
- Missing error handling (empty catch blocks, unhandled promises/exceptions)
- Unstructured or inconsistent error types (no standard error hierarchy)
- Missing retry logic or graceful degradation for external service calls
- Incorrect or incomplete business logic
- Missing boundary checks
- Inconsistent state management
- Excessive complexity (deeply nested, too many branches, too long)
- Missing type safety or type errors
- Missing configuration validation at startup
- Database migrations without rollback path

## Medium — Features & Improvements

- Missing features that are expected or documented
- Hardcoded values that should be configurable
- Performance bottlenecks (no baselines or benchmarks)
- Poor test coverage (files or modules with no tests)
- Tight coupling between components
- Test bypasses or environment-specific hacks in production code
- Missing or disabled linter rules
- Excessively long functions or files (not breaking, but unmaintainable)
- Missing documentation on public APIs or complex logic
- Missing formatter, linter, or type checker setup
- Missing structured logging or observability
- API endpoints without schema validation or contract tests
- Missing backward compatibility strategy
- Non-idempotent operations that should be idempotent
- Dev/staging/production environment inconsistencies

## Low — Cleanup & Polish

- Dead code (unused functions, variables, imports, config)
- Inconsistent naming or formatting
- Missing or scattered logging
- Documentation gaps
- Dependency updates
- Dependencies with incompatible or unknown licenses
- Suppressed linter warnings without justification
- Inconsistent formatting that a formatter would fix
- Stale or redundant comments
- Missing deprecation notices on superseded interfaces

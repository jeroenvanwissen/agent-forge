# Changelog

All notable changes to Agent Forge will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/).

---

## [1.0.0] — 2025-02-18

### Added

**Core Analysis Framework**
- 4-phase analysis process: Deep Analysis → Prioritize & Group → Generate Output → Quality Check
- Priority-ordered wave system (A through F) with dependency mapping
- T-shirt sizing (S/M/L/XL) with automatic XL splitting
- Mermaid dependency graph generation

**Quality Enforcement**
- Code complexity limits (function length, file length, nesting depth, parameter count, cyclomatic complexity)
- Zero new linter warnings policy
- Formatter enforcement (no unformatted code)
- Type checker enforcement (zero new errors)
- Test coverage no-decrease policy with per-PR tracking
- Cognitive complexity reduction patterns with code examples
- PR size guardrails (max 8 files, max 500 lines)

**Testing Standards**
- Required tests by task type (bug fix → regression test, feature → happy/error/boundary, security fix → exploit test)
- Test quality rules (one behavior per test, deterministic, isolated, no flaky tests)
- Test categories (unit, integration, e2e) with location conventions
- Coverage baseline tracking and new code coverage targets

**Task Management**
- Per-task files with goal, current state, scope tree, implementation steps, testing requirements, acceptance criteria, risk/rollback, verification commands
- Quality Baseline table in TASKS.md (coverage, linter counts, type errors, complexity hotspots, dependency vulns)
- PR review checklist (correctness, quality, testing, standards, documentation, security)

**Convention Extraction**
- Code style rules (formatter, linter, type checker, naming, imports, documentation comments)
- Project structure rules (directory layout, module organization, dependency hygiene)
- Security rules (secrets, input validation, auth, data protection)
- Quality rules (complexity limits, cognitive load reduction, hotspot tracking, linter suppression policy)
- Convention gap detection ("No established convention — recommended:" format)

**Additional Rule Templates (8 optional domains)**
- `api.md` — REST/GraphQL conventions, versioning, schema validation, contract testing, idempotency
- `database.md` — Schema naming, migration safety, query patterns, N+1 prevention
- `deployment.md` — CI/CD pipelines, containerization, release process, environment parity
- `git.md` — Branching strategy, conventional commits, PR process, merge strategy
- `observability.md` — Structured logging, metrics, tracing, health checks, alerting
- `performance.md` — Baselines, benchmarks, no-regression policy, bundle/query/timeout budgets
- `accessibility.md` — WCAG compliance, semantic HTML, keyboard nav, ARIA, screen reader testing
- `i18n.md` — Translation files, string rules, locale-aware formatting, RTL, pseudo-localization

**Documentation**
- Implementation guide with step-by-step setup
- Re-analysis triggers (when to re-run)
- Troubleshooting guide
- Example output structure

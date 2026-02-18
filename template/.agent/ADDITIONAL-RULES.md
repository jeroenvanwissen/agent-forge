# Additional Rules Reference

> Detailed templates for optional `.claude/rules/` files. Only create these if the project has established conventions for the domain. Do not generate empty or speculative rules.

---

## When to Create Additional Rule Files

| File                     | Create when...                                              | Skip when...                                   |
| ------------------------ | ----------------------------------------------------------- | ---------------------------------------------- |
| `rules/api.md`           | Project exposes or consumes HTTP APIs, GraphQL, gRPC, etc.  | No API layer exists                            |
| `rules/database.md`      | Project uses any database (SQL, NoSQL, ORM, raw queries)    | No database interaction                        |
| `rules/deployment.md`    | Project has CI/CD, Docker, cloud infra, or release process  | Single-developer local-only project            |
| `rules/git.md`           | Team has specific conventions beyond standard git flow      | Standard branch/merge workflow is sufficient   |
| `rules/observability.md` | Project has logging, metrics, tracing, or alerting setup    | CLI tool or library with no runtime monitoring |
| `rules/performance.md`   | Project has benchmarks, SLAs, bundle budgets, or perf tests | Performance is not a concern for this project  |
| `rules/accessibility.md` | Project has a user-facing frontend                          | Backend-only, CLI, or library project          |
| `rules/i18n.md`          | Project supports or plans to support multiple locales       | Single-language project with no i18n needs     |

---

## rules/api.md

API design conventions, contracts, versioning, and validation.

### Template

````markdown
# API Conventions

## Design Style

- **API style:** {{REST / GraphQL / gRPC / RPC / hybrid}}
- **Specification:** {{OpenAPI/Swagger version, GraphQL schema location, protobuf location, or "none"}}
- **Spec location:** `{{path to spec file}}`

## URL & Naming Conventions (REST)

- Base path: {{e.g., `/api/v1/`}}
- Resource naming: {{plural nouns, lowercase, hyphen-separated — e.g., `/api/v1/user-accounts`}}
- Nested resources: {{e.g., `/users/{id}/orders` — max nesting depth}}
- Query parameters: {{camelCase, snake_case, or kebab-case}}
- Action endpoints (non-CRUD): {{e.g., `POST /orders/{id}/cancel` vs `POST /orders/{id}/actions/cancel`}}

## HTTP Methods & Status Codes

| Action    | Method   | Success Code | Error Codes              |
| --------- | -------- | ------------ | ------------------------ |
| List      | `GET`    | `200`        | `400`, `401`, `403`      |
| Get one   | `GET`    | `200`        | `401`, `403`, `404`      |
| Create    | `POST`   | `201`        | `400`, `401`, `403`, `409` |
| Update    | `PUT/PATCH` | `200`     | `400`, `401`, `403`, `404` |
| Delete    | `DELETE` | `204`        | `401`, `403`, `404`      |

## Request & Response Format

- **Content type:** {{application/json, etc.}}
- **Date format:** {{ISO 8601, Unix timestamp}}
- **Pagination:** {{cursor-based, offset-based — include pattern}}
- **Envelope:** {{e.g., `{ "data": [...], "meta": { "total": 100 } }` or flat}}
- **Error format:**

```json
{{project's actual error response format, e.g.:}}
{
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Human-readable description",
    "details": [{ "field": "email", "reason": "required" }]
  }
}
```

## Versioning

- **Strategy:** {{URL path `/v1/`, header `Accept: application/vnd.api+json;version=1`, or query param}}
- **Breaking change policy:** {{what constitutes a breaking change}}
- **Deprecation process:** {{how old versions are deprecated and sunset}}

## Schema Validation

- **Validation library:** {{e.g., Zod, Joi, JSON Schema, pydantic}}
- **Where validation runs:** {{middleware, controller, service layer}}
- **Request validation:** {{all endpoints validate input — describe pattern}}
- **Response validation:** {{optional — whether responses are also validated against schema}}

## Contract Testing

- **Tool:** {{e.g., Pact, Dredd, Schemathesis, or "none"}}
- **Contract location:** {{where contract tests live}}
- **When contracts run:** {{CI, pre-merge, or manual}}

## Authentication & Authorization

- **Auth mechanism:** {{Bearer token, API key, OAuth 2.0, session cookie}}
- **Where auth is checked:** {{middleware, decorator, guard}}
- **Public endpoints:** {{how unauthenticated endpoints are marked}}
- **Rate limiting:** {{per-endpoint, per-user, global — and how it's configured}}

## Backward Compatibility Rules

- Never remove a field from a response without deprecation period
- Never change a field's type in a response
- Never make an optional request field required
- New fields must have default values
- {{Project-specific compatibility rules}}

## Idempotency

- **Idempotent operations:** {{which endpoints must be idempotent}}
- **Idempotency key:** {{e.g., `Idempotency-Key` header for POST requests}}
- **Implementation:** {{how idempotency is enforced — dedup table, cache, etc.}}

## Do / Don't

| Do                                          | Don't                                          |
| ------------------------------------------- | ---------------------------------------------- |
| Return consistent error format on all endpoints | Return different error shapes per endpoint   |
| Validate all inputs at the API boundary     | Trust client input without validation          |
| Version breaking changes                    | Make breaking changes without a new version    |
| Use plural nouns for resources              | Use verbs in URLs (except for actions)         |
| Document all endpoints in the spec file     | Add endpoints without updating the spec        |
| Return appropriate HTTP status codes        | Return `200` for everything                    |
````

---

## rules/database.md

Database conventions, queries, migrations, and data modeling.

### Template

````markdown
# Database Conventions

## Technology

- **Database:** {{PostgreSQL, MySQL, MongoDB, SQLite, etc.}}
- **ORM / Query builder:** {{e.g., Prisma, SQLAlchemy, Sequelize, TypeORM, Drizzle, or "raw queries"}}
- **Schema location:** {{path to schema files, e.g., `prisma/schema.prisma`}}
- **Migration tool:** {{e.g., built-in ORM migrations, Flyway, Alembic, knex}}
- **Migration location:** `{{path to migrations directory}}`

## Schema Conventions

### Table / Collection Naming

- {{Casing: snake_case, PascalCase, etc.}}
- {{Plurality: plural (`users`) or singular (`user`)}}
- {{Join table naming: e.g., `user_roles` or alphabetical `roles_users`}}

### Column / Field Naming

- {{Casing: snake_case, camelCase}}
- {{Primary key convention: `id`, `uuid`, `{table}_id`}}
- {{Foreign key convention: `{referenced_table}_id`}}
- {{Timestamp columns: `created_at`, `updated_at`, `deleted_at`}}
- {{Boolean columns: `is_active`, `has_verified`}}

### Indexes

- {{When to add indexes (foreign keys, frequently queried fields, unique constraints)}}
- {{Naming convention: e.g., `idx_{table}_{column}`}}
- {{Composite index conventions}}

## Migrations

### Creating Migrations

- **Command:** `{{command to create a new migration}}`
- **Naming:** {{e.g., `YYYYMMDDHHMMSS_description.sql` or auto-generated}}
- Every migration must be **reversible** — include both up and down steps
- One logical change per migration (don't mix schema and data changes)

### Migration Safety Rules

- Never drop a column or table in a single migration — deprecate first, then remove
- Add new columns as nullable or with defaults — never add a required column without a default
- For large tables, consider: will this lock the table? Use online schema change tools if needed
- Always test migrations against a copy of production data size
- **Rollback plan:** Every migration must have a tested rollback. Document rollback steps in the migration file

### Running Migrations

- **Migrate up:** `{{command}}`
- **Migrate down / rollback:** `{{command}}`
- **Check status:** `{{command}}`
- **When:** Migrations run {{automatically on deploy / manually before deploy / as part of CI}}

## Query Patterns

- {{ORM vs raw queries: when to use each}}
- {{Parameterized queries: always use parameterized queries, never string interpolation}}
- {{N+1 query prevention: eager loading, joins, batching}}
- {{Transaction usage: when to use transactions, isolation levels}}
- {{Connection pooling: how connections are managed}}

## Data Integrity

- {{Soft delete vs hard delete: which pattern is used}}
- {{Referential integrity: foreign key constraints enforced at DB level}}
- {{Data validation: validated at app level, DB level, or both}}

## Do / Don't

| Do                                       | Don't                                          |
| ---------------------------------------- | ---------------------------------------------- |
| Use parameterized queries                | Interpolate user input into SQL                |
| Add indexes for foreign keys             | Leave foreign keys unindexed                   |
| Make migrations reversible               | Write one-way migrations                       |
| Test migrations against production-size data | Only test migrations on empty databases     |
| Use transactions for multi-step writes   | Assume individual queries are atomic           |
````

---

## rules/deployment.md

CI/CD, containerization, infrastructure, and release process.

### Template

````markdown
# Deployment Conventions

## Environments

| Environment | Purpose              | URL / Access          | Deploy Method      |
| ----------- | -------------------- | --------------------- | ------------------ |
| Local       | Development          | `localhost:{{port}}`  | {{manual / docker}} |
| Staging     | Pre-production testing | {{URL}}             | {{auto / manual}}  |
| Production  | Live                 | {{URL}}               | {{auto / manual}}  |

## CI/CD Pipeline

- **Platform:** {{GitHub Actions, GitLab CI, Jenkins, CircleCI, etc.}}
- **Config location:** `{{path, e.g., .github/workflows/}}`
- **Pipeline stages:**
  1. {{Lint & format check}}
  2. {{Type check}}
  3. {{Unit tests}}
  4. {{Integration tests}}
  5. {{Build}}
  6. {{Deploy to staging}}
  7. {{E2E tests on staging}}
  8. {{Deploy to production}}

## Containerization (if applicable)

- **Dockerfile location:** `{{path}}`
- **Docker Compose:** `{{path}}`
- **Build command:** `{{command}}`
- **Run command:** `{{command}}`
- **Base image:** {{e.g., `node:20-alpine`}}
- **Image naming:** {{e.g., `project-name:git-sha`}}

## Release Process

- **Version strategy:** {{semver, calver, or "not versioned"}}
- **Changelog:** {{how changelog is maintained — auto-generated, manual, conventional commits}}
- **Release command:** `{{command, if any}}`
- **Rollback process:** {{how to rollback a bad release}}

## Environment Variables

- **Where defined:** {{.env files, CI secrets, cloud secret manager}}
- **Required vars:** {{list or reference to env template file}}
- **Template file:** `{{path, e.g., .env.example}}`
- **Validation:** {{how missing env vars are caught at startup}}

## Environment Parity

- All environments must use the same {{database engine, runtime version, etc.}}
- Feature flags are the mechanism for environment-specific behavior — not code branches
- {{Project-specific parity rules}}

## Do / Don't

| Do                                          | Don't                                     |
| ------------------------------------------- | ----------------------------------------- |
| Use the same Docker image across all envs   | Build different images per environment    |
| Keep env-specific config in environment vars | Hardcode environment-specific values      |
| Test migrations on staging before production | Run untested migrations in production     |
| Use health checks for readiness/liveness    | Deploy without health check endpoints     |
````

---

## rules/git.md

Git workflow, branching, commit messages, and PR conventions.

### Template

```markdown
# Git Conventions

## Branching Strategy

- **Primary branch:** `{{main / master}}`
- **Branch protection:** {{required reviews, CI checks, etc.}}
- **Branch naming:** `{{prefix}}/{{task-id}}-{{short-description}}`
- **Long-lived branches:** {{release branches, develop branch — or "none, trunk-based"}}
- **Branch lifetime:** Delete after merge. No long-lived feature branches.

## Commit Messages

### Format

```
<type>(<scope>): <subject>

<body>

<footer>
```

### Types

| Type       | When to Use                              |
| ---------- | ---------------------------------------- |
| `feat`     | New feature                              |
| `fix`      | Bug fix                                  |
| `refactor` | Code change that neither fixes nor adds  |
| `test`     | Adding or updating tests                 |
| `chore`    | Maintenance, deps, config                |
| `docs`     | Documentation only                       |
| `perf`     | Performance improvement                  |
| `ci`       | CI/CD configuration changes              |

### Rules

- Subject line: imperative mood, no period, ≤ 72 characters
- Body: explain "why" not "what" (the diff shows "what")
- Reference task ID: include `Task <ID> from .agent/TASKS.md` in body
- Breaking changes: add `BREAKING CHANGE:` footer

## Pull Requests

### PR Title

`<type>(<scope>): brief description`

### PR Body Template

{{Include the PR body template from .agent/README.md or customize it here}}

### Review Process

- {{Minimum number of approvals required}}
- {{Who can approve (team members, specific code owners)}}
- {{Auto-merge policy: allowed or not}}
- {{Stale review policy: re-review after force push or not}}

### Merge Strategy

- **Method:** {{squash merge, merge commit, rebase — and when to use each}}
- **Squash policy:** {{always squash, or merge commits for multi-commit PRs}}
- **After merge:** Delete the source branch

## Tags & Releases

- **Tag format:** {{e.g., `v1.2.3`, semver}}
- **When to tag:** {{every release, every deploy, manually}}
- **Release notes:** {{auto-generated from commits, manual, or both}}

## Do / Don't

| Do                                      | Don't                                    |
| --------------------------------------- | ---------------------------------------- |
| Write atomic commits (one change each)  | Mix unrelated changes in one commit      |
| Use conventional commit format          | Write vague messages like "fix stuff"    |
| Rebase feature branches on main         | Merge main into feature branches         |
| Delete branches after merge             | Leave stale branches lying around        |
```

---

## rules/observability.md

Logging, metrics, tracing, health checks, and monitoring.

### Template

````markdown
# Observability Conventions

## Logging

### Framework

- **Logger:** {{e.g., Winston, Pino, Python logging, slog, Serilog}}
- **Config location:** `{{path}}`
- **Format:** {{structured JSON, plaintext, or configurable per environment}}

### Log Levels

| Level   | When to Use                                                    |
| ------- | -------------------------------------------------------------- |
| `error` | Something failed and needs attention (include error + context) |
| `warn`  | Unexpected but recoverable (degraded state, fallback used)     |
| `info`  | Significant business events (request handled, job completed)   |
| `debug` | Detailed internal state (only useful during development)       |

### Structured Logging Rules

- Always log as structured data (key-value pairs), not interpolated strings
- Include context fields: `requestId`, `userId`, `operation`, `duration`
- **Never log:** passwords, tokens, API keys, PII, credit card numbers, session IDs
- **Always log:** request method + path, response status, error messages, operation duration

### Example

```{{language}}
{{example of a well-structured log call in the project's actual format}}
```

## Metrics (if applicable)

- **Tool:** {{Prometheus, Datadog, CloudWatch, StatsD, or "none"}}
- **Key metrics tracked:**
  - {{Request rate, error rate, latency (RED metrics)}}
  - {{Queue depth, processing time (for async workers)}}
  - {{Custom business metrics}}
- **Naming convention:** {{e.g., `app_http_requests_total`, snake_case with unit suffix}}

## Tracing (if applicable)

- **Tool:** {{OpenTelemetry, Jaeger, Datadog APM, or "none"}}
- **Instrumentation:** {{automatic, manual, or hybrid}}
- **Trace context propagation:** {{how trace IDs flow across services}}

## Health Checks

- **Liveness endpoint:** `{{e.g., GET /health/live}}`
- **Readiness endpoint:** `{{e.g., GET /health/ready}}`
- **What readiness checks:** {{database connectivity, external service availability, etc.}}

## Alerting (if applicable)

- **Tool:** {{PagerDuty, Opsgenie, Slack alerts, or "none"}}
- **Alert conditions:** {{error rate > X%, latency > Y ms, health check failing}}

## Do / Don't

| Do                                        | Don't                                     |
| ----------------------------------------- | ----------------------------------------- |
| Use structured logging (JSON key-value)   | Log with string interpolation             |
| Include request context in all log entries | Log without correlation IDs               |
| Log at the appropriate level              | Log everything at `info` or `error`       |
| Add health check endpoints                | Deploy without any health monitoring      |
| Scrub sensitive data before logging       | Log tokens, passwords, or PII            |
````

---

## rules/performance.md

Performance baselines, budgets, benchmarks, and optimization patterns.

### Template

````markdown
# Performance Conventions

## Baselines

| Metric                     | Current    | Budget / Target    | How to Measure           |
| -------------------------- | ---------- | ------------------ | ------------------------ |
| {{e.g., API p95 latency}}  | {{N ms}}   | {{target ms}}      | `{{benchmark command}}`  |
| {{e.g., Bundle size}}      | {{N KB}}   | {{target KB}}      | `{{build + size check}}` |
| {{e.g., Startup time}}     | {{N s}}    | {{target s}}       | `{{measurement method}}` |
| {{e.g., Memory usage}}     | {{N MB}}   | {{target MB}}      | `{{measurement method}}` |

## Benchmark Suite

- **Tool:** {{e.g., k6, wrk, Benchmark.js, pytest-benchmark, or "none"}}
- **Location:** `{{path to benchmarks}}`
- **Run command:** `{{command}}`
- **When to run:** {{before/after performance-related PRs, in CI, manually}}

## Performance Rules

- **No-regression policy:** Performance must not degrade with any PR that claims not to affect performance. If a PR is expected to impact performance, include benchmark results in the PR.
- **Bundle budget (frontend):** Maximum bundle size is {{N KB}}. Exceeding this requires justification and approval.
- **Query budget (backend):** Maximum {{N}} database queries per request. Use eager loading to avoid N+1.
- **Timeout budget:** External service calls must have timeouts of {{N}} seconds.

## Optimization Patterns

- {{Caching strategy: what is cached, where (memory, Redis, CDN), invalidation approach}}
- {{Lazy loading: what is loaded lazily and why}}
- {{Pagination: required for all list endpoints returning more than {{N}} items}}
- {{Connection pooling: how database and HTTP connections are pooled}}
- {{Async processing: what work is deferred to background jobs/queues}}

## Do / Don't

| Do                                        | Don't                                      |
| ----------------------------------------- | ------------------------------------------ |
| Measure before optimizing                 | Optimize without profiling first           |
| Set timeouts on all external calls        | Let external calls hang indefinitely       |
| Paginate large result sets                | Return unbounded lists                     |
| Use caching for expensive computations    | Cache without an invalidation strategy     |
| Run benchmarks for performance-sensitive PRs | Merge perf changes without measurement  |
````

---

## rules/accessibility.md

Accessibility requirements for frontend projects (WCAG compliance, semantic HTML, assistive technology).

### Template

```markdown
# Accessibility Conventions

## Standard

- **Target compliance:** {{WCAG 2.1 AA, WCAG 2.2 AA, Section 508, or "best effort"}}
- **Audit tool:** {{axe-core, Lighthouse, pa11y, WAVE, or "manual"}}
- **Audit command:** `{{command}}`
- **When to audit:** {{every PR with UI changes, weekly, manually}}

## Semantic HTML

- Use semantic elements (`<nav>`, `<main>`, `<article>`, `<section>`, `<aside>`, `<header>`, `<footer>`) instead of generic `<div>` containers
- Every page must have exactly one `<h1>`
- Heading levels must not skip (no `<h1>` → `<h3>`)
- Use `<button>` for actions and `<a>` for navigation — never use `<div>` or `<span>` as interactive elements
- All form inputs must have associated `<label>` elements

## Images & Media

- Every `<img>` must have an `alt` attribute (empty `alt=""` for decorative images)
- Complex images (charts, diagrams) need detailed descriptions
- Videos must have captions or transcripts
- Do not use images of text — use real text

## Keyboard Navigation

- All interactive elements must be reachable via `Tab` key
- Focus order must follow visual order
- Focus must be visible (never `outline: none` without a replacement)
- Modal dialogs must trap focus and return focus on close
- Custom components must implement appropriate keyboard patterns (arrow keys for menus, Escape to close)

## ARIA

- Prefer semantic HTML over ARIA attributes — ARIA is a last resort
- If ARIA is needed, use correct roles and states
- Dynamic content updates must use `aria-live` regions
- Custom components must have appropriate `role`, `aria-label`, and state attributes

## Color & Contrast

- Text must have minimum contrast ratio of {{4.5:1 for normal text, 3:1 for large text}}
- Do not convey information through color alone — use text, icons, or patterns as well
- Support high contrast / dark mode if applicable

## Testing

- **Automated testing:** Run axe-core or equivalent in CI for every UI PR
- **Manual testing:** Tab through all interactive elements, verify with screen reader
- **Screen readers tested:** {{VoiceOver, NVDA, JAWS, or "not tested"}}

## Do / Don't

| Do                                        | Don't                                    |
| ----------------------------------------- | ---------------------------------------- |
| Use semantic HTML elements                | Use divs and spans for everything        |
| Provide alt text for all images           | Leave images without alt attributes      |
| Ensure keyboard navigation works          | Require mouse for essential interactions |
| Test with a screen reader                 | Rely only on automated tools             |
| Maintain visible focus indicators         | Remove focus outlines without replacement|
```

---

## rules/i18n.md

Internationalization and localization conventions.

### Template

````markdown
# Internationalization Conventions

## Framework

- **i18n library:** {{e.g., react-intl, i18next, gettext, ICU, or "custom"}}
- **Config location:** `{{path}}`
- **Default locale:** {{e.g., `en-US`}}
- **Supported locales:** {{e.g., `en-US`, `es-ES`, `ja-JP`, `de-DE`}}

## Translation Files

- **Location:** `{{path, e.g., `src/locales/` or `public/locales/`}}`
- **Format:** {{JSON, YAML, PO, XLIFF}}
- **File naming:** {{e.g., `{locale}.json`, `{namespace}/{locale}.json`}}
- **Key naming:** {{e.g., dot-separated `page.section.label`, or flat `pageSection_label`}}

## String Rules

- **Never hardcode user-facing strings** — always use the i18n framework
- Use ICU message format (or equivalent) for plurals, gender, and interpolation
- Keys should describe the meaning, not the English text: `userGreeting` not `hello_user`
- Include context comments for translators when meaning is ambiguous

### Example

```{{language}}
{{example of a correctly internationalized string in the project's actual format}}
```

## Formatting

- **Dates:** Use locale-aware formatters (never hardcode `MM/DD/YYYY`)
- **Numbers:** Use locale-aware formatters for decimals, thousands separators, currency
- **Currency:** Always include currency code — never assume a symbol means one currency
- **Time zones:** Store in UTC, display in user's local time zone

## Text & Layout

- Support right-to-left (RTL) layouts if any supported locale requires it
- Do not assume text length — translated strings can be 30-200% longer than English
- Do not embed text in images — use CSS or overlay text instead
- Do not split sentences across multiple keys — translators need full sentences for context

## Testing

- **Pseudo-localization:** {{tool or technique used to test i18n without real translations}}
- **Missing key detection:** {{how missing translation keys are caught — build error, runtime warning, CI check}}
- **Visual testing:** {{how layout is verified for longer/shorter translations}}

## Do / Don't

| Do                                       | Don't                                    |
| ---------------------------------------- | ---------------------------------------- |
| Use the i18n framework for all UI strings | Hardcode user-facing strings             |
| Use ICU format for plurals               | Concatenate strings to build sentences   |
| Format dates/numbers with locale-aware APIs | Hardcode date formats like `MM/DD/YYYY` |
| Provide translator context for ambiguous keys | Use cryptic abbreviation keys         |
| Test with pseudo-localization            | Only test with English                   |
````

---

## How to Use These Templates

1. **During Phase 1.3** of ANALYZE.md, identify which of these domains the project has conventions for
2. **During Phase 3.5**, generate only the rule files where the project has established patterns
3. **Use the "No established convention" format** for areas within a file where the project has gaps:

```markdown
### {{Area}}

> **No established convention.** Recommended: {{suggestion with rationale}}.
> This is a suggestion, not an observed pattern. Adopt it if the team agrees.
```

4. **Link new rule files** in `.claude/CLAUDE.md`'s Rules table
5. **Follow the quality checklist** in ANALYZE.md Phase 4 — additional rule files should pass the same standards (imperative voice, concrete examples, based on actual conventions)

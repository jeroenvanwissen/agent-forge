# TaskFlow API

> REST API for task management built with Express, TypeScript, and Prisma.

## Quick Reference

| Action       | Command                            |
| ------------ | ---------------------------------- |
| Build        | `npm run build`                    |
| Test         | `npm test`                         |
| Lint         | `npx eslint src/`                  |
| Format       | `npx prettier --write src/`        |
| Format check | `npx prettier --check src/`        |
| Type check   | `npx tsc --noEmit`                 |
| Coverage     | `npm run test:coverage`            |
| Run          | `npm start`                        |
| Dev mode     | `npm run dev`                      |

## Project Structure

```
src/
├── config/          # Environment and app configuration
├── middleware/       # Express middleware (auth, error handling)
├── routes/          # Route handlers grouped by resource
├── services/        # Business logic layer
├── schemas/         # Zod validation schemas (after B1)
├── utils/           # Shared utility functions
└── index.ts         # App entry point
prisma/
├── schema.prisma    # Database schema
└── migrations/      # Database migrations
```

## Architecture

TaskFlow is a 3-layer Express API: routes handle HTTP concerns, services contain business logic, and Prisma handles database access. Authentication uses JWT tokens verified via middleware. All routes are prefixed with `/api/v1/`.

## Key Decisions

- Prisma over raw SQL — type-safe queries, auto-generated migrations
- JWT over sessions — stateless auth for horizontal scaling
- Zod for request validation — runtime type checking that mirrors TypeScript types
- Jest over Vitest — team familiarity, mature ecosystem

## Quality Standards

Before every commit, run:

```bash
npx prettier --write src/    # Auto-fix formatting
npx eslint src/              # Must pass with zero warnings
npx tsc --noEmit             # Must pass with zero errors
npm run build                # Must compile
npm test                     # Must pass all tests
npm run test:coverage        # Must not decrease coverage
```

Key limits (see `.claude/rules/quality.md` for details):

- Max function length: 50 lines
- Max file length: 300 lines
- Max nesting depth: 4 levels
- Max function parameters: 4

## Rules

Detailed conventions are in `.claude/rules/`:

| File                                       | Covers                                       |
| ------------------------------------------ | -------------------------------------------- |
| [rules/project.md](rules/project.md)       | Project structure and architecture            |
| [rules/code-style.md](rules/code-style.md) | Code style and formatting                     |
| [rules/testing.md](rules/testing.md)       | Testing conventions and requirements          |
| [rules/security.md](rules/security.md)     | Security requirements and practices           |
| [rules/quality.md](rules/quality.md)       | Code quality thresholds and complexity limits  |

## Documentation Maintenance

When making changes to the codebase, update all affected documentation files to stay in sync:

| File                                       | Update when...                                     |
| ------------------------------------------ | -------------------------------------------------- |
| [README.md](../README.md)                  | Features, architecture, config, or usage changes   |
| [.agent/TASKS.md](../.agent/TASKS.md)      | Task status changes (started, completed, blocked)  |
| [.agent/README.md](../.agent/README.md)    | Workflow or tooling changes                        |
| [.claude/CLAUDE.md](./)                    | Architecture, key decisions, or structure changes  |
| [rules/code-style.md](rules/code-style.md) | New patterns, naming conventions, or style changes |
| [rules/project.md](rules/project.md)       | New files, directories, or dependency changes      |
| [rules/security.md](rules/security.md)     | Security-relevant changes or new validations       |
| [rules/testing.md](rules/testing.md)       | New test files, patterns, or framework changes     |
| [rules/quality.md](rules/quality.md)       | Quality thresholds or tooling changes              |

## Tasks

See [.agent/TASKS.md](../.agent/TASKS.md) for the project backlog and task details.

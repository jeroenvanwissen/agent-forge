# Project Guidelines

## Directory Structure

```
src/
├── config/          # App config and environment loading
├── middleware/       # Express middleware (auth, validation, errors)
├── routes/          # Route handlers — one file per resource
├── services/        # Business logic — one file per domain
├── schemas/         # Zod validation schemas — one file per resource
├── utils/           # Pure utility functions (no side effects)
└── index.ts         # App entry point — creates and starts server
prisma/
├── schema.prisma    # Single source of truth for DB schema
└── migrations/      # Auto-generated, never hand-edit
```

## Module Organization

- Place new route files in `src/routes/` named `{resource}.ts`
- Place business logic in `src/services/` — routes must not contain business logic
- Place validation schemas in `src/schemas/` — one file per resource matching route file name
- Shared utilities go in `src/utils/` — must be pure functions with no side effects
- Middleware goes in `src/middleware/` — one file per concern

## Naming Conventions

### Files

- Source files: `camelCase.ts` (e.g., `taskService.ts`)
- Test files: `{source}.test.ts` alongside source (e.g., `taskService.test.ts`)
- Route files: plural resource name (e.g., `tasks.ts`, `users.ts`)

### Directories

- All lowercase, singular concept (e.g., `middleware/`, `service/`, `route/`)

## Configuration

- All config values in `src/config/env.ts` — never read `process.env` directly elsewhere
- Use `z.object()` to validate env vars at startup — fail fast on missing config
- Prefix custom env vars with `TASKFLOW_` (e.g., `TASKFLOW_JWT_SECRET`)

## Dependencies

- Use exact versions in `package.json` (no `^` or `~`)
- Run `npm audit` before adding any new dependency
- Prefer well-maintained packages with TypeScript types included

## Do / Don't

| Do                                      | Don't                                    |
| --------------------------------------- | ---------------------------------------- |
| Read env vars through `src/config/env.ts` | Read `process.env` directly in services |
| Put business logic in services          | Put business logic in route handlers     |
| One route file per resource             | Mix multiple resources in one route file |

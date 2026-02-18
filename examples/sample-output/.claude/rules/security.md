# Security Requirements

## Secrets Management

- All secrets in environment variables — never in source code or config files
- Access through `src/config/env.ts` — never read `process.env` directly
- `.env` files are in `.gitignore` — use `.env.example` as a template (no real values)
- Required secrets: `TASKFLOW_JWT_SECRET`, `DATABASE_URL`

## Input Validation

- Validate all request bodies at the API boundary using Zod schemas (see `src/schemas/`)
- Validate URL parameters (IDs must be valid UUID format)
- Sanitize user input before database queries — Prisma handles parameterization
- Never trust `Content-Type` headers — validate actual content

## Authentication & Authorization

- JWT Bearer tokens in `Authorization` header
- Tokens verified via `verifyToken` middleware using `jwt.verify()` (not `jwt.decode()`)
- Role-based access: `admin`, `manager`, `member`
- Authorization checked in route handlers after authentication middleware

## Data Protection

- Passwords hashed with bcrypt (cost factor 12)
- JWT tokens expire after 24 hours
- Never log: passwords, JWT tokens, API keys, email addresses, full names
- Database connections use TLS in production

## Dependencies

- Run `npm audit` weekly and before adding new dependencies
- Critical vulnerabilities must be patched within 24 hours
- High vulnerabilities within 1 week

## Do / Don't

| Do                                        | Don't                                   |
| ----------------------------------------- | --------------------------------------- |
| Use `jwt.verify()` with secret            | Use `jwt.decode()` (no verification)    |
| Validate all inputs with Zod schemas      | Pass `req.body` directly to Prisma      |
| Hash passwords with bcrypt                | Store passwords in plaintext            |
| Read secrets from `src/config/env.ts`     | Read `process.env.SECRET` in services   |
| Log request method and path               | Log request bodies or auth tokens       |

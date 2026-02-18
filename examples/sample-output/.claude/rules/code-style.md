# Code Style

## Automated Formatting

- **Formatter:** Prettier 3.2
- **Config file:** `.prettierrc`
- **Format command:** `npx prettier --write src/`
- **Check command:** `npx prettier --check src/`

Always run the formatter before committing. Never manually format code that Prettier handles.

## Linting

- **Linter:** ESLint 9.x with `@typescript-eslint`
- **Config file:** `eslint.config.mjs`
- **Lint command:** `npx eslint src/`
- **Auto-fix command:** `npx eslint src/ --fix`

Zero linter warnings policy: fix warnings rather than suppressing them.

## Type Checking

- **Type checker:** TypeScript 5.x (strict mode)
- **Config file:** `tsconfig.json`
- **Check command:** `npx tsc --noEmit`

All strict flags enabled. Never use `any` without a justifying comment.

## Formatting Rules

- Indentation: 2 spaces
- Line length: 100 characters
- Braces: same line (K&R style)
- Trailing commas: all (ES5+)
- Semicolons: always
- Quotes: single quotes for code, double quotes for JSX

## Naming

### Variables and Functions

- camelCase: `getUserById`, `taskCount`, `isValid`
- Boolean prefix: `is`, `has`, `can`, `should` (e.g., `isAdmin`, `hasPermission`)

### Types / Interfaces

- PascalCase: `UserPayload`, `TaskResponse`, `CreateTaskInput`
- No `I` prefix for interfaces

### Constants

- UPPER_SNAKE_CASE for true constants: `MAX_RETRY_COUNT`, `DEFAULT_PAGE_SIZE`
- Defined in relevant config or at module top

## Imports

- Order: node builtins → external packages → internal modules → relative imports
- Use path alias `@/` for `src/` (e.g., `import { verifyToken } from '@/middleware/auth'`)
- Named exports only — no default exports

## Documentation Comments

- Required on all exported functions, middleware, and service methods
- Use JSDoc format with `@param`, `@returns`, `@throws`
- Inline comments for "why" not "what"

## Patterns

### Error Handling

- Throw custom error classes extending `AppError` (defined in `src/utils/errors.ts`)
- Catch in the global error middleware — never swallow errors silently
- Always include original error as `cause` when wrapping

### Logging

- Use `pino` logger from `src/config/logger.ts`
- Levels: `error` (failures), `warn` (degraded), `info` (business events), `debug` (dev only)
- Never log tokens, passwords, or PII

### Async

- Always use async/await — no raw promises or callbacks
- Always wrap async route handlers with `asyncHandler()` utility

## Do / Don't

| Do                                       | Don't                                   |
| ---------------------------------------- | --------------------------------------- |
| `const isAdmin = user.role === 'admin'`  | `const flag = user.role === 'admin'`    |
| `throw new NotFoundError('Task')`        | `throw new Error('not found')`          |
| `import { x } from '@/services/task'`    | `import { x } from '../../../services'` |
| Use `asyncHandler()` on route handlers   | Use try-catch in every route handler    |

# Code Quality

## Complexity Limits

| Metric                    | Limit      | How to Fix                         |
| ------------------------- | ---------- | ---------------------------------- |
| Function/method length    | 50 lines   | Extract helper functions           |
| File length               | 300 lines  | Split into modules                 |
| Nesting depth             | 4 levels   | Use early returns, extract methods |
| Function parameters       | 4 params   | Use options/config object          |
| Cyclomatic complexity     | 10 per fn  | Simplify branching, extract logic  |

_These limits are based on observed patterns in the codebase. If existing code exceeds these limits, it is a refactoring candidate — new code must stay within limits._

## Reducing Cognitive Complexity

### Use early returns instead of nested conditionals

```typescript
// BAD — deeply nested
async function getTask(req: Request, res: Response) {
  const user = req.user;
  if (user) {
    const task = await prisma.task.findUnique({ where: { id: req.params.id } });
    if (task) {
      if (task.userId === user.id || user.role === 'admin') {
        res.json(task);
      } else {
        res.status(403).json({ error: 'Forbidden' });
      }
    } else {
      res.status(404).json({ error: 'Not found' });
    }
  } else {
    res.status(401).json({ error: 'Unauthorized' });
  }
}
```

```typescript
// GOOD — early returns, flat structure
async function getTask(req: Request, res: Response) {
  if (!req.user) return res.status(401).json({ error: 'Unauthorized' });

  const task = await prisma.task.findUnique({ where: { id: req.params.id } });
  if (!task) return res.status(404).json({ error: 'Not found' });

  const canAccess = task.userId === req.user.id || req.user.role === 'admin';
  if (!canAccess) return res.status(403).json({ error: 'Forbidden' });

  res.json(task);
}
```

### Extract complex conditions into named variables

```typescript
// BAD — complex inline condition
if (user.role === 'admin' || (user.role === 'manager' && project.teamId === user.teamId) || user.id === project.ownerId) {
```

```typescript
// GOOD — named boolean
const isAdmin = user.role === 'admin';
const isTeamManager = user.role === 'manager' && project.teamId === user.teamId;
const isProjectOwner = user.id === project.ownerId;
const canEditProject = isAdmin || isTeamManager || isProjectOwner;

if (canEditProject) {
```

## Automated Tooling

### Formatter

- **Tool:** Prettier 3.2
- **Config:** `.prettierrc`
- **Run:** `npx prettier --write src/`
- **Check:** `npx prettier --check src/`
- **When:** Before every commit. The formatter is the authority on formatting — never override it manually.

### Linter

- **Tool:** ESLint 9.x with `@typescript-eslint`
- **Config:** `eslint.config.mjs`
- **Run:** `npx eslint src/`
- **Auto-fix:** `npx eslint src/ --fix`
- **Policy:** Zero warnings. Fix issues rather than suppressing them.
- **Suppression:** If a rule must be disabled for a specific line, add a comment explaining the exact reason why. Generic suppressions like "too complex to fix" are not acceptable.

### Type Checker

- **Tool:** TypeScript 5.x (strict mode)
- **Config:** `tsconfig.json`
- **Run:** `npx tsc --noEmit`
- **Policy:** Zero errors. Use explicit types for function signatures and public APIs. Avoid `any` except with a justifying comment.

## Coverage Policy

- **Current baseline:** 62%
- **No-decrease rule:** Every PR must maintain or increase overall test coverage
- **New code target:** New files should have ≥ 80% line coverage
- **How to check:** `npm run test:coverage`

## Documentation Requirements

### Always document:

- Exported functions and middleware (purpose, parameters, return value, errors thrown)
- Service layer functions (business logic, side effects)
- Complex Prisma queries (what data is being fetched and why)
- Non-obvious error handling decisions

### Never document:

- Simple route handler delegations
- Prisma CRUD wrappers with obvious behavior
- Code that the function name already explains

### Comment format:

```typescript
/**
 * Validates that the authenticated user has access to the specified task.
 * Checks ownership and admin role. Throws ForbiddenError if access denied.
 *
 * @param userId - The authenticated user's ID
 * @param taskId - The task to check access for
 * @returns The task if access is granted
 * @throws {NotFoundError} If the task doesn't exist
 * @throws {ForbiddenError} If the user doesn't have access
 */
async function validateTaskAccess(userId: string, taskId: string): Promise<Task> {
```

## Hotspot Tracking

Known complexity hotspots in the codebase (update as refactoring progresses):

| File                     | Issue                              | Lines | Target Task |
| ------------------------ | ---------------------------------- | ----- | ----------- |
| `src/routes/tasks.ts`    | File too long, mixed concerns      | 487   | —           |
| `src/services/report.ts` | `generateReport()` too complex     | 142   | —           |
| `src/middleware/auth.ts`  | Error handling after A1 fix        | 45    | A1          |

## Do / Don't

| Do                                         | Don't                                     |
| ------------------------------------------ | ----------------------------------------- |
| Extract functions when logic gets complex   | Write functions longer than 50 lines      |
| Use early returns for guard clauses         | Nest conditionals deeper than 4 levels    |
| Name booleans clearly (isValid, hasAccess)  | Use cryptic abbreviations (flg, tmp2)     |
| Add "why" comments for non-obvious logic    | Add "what" comments restating the code    |
| Fix linter warnings when they appear        | Suppress warnings without explanation     |
| Run `npx prettier --write src/` before commit | Manually adjust formatting              |
| Write focused functions (one responsibility)| Write "god functions" that do everything  |

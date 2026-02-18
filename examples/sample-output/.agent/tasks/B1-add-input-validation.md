# Task B1: Add Request Input Validation

| Field            | Value                              |
| ---------------- | ---------------------------------- |
| **ID**           | `B1`                               |
| **Status**       | `[ ]`                              |
| **Priority**     | High                               |
| **Size**         | M                                  |
| **Dependencies** | A1                                 |
| **Branch**       | `fix/b1-add-input-validation`      |

**Goal:**
Add Zod schema validation to all API endpoints that accept request bodies. Currently, raw `req.body` is passed directly to Prisma queries without validation, allowing malformed data and potential injection.

**Current State:**

Endpoints pass `req.body` directly to the database layer without any validation:

```typescript
// src/routes/tasks.ts — lines 24-30
router.post('/', verifyToken, async (req: Request, res: Response) => {
  const task = await prisma.task.create({
    data: req.body,  // No validation — any shape passes through
  });
  res.status(201).json(task);
});
```

This affects 6 endpoints: `POST /tasks`, `PUT /tasks/:id`, `POST /users`, `PUT /users/:id`, `POST /projects`, `PUT /projects/:id`.

**Scope:**

```
src/
├── schemas/                         # CREATE — new directory
│   ├── task.schema.ts               # CREATE — Zod schemas for task endpoints
│   ├── user.schema.ts               # CREATE — Zod schemas for user endpoints
│   └── project.schema.ts            # CREATE — Zod schemas for project endpoints
├── middleware/validate.ts            # CREATE — reusable validation middleware
├── routes/tasks.ts                  # MODIFY — add validation middleware
├── routes/users.ts                  # MODIFY — add validation middleware
├── routes/projects.ts               # MODIFY — add validation middleware
└── schemas/__tests__/
    ├── task.schema.test.ts          # CREATE — schema unit tests
    ├── user.schema.test.ts          # CREATE — schema unit tests
    └── validate.test.ts             # CREATE — middleware tests
```

**Implementation:**

1. Install Zod: `npm install zod`
2. Create `src/schemas/task.schema.ts` with `createTaskSchema` and `updateTaskSchema` (Zod objects matching Prisma model fields)
3. Create `src/schemas/user.schema.ts` with `createUserSchema` and `updateUserSchema`
4. Create `src/schemas/project.schema.ts` with `createProjectSchema` and `updateProjectSchema`
5. Create `src/middleware/validate.ts` — a middleware factory that takes a Zod schema and returns a middleware that validates `req.body`, returning 400 with Zod error details on failure
6. Add `validate(createTaskSchema)` middleware to `POST /tasks` and `validate(updateTaskSchema)` to `PUT /tasks/:id`
7. Repeat for users and projects routes
8. Replace `data: req.body` with `data: req.validatedBody` (set by validation middleware)
9. Write unit tests for each schema (valid data passes, invalid data fails with correct error)
10. Write integration test for the validate middleware

**Testing Requirements:**

- [ ] Each schema rejects missing required fields (returns specific field error)
- [ ] Each schema rejects invalid field types (string where number expected, etc.)
- [ ] Each schema accepts valid data and strips unknown fields
- [ ] Validate middleware returns 400 with structured error body on invalid input
- [ ] Validate middleware calls `next()` on valid input
- [ ] Integration: `POST /tasks` with invalid body returns 400, not 500
- [ ] Coverage target: new schema files should have ≥ 90% line coverage

**Acceptance Criteria:**

- [ ] All 6 endpoints have validation middleware applied
- [ ] Invalid requests return 400 with descriptive error messages (not 500)
- [ ] Valid requests continue to work as before
- [ ] Zod schemas match Prisma model constraints (required fields, types, string lengths)
- [ ] Unknown fields are stripped from validated data (no extra fields reach the database)
- [ ] New/updated tests cover the change
- [ ] No function exceeds 50 lines
- [ ] No new linter warnings: `npx eslint src/`
- [ ] Code is formatted: `npx prettier --check src/`
- [ ] Build passes: `npm run build`
- [ ] Tests pass: `npm test`
- [ ] Coverage has not decreased: `npm run test:coverage`

**Risk & Rollback:**

- **Risk:** Existing API consumers may be sending extra fields or incorrect types that were silently accepted before. This could break clients that rely on lenient input handling.
- **Rollback:** Revert the PR. No data migration involved. Consider logging validation failures before enforcing (a separate task if the team prefers gradual rollout).
- **Side effects:** Error response format changes from generic 500 errors to structured 400 errors. API consumers may need to update error handling.

**Verification Commands:**

```bash
npx eslint src/
npx prettier --check src/
npx tsc --noEmit
npm run build
npm test
npm run test:coverage
# Manual: POST /tasks with missing title field — should get 400, not 500
# Manual: POST /tasks with valid data — should work as before
```

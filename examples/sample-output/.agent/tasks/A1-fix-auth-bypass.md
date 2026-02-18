# Task A1: Fix JWT Authentication Bypass

| Field            | Value                         |
| ---------------- | ----------------------------- |
| **ID**           | `A1`                          |
| **Status**       | `[ ]`                         |
| **Priority**     | Critical                      |
| **Size**         | S                             |
| **Dependencies** | None                          |
| **Branch**       | `fix/a1-fix-auth-bypass`      |

**Goal:**
Fix a JWT verification bypass that allows unauthenticated access to protected endpoints. The `verifyToken` middleware does not validate the token signature, only checks that a token is present in the header.

**Current State:**

The middleware checks for token presence but calls `jwt.decode()` (no verification) instead of `jwt.verify()`:

```typescript
// src/middleware/auth.ts — lines 12-18
export const verifyToken = (req: Request, res: Response, next: NextFunction) => {
  const token = req.headers.authorization?.split(' ')[1];
  if (!token) return res.status(401).json({ error: 'No token provided' });

  const decoded = jwt.decode(token);  // BUG: decode() does NOT verify signature
  req.user = decoded as UserPayload;
  next();
};
```

Any valid-shaped JWT (even self-signed) passes this check.

**Scope:**

```
src/
├── middleware/auth.ts               # MODIFY — replace jwt.decode with jwt.verify
├── middleware/__tests__/auth.test.ts # MODIFY — add regression tests
└── config/env.ts                    # VERIFY — confirm JWT_SECRET is loaded
```

**Implementation:**

1. Open `src/middleware/auth.ts`
2. Replace `jwt.decode(token)` with `jwt.verify(token, process.env.JWT_SECRET!)`
3. Wrap in try-catch — return 401 with `{ error: 'Invalid token' }` on `JsonWebTokenError`
4. Return 401 with `{ error: 'Token expired' }` on `TokenExpiredError`
5. Add regression tests in `src/middleware/__tests__/auth.test.ts`

**Testing Requirements:**

- [ ] Test that a self-signed JWT (valid structure, wrong signature) returns 401
- [ ] Test that an expired JWT returns 401 with "Token expired" message
- [ ] Test that a malformed string returns 401
- [ ] Test that a valid JWT (correct signature) passes and populates `req.user`
- [ ] Test that missing Authorization header returns 401

**Acceptance Criteria:**

- [ ] `jwt.decode()` is no longer used anywhere in auth middleware
- [ ] `jwt.verify()` is used with the correct secret
- [ ] Invalid tokens return 401 (not 500 or 200)
- [ ] Expired tokens return 401 with descriptive message
- [ ] Valid tokens still work correctly
- [ ] New/updated tests cover the change
- [ ] No function exceeds 50 lines
- [ ] No new linter warnings: `npx eslint src/`
- [ ] Code is formatted: `npx prettier --check src/`
- [ ] Build passes: `npm run build`
- [ ] Tests pass: `npm test`
- [ ] Coverage has not decreased: `npm run test:coverage`

**Risk & Rollback:**

- **Risk:** If `JWT_SECRET` environment variable is not set, all requests will fail with 500. Verify env var is loaded before deploying.
- **Rollback:** Revert the PR. No data migration involved.
- **Side effects:** All active user sessions will continue to work (tokens are unchanged). Only forged/invalid tokens will be rejected.

**Verification Commands:**

```bash
npx eslint src/
npx prettier --check src/
npx tsc --noEmit
npm run build
npm test
npm run test:coverage
# Manual: try accessing a protected endpoint with a self-signed JWT — should get 401
```

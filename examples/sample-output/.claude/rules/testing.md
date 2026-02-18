# Testing Conventions

## Framework

- **Test runner:** Jest 29.x
- **Assertion library:** Jest built-in (`expect`)
- **Mocking:** Jest mocks (`jest.mock()`, `jest.spyOn()`)
- **Coverage tool:** Jest built-in (`--coverage` flag)

## File Organization

- Test location: alongside source files
- Test file naming: `{source}.test.ts` (e.g., `auth.test.ts`)
- Test helper location: `src/__tests__/helpers/`

## Test Categories

### Unit Tests

- **Purpose:** Test individual functions, middleware, and service methods in isolation
- **Location:** `{source}.test.ts` alongside source
- **Speed:** Must complete in < 100ms per test
- **Dependencies:** Mock all external services, database, and I/O

### Integration Tests

- **Purpose:** Test API endpoints with a real database
- **Location:** `src/__tests__/integration/`
- **Naming:** `{resource}.integration.test.ts`
- **Dependencies:** Uses test database (seeded per test suite)

## Test Structure

```typescript
describe('TaskService', () => {
  describe('getById', () => {
    it('should return the task when it exists', async () => {
      // Arrange
      const mockTask = createMockTask({ id: '1', title: 'Test' });
      prismaMock.task.findUnique.mockResolvedValue(mockTask);

      // Act
      const result = await taskService.getById('1');

      // Assert
      expect(result).toEqual(mockTask);
    });

    it('should throw NotFoundError when task does not exist', async () => {
      prismaMock.task.findUnique.mockResolvedValue(null);

      await expect(taskService.getById('999')).rejects.toThrow(NotFoundError);
    });
  });
});
```

## Naming

- Test suites: describe the module or class being tested (`describe('TaskService')`)
- Test cases: `should {expected behavior} when {condition}` (`it('should throw NotFoundError when task does not exist')`)

## Patterns

### Mocking

- Mock Prisma client using `jest.mock('@/config/prisma')` with typed mock
- Mock external HTTP calls using `jest.spyOn(axios, 'get')`
- Never mock business logic or pure functions — test them directly

### Assertions

- One logical assertion per test (multiple `expect` calls are fine if testing one behavior)
- Use `.toEqual()` for objects, `.toBe()` for primitives, `.toThrow()` for errors

## Required Tests by Task Type

| Task Type      | Required Tests                                                        |
| -------------- | --------------------------------------------------------------------- |
| Bug fix        | Regression test that reproduces the bug before the fix                |
| New feature    | Happy path, error paths, boundary conditions                          |
| Refactor       | No net decrease in test count or coverage percentage                  |
| Security fix   | Test that verifies the vulnerability is no longer exploitable         |

## Coverage

- **Minimum coverage:** Not enforced (baseline: 62%)
- **No-decrease rule:** Coverage must not decrease with any PR
- **Coverage command:** `npm run test:coverage`
- **Coverage report:** `coverage/lcov-report/index.html`
- **Excluded from coverage:** `src/config/`, `src/__tests__/helpers/`, `prisma/`
- **New code target:** All new code should have ≥ 80% line coverage

## Test Quality Rules

- Each test tests one behavior
- Test names describe expected behavior, not implementation details
- Tests must not depend on each other or on execution order
- Tests must not call real external services — mock them
- Tests must be deterministic — no `Math.random()`, no `Date.now()` without mocking

## Do / Don't

| Do                                          | Don't                                    |
| ------------------------------------------- | ---------------------------------------- |
| `it('should throw when user not found')`    | `it('test case 1')`                     |
| Mock Prisma, test the service logic         | Test Prisma queries directly in unit tests |
| Use `createMockTask()` helper               | Copy-paste mock objects across tests     |
| One behavior per test                       | Assert 5 unrelated things in one test    |

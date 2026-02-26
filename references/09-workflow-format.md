# Workflow Format

Generate `tasks/README.md` using this template.

## Template

````markdown
# Agent Environment

> Guide for executing development tasks on {{project-name}}.

## Quick Start

```
1. Open tasks/BACKLOG.md
2. Pick a task and create a branch
3. Follow the task's implementation steps
4. Write tests as specified
5. Run quality checks
6. Commit, push, and create PR
7. Mark task as completed in BACKLOG.md
```

## Step-by-Step Workflow

### Step 1 — Pick a Task

Open `tasks/BACKLOG.md`. Pick any `[ ]` task whose dependencies are all `[x]`.

### Step 2 — Create a Branch

Branch prefix by priority:

| Priority       | Prefix                 |
| -------------- | ---------------------- |
| Critical       | `hotfix/`              |
| High (bug)     | `fix/`                 |
| High (feature) | `feat/`                |
| Medium         | `feat/` or `refactor/` |
| Low            | `chore/`               |

```bash
git checkout -b {{prefix}}/{{task-id}}-{{slug}}
```

### Step 3 — Implement

Follow the task's Implementation steps. While coding:

- Keep functions under {{N}} lines
- Keep files under {{N}} lines
- Keep nesting under {{N}} levels
- Keep function parameters under {{N}}
- Add doc comments on public APIs and complex logic
- Use the project's formatter — never format manually
- If you encounter ambiguity: **STOP and ask the user**

### Step 4 — Write Tests

Follow the task's Testing Requirements. Test quality rules:

- One behavior per test
- Test names: `should {expected behavior} when {condition}`
- Tests must be deterministic — no randomness, no `Date.now()` without mocking
- Tests must be isolated — no shared state, no execution order dependencies
- Mock external I/O only — never mock business logic
- No flaky tests

### Step 5 — Run Quality Checks

```bash
{{format command}}     # Fix formatting
{{lint command}}       # Must pass with zero warnings
{{type check command}} # Must pass with zero errors
{{build command}}      # Must compile/build
{{test command}}       # All tests must pass
{{coverage command}}   # Coverage must not decrease
```

**All six checks must pass before committing.**

### Step 6 — Commit

```bash
git add -A
git commit -m "{{type}}({{scope}}): {{description}}

Task {{ID}} from tasks/BACKLOG.md"
```

Commit message format: `<type>(<scope>): <subject>` — imperative mood, ≤ 72 chars.

### Step 7 — Push and Create PR

```bash
git push -u origin {{branch-name}}
```

PR title: `{{type}}({{scope}}): brief description`

### Step 8 — Update BACKLOG.md

Change the task status from `[ ]` to `[x]` in `tasks/BACKLOG.md`.

---

## PR Review Checklist

| Category          | Check                                                                            |
| ----------------- | -------------------------------------------------------------------------------- |
| **Correctness**   | Solves the stated goal? Edge cases handled?                                      |
| **Code quality**  | Functions within length limit? Nesting within limit? No new complexity hotspots? |
| **Testing**       | Tests cover the change? Regression test for bugs? Coverage maintained?           |
| **Standards**     | Linter passes? Formatter passes? Type checker passes?                            |
| **Documentation** | Public APIs documented? Complex logic has "why" comments?                        |
| **Security**      | No secrets in code? Inputs validated? Auth checks present?                       |

---

## Guardrails

### When to STOP

- **Ambiguity:** If a task's requirements are unclear, stop and ask the user
- **Scope creep:** If you discover additional work, create a new task — don't expand the current one
- **XL task:** If implementation will exceed 8 files or 500 lines, stop and split the task first
- **Test failure:** If existing tests break, fix them before continuing

### Hard Rules

- Every PR must pass all quality gates
- Never suppress a linter warning without a comment explaining why
- Never decrease test coverage
- Never commit secrets, tokens, or credentials
- Never commit with failing tests
````

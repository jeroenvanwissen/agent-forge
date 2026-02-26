# Task Format

## File Naming

`{WaveLetter}{Number}-{lowercase-slug}.md` — slug is 2-4 words, hyphen-separated.

Examples: `A1-fix-auth-bypass.md`, `B3-add-input-validation.md`, `C1-refactor-user-service.md`

## Branch Naming

`{prefix}/{task-id}-{slug}`

| Priority       | Prefix                 |
| -------------- | ---------------------- |
| Critical       | `hotfix/`              |
| High (bug)     | `fix/`                 |
| High (feature) | `feat/`                |
| Medium         | `feat/` or `refactor/` |
| Low            | `chore/`               |

Example: `fix/b1-add-input-validation`

See also `tasks/README.md` (generated from `09-workflow-format`) Step 2 for the branch creation command.

## Template

````markdown
# Task {{ID}}: {{Title}}

| Field            | Value                              |
| ---------------- | ---------------------------------- |
| **ID**           | `{{ID}}`                           |
| **Status**       | `[ ]`                              |
| **Priority**     | {{Critical / High / Medium / Low}} |
| **Size**         | {{S / M / L}}                      |
| **Dependencies** | {{comma-separated IDs, or "None"}} |
| **Branch**       | `{{prefix/id-slug}}`               |

**Goal:**
{{One paragraph: what is broken/missing and what "done" looks like.}}

**Current State:**
{{Describe the problem with a specific code snippet showing the issue.}}

```{{language}}
// {{filepath}} — lines {{N-M}}
{{actual problematic code}}
```

**Baselines** (from Phase 1.6):

- Coverage: {{X%}} overall, {{Y%}} for affected module
- Complexity: {{affected function}} is {{N}} lines, nesting depth {{M}}
- Linter: {{N}} warnings in affected files

**Scope:**

```
{{directory tree showing ONLY files to CREATE / MODIFY / DELETE}}
```

**Implementation:**

1. {{Concrete step with file path}}
2. {{Next step}}
3. ...

**Testing Requirements:**

- [ ] {{Specific test case with expected behavior}}
- [ ] {{Another test case}}
- Coverage target: {{new files ≥ X% line coverage}}

**Acceptance Criteria:**

- [ ] {{Functional criterion}}
- [ ] {{Quality criterion (complexity, linter, formatter, coverage)}}
- [ ] No function exceeds {{N}} lines
- [ ] No new linter warnings: `{{lint command}}`
- [ ] Code is formatted: `{{format command}}`
- [ ] Build passes: `{{build command}}`
- [ ] Tests pass: `{{test command}}`
- [ ] Coverage has not decreased: `{{coverage command}}`

**Risk & Rollback:**

- **Risk:** {{What could go wrong}}
- **Rollback:** {{How to undo — revert PR, run migration down, etc.}}
- **Side effects:** {{What else is affected by this change}}

**Verification Commands:**

```bash
{{lint command}}
{{format command}}
{{type check command}}
{{build command}}
{{test command}}
{{coverage command}}
```
````

## Writing Rules

| Section        | Rule                                                                                           |
| -------------- | ---------------------------------------------------------------------------------------------- |
| Goal           | One paragraph max. State the problem and the outcome.                                          |
| Current State  | Always include a real code snippet showing the issue. Include baselines.                       |
| Scope          | Use `CREATE` / `MODIFY` / `DELETE` / `VERIFY` annotations. Only list affected files.           |
| Implementation | Numbered steps. Every step references a specific file path.                                    |
| Testing        | Checkboxes. Each test = one behavior. Include coverage target.                                 |
| Acceptance     | Checkboxes. Include both functional and quality criteria. All commands must be copy-pasteable. |
| Risk           | Always present, even if low. "Revert the PR" is a valid rollback.                              |
| Verification   | Copy-pasteable command block. Same commands across all tasks in the project.                   |

## Section Inclusion Rules

| Section         | Always | Sometimes                | Never |
| --------------- | ------ | ------------------------ | ----- |
| Goal            | ✅     |                          |       |
| Current State   | ✅     |                          |       |
| Baselines       |        | ✅ (skip for greenfield) |       |
| Scope           | ✅     |                          |       |
| Implementation  | ✅     |                          |       |
| Testing         | ✅     |                          |       |
| Acceptance      | ✅     |                          |       |
| Risk & Rollback | ✅     |                          |       |
| Verification    | ✅     |                          |       |

## Documentation-First Adaptations

For tasks derived from feature/technical docs rather than existing code:

- **Current State:** Replace code snippet with the relevant excerpt from the feature/technical doc (requirement, user story, or design decision). Reference the source document.
- **Baselines:** Omit or set to initial values (coverage: 0%, lint errors: 0, etc.)
- **Scope:** Use `CREATE` annotations for all files (nothing to `MODIFY` yet)
- **Implementation:** Include project scaffolding steps if this is an early-wave task (e.g., "Create `src/routes/` directory", "Initialize Express app in `src/app.ts`")
- **Risk & Rollback:** For greenfield, risk is more about design decisions than breaking changes (e.g., "Risk: chosen auth library may not support SSO — verify before implementing")

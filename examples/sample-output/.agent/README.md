# Agent Environment

> Guide for executing development tasks on TaskFlow API.

---

## Contents

| File                 | Purpose                        |
| -------------------- | ------------------------------ |
| [TASKS.md](TASKS.md) | Task index and project backlog |
| [tasks/](tasks/)     | Individual task detail files   |

---

## Quick Start

```
1.  Open .agent/TASKS.md
2.  Pick a task and create a branch:  git checkout -b <prefix>/<task-id>-<name>
3.  Execute the task following its implementation steps
4.  Write tests as specified in Testing Requirements
5.  Run quality checks:               npx eslint src/ && npx prettier --check src/ && npx tsc --noEmit
6.  Run verification:                  npm run build && npm test
7.  Check coverage:                    npm run test:coverage
8.  Commit:                            git add -A && git commit -m "descriptive message"
9.  Push:                              git push -u origin <branch-name>
10. Create PR:                         gh pr create --title "..." --body "..."
11. Mark task as completed in TASKS.md
```

> **Note:** This is an abbreviated example. See the full README.md template in ANALYZE.md section 3.3 for the complete workflow guide including PR review checklist, guardrails, and commit message format.

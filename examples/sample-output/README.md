# Sample Output

This directory shows an example of what Agent Forge generates when run against a fictional **Express.js REST API** project called "TaskFlow API."

This is not a real project — it's a realistic example to demonstrate the structure, level of detail, and conventions that the analysis produces.

## The Fictional Project

**TaskFlow API** is a Node.js/Express REST API for task management with:
- TypeScript, Express, Prisma ORM, PostgreSQL
- Jest for testing, ESLint + Prettier for linting/formatting
- JWT authentication, basic RBAC
- ~60% test coverage, some complexity hotspots

## What Was Generated

```
sample-output/
├── .agent/
│   ├── TASKS.md                        # Task backlog with 2 example waves
│   ├── README.md                       # Agent workflow (abbreviated)
│   └── tasks/
│       ├── A1-fix-auth-bypass.md       # Critical: auth bypass fix
│       └── B1-add-input-validation.md  # High: missing validation
└── .claude/
    ├── CLAUDE.md                       # Project overview
    └── rules/
        ├── project.md                  # Structure rules
        ├── code-style.md              # Style rules
        ├── testing.md                 # Testing conventions
        ├── security.md               # Security rules
        └── quality.md                # Quality thresholds
```

> **Note:** This is an abbreviated example. A real analysis would produce more tasks, more detailed rules, and project-specific examples in every Do/Don't table.

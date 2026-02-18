# 🔨 Agent Forge

**A framework for AI agents to analyze codebases and generate structured, quality-enforced task backlogs.**

Agent Forge gives any AI coding agent a systematic process to deeply analyze a project, extract conventions, identify issues, and produce a complete development backlog — with enforced quality gates, complexity limits, test coverage policies, and PR guardrails.

---

## What It Does

Drop two files into your project. An AI agent reads them, analyzes your entire codebase, and generates:

```
your-project/
├── AGENTS.md                       ← Generated: universal agent entry point
├── .ai/
│   └── rules/                      ← Generated: canonical rules (single source of truth)
│       ├── project.md
│       ├── code-style.md
│       ├── testing.md
│       ├── security.md
│       └── quality.md
├── .agent/
│   ├── ANALYZE.md              ← You add this (the instructions)
│   ├── ADDITIONAL-RULES.md     ← You add this (optional rule templates)
│   ├── TASKS.md                ← Generated: prioritized task backlog
│   ├── README.md               ← Generated: agent workflow guide
│   └── tasks/
│       ├── A1-fix-auth-bypass.md   ← Generated: detailed task files
│       └── ...
├── .claude/
│   └── CLAUDE.md               ← Generated: thin pointer to .ai/rules/
├── .github/
│   ├── copilot-instructions.md ← Generated: thin pointer to .ai/rules/
│   └── instructions/           ← Generated: path-specific rules (optional)
├── .junie/
│   └── guidelines.md           ← Generated: thin pointer to .ai/rules/
├── .vscode/
│   └── settings.json           ← Generated: editor + Copilot settings
└── src/                        Your existing code (unchanged)
```

## What Gets Enforced

Every task the agent generates includes these quality gates:

| Gate | Enforcement |
|------|-------------|
| **Linting** | Zero new warnings policy |
| **Formatting** | All code must be auto-formatted |
| **Type checking** | Zero new type errors |
| **Test coverage** | No decrease from baseline |
| **Complexity limits** | Function length, file length, nesting depth, parameter count |
| **PR size limits** | Max 8 files, max 500 lines changed |
| **Documentation** | Public APIs must have doc comments |
| **Security** | Input validation, no secrets in code |

---

## Quick Start

### 1. Copy the template files into your project

```bash
# From your project root:
mkdir -p .agent

# Copy the core instruction file
cp path/to/agent-forge/template/.agent/ANALYZE.md .agent/ANALYZE.md

# Copy the optional rules reference (recommended)
cp path/to/agent-forge/template/.agent/ADDITIONAL-RULES.md .agent/ADDITIONAL-RULES.md
```

Or with curl:

```bash
mkdir -p .agent
curl -sL https://raw.githubusercontent.com/jeroenvanwissen/agent-forge/main/template/.agent/ANALYZE.md -o .agent/ANALYZE.md
curl -sL https://raw.githubusercontent.com/jeroenvanwissen/agent-forge/main/template/.agent/ADDITIONAL-RULES.md -o .agent/ADDITIONAL-RULES.md
```

### 2. Run the analysis

With **Claude Code** (CLI):

```bash
claude
> Read .agent/ANALYZE.md and follow all instructions to analyze this project.
> Execute every phase in order. Generate all output files.
```

With **Claude.ai** or **Claude Projects**:

```
Read the .agent/ANALYZE.md file and follow all instructions to analyze
this project. Execute every phase in order. Generate all output files.
```

With **any AI agent**:

```
Follow the instructions in .agent/ANALYZE.md to analyze this project.
```

### 3. Start working tasks

```bash
# Open the generated task backlog
cat .agent/TASKS.md

# Pick a task and go
git checkout -b fix/a1-whatever-the-first-task-is
# Follow .agent/tasks/A1-whatever.md
```

---

## Repository Structure

```
agent-forge/
├── README.md                       # This file
├── LICENSE                         # MIT License
├── template/
│   └── .agent/
│       ├── ANALYZE.md              # Core analysis instructions (copy to your project)
│       └── ADDITIONAL-RULES.md     # Optional rule templates for specialized domains
├── docs/
│   ├── IMPLEMENTATION-GUIDE.md     # Detailed step-by-step setup guide
│   └── CHANGELOG.md               # Version history
└── examples/
    └── sample-output/             # Example of generated output for a sample project
        ├── .agent/
        │   ├── TASKS.md
        │   ├── README.md
        │   └── tasks/
        │       ├── A1-fix-auth-bypass.md
        │       └── B1-add-input-validation.md
        └── .claude/
            ├── CLAUDE.md
            └── rules/
                ├── project.md
                ├── code-style.md
                ├── testing.md
                ├── security.md
                └── quality.md
```

---

## How It Works

The analysis runs in 4 phases:

### Phase 1: Deep Analysis
The agent reads every file in your project — source code, configs, tests, docs, linter settings, CI pipelines — and builds a complete understanding of the architecture, conventions, and quality tooling.

### Phase 2: Prioritize & Group
Findings are prioritized (Critical → Low), sized (S/M/L), and organized into dependency-ordered waves (A through F). Wave A contains only critical fixes with no dependencies — all can start in parallel.

### Phase 3: Generate Output
The agent produces task files, the backlog index, workflow guide, project conventions, and rule files. Every task includes implementation steps, testing requirements, acceptance criteria, and verification commands.

### Phase 4: Quality Check
The agent validates its own output against a comprehensive checklist — correct task IDs, valid dependencies, no circular references, all commands are copy-pasteable, all rules are based on observed conventions (not invented).

---

## What's in Each File

### Core Files (you copy these)

| File | Size | Purpose |
|------|------|---------|
| [`ANALYZE.md`](template/.agent/ANALYZE.md) | ~1,600 lines | The complete analysis instructions. This is the brain of the system. |
| [`ADDITIONAL-RULES.md`](template/.agent/ADDITIONAL-RULES.md) | ~665 lines | Templates for optional domain-specific rules (API, database, deployment, git, observability, performance, accessibility, i18n). |

### Generated Files (the agent creates these)

| File | Purpose |
|------|---------|
| `.agent/TASKS.md` | Central task index with waves, quality baseline, dependency graph, and guardrails |
| `.agent/README.md` | Workflow guide with step-by-step process, PR review checklist, and quality gates |
| `.agent/tasks/*.md` | Individual task files with goal, scope, implementation steps, testing requirements, acceptance criteria, risk/rollback |
| `.claude/CLAUDE.md` | Project overview loaded into agent context — quick reference commands, architecture, quality standards |
| `.claude/rules/project.md` | Directory structure, module organization, dependency rules |
| `.claude/rules/code-style.md` | Formatter, linter, type checker config, naming conventions, documentation comments |
| `.claude/rules/testing.md` | Test categories (unit/integration/e2e), coverage policy, test quality rules |
| `.claude/rules/security.md` | Secrets management, input validation, auth patterns, data protection |
| `.claude/rules/quality.md` | Complexity limits, cognitive complexity examples, coverage policy, hotspot tracking |

---

## Key Design Decisions

**Multi-agent, no duplication.** Rules live in `.ai/rules/` as a single source of truth. Claude, Copilot, Junie, and generic agents each get a thin pointer file. Edit rules once, every tool stays in sync. See the [Multi-Agent Setup Guide](docs/MULTI-AGENT-SETUP.md).

**Agent-agnostic tasks.** The `.agent/` directory and ANALYZE.md work with any AI coding agent. The task format is plain Markdown that any tool or human can follow.

**Convention-first.** Rules are extracted from the actual codebase, not invented. If the project has no convention for something, it's flagged as "No established convention — recommended:" with a clear suggestion.

**Measurable quality.** Every task includes specific, measurable acceptance criteria. No subjective goals like "improve code quality" — instead: "Function X is under 50 lines, coverage is ≥ 80%, zero linter warnings."

**Incremental.** Tasks are organized in waves with explicit dependencies. You can complete Wave A without touching Wave F. The quality baseline is tracked and updated as work progresses.

**Splittable.** Any task that would exceed 8 files or 500 lines is split during planning, not during implementation. Every sub-task must independently pass build and tests.

---

## Documentation

| Document | Description |
|----------|-------------|
| [Implementation Guide](docs/IMPLEMENTATION-GUIDE.md) | Detailed step-by-step setup, .gitignore strategies, verification checklist, troubleshooting |
| [Multi-Agent Setup](docs/MULTI-AGENT-SETUP.md) | How to configure Claude, Copilot, Junie, and generic agents without duplicating rules |
| [ANALYZE.md](template/.agent/ANALYZE.md) | The core instructions (read this to understand the full process) |
| [Additional Rules](template/.agent/ADDITIONAL-RULES.md) | Templates for API, database, deployment, git, observability, performance, a11y, i18n rules |
| [Changelog](docs/CHANGELOG.md) | Version history and changes |

---

## FAQ

**Q: Does this only work with Claude?**
No. The `.agent/ANALYZE.md` file is agent-agnostic — any AI coding agent can follow it. The `.claude/` directory is Claude-specific (auto-loaded into context), but the conventions it contains are useful for any agent or human developer.

**Q: How long does the analysis take?**
Depends on project size. A small project (< 50 files) typically completes in one conversation. Large projects may need multiple sessions or a Claude Project with all files loaded.

**Q: Do I commit the generated files?**
Yes, recommended. The task backlog and conventions are valuable project documentation. See the [Implementation Guide](docs/IMPLEMENTATION-GUIDE.md) for .gitignore strategies if you want alternatives.

**Q: What if the agent invents conventions that don't exist in my project?**
The ANALYZE.md instructions explicitly prohibit this. Convention gaps are marked as "No established convention — recommended:" and clearly separated from observed patterns. If it happens anyway, correct the rule files and re-prompt.

**Q: Can I customize the complexity limits, coverage targets, etc.?**
Yes. The defaults (50-line functions, 300-line files, 4 levels of nesting) are starting points. The agent calibrates them to your project's existing patterns during analysis.

**Q: What if my project has no linter/formatter/type checker?**
The analysis will flag this as a finding and generate a task to set them up. The quality gates adapt to what's available — if there's no linter, the linter gate is marked N/A.

---

## Contributing

Contributions are welcome. If you have improvements to the analysis process, new rule templates, or better quality gates:

1. Fork the repo
2. Create a branch: `git checkout -b feature/your-improvement`
3. Make your changes
4. Submit a PR with a clear description of what you changed and why

---

## License

MIT — see [LICENSE](LICENSE).

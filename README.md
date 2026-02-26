# project-analysis

Analyze a codebase or documented project plan and generate a structured development backlog with prioritized tasks, coding conventions, and multi-agent configuration files.

## What It Does

This skill runs a 4-phase analysis on your project and produces:

- **Task backlog** (`tasks/BACKLOG.md`) with prioritized, dependency-ordered waves
- **Individual task files** (`tasks/*.md`) with acceptance criteria, test requirements, and references
- **Agent workflow guide** (`tasks/README.md`) with PR checklists and branch naming conventions
- **Coding conventions** (`.ai/rules/`) as a single source of truth for all AI tools
- **Agent entry points** for Claude Code, GitHub Copilot, JetBrains Junie, Cursor, and Aider
- **Quality baseline** with coverage, lint, complexity, and dependency health metrics

It works on existing codebases and documentation-first/greenfield projects alike.

## Install

### OpenCode (global)

```bash
mkdir -p ~/.config/opencode/skills/project-analysis
cp -r . ~/.config/opencode/skills/project-analysis/
```

### OpenCode (project-local)

```bash
mkdir -p .opencode/skills/project-analysis
cp -r . .opencode/skills/project-analysis/
```

### Claude Code (global)

```bash
mkdir -p ~/.claude/skills/project-analysis
cp -r . ~/.claude/skills/project-analysis/
```

### Claude Code (project-local)

```bash
mkdir -p .claude/skills/project-analysis
cp -r . .claude/skills/project-analysis/
```

### Generic agents path

```bash
mkdir -p .agents/skills/project-analysis
cp -r . .agents/skills/project-analysis/
```

The directory name must be `project-analysis` (matching the skill name).

## Usage

Trigger the skill with any of these phrases:

- "analyze this project"
- "create a task backlog"
- "onboard this codebase"
- "what needs fixing"
- "set up coding standards"
- "generate tasks"
- "scaffold from docs"
- "build this from the spec"

## How It Works

### Phase 1 -- Deep Analysis

Reads all source files, configs, tests, and documentation. Extracts coding conventions, categorizes findings by severity (Critical/High/Medium/Low), triages TODO/FIXME/HACK markers, measures quality baselines, and detects existing AI tool configurations.

### Phase 2 -- Prioritize and Group

Assigns priority and size estimates to each finding, organizes them into dependency-ordered waves (A, B, C, ...), and assigns unique task IDs.

### Checkpoint -- User Validation

Before generating files, the skill pauses and presents:

1. Summary of findings by priority
2. Proposed waves with themes and task counts
3. Key architectural observations
4. Quality baseline snapshot
5. AI tool selection (see below)

You confirm or adjust priorities, scope, and tool selection before proceeding.

### Phase 3 -- Generate Output

Generates all task files, the backlog, workflow guide, coding conventions, and agent entry points for the tools you selected.

### Phase 4 -- Quality Check

Validates all generated output against a checklist: cross-references, formatting, completeness, and consistency.

## AI Tool Selection

The skill auto-detects which AI tools are already configured in your project by checking for known config files (`.claude/`, `.github/copilot-instructions.md`, `.junie/`, `.cursor/rules/`, `.aider.conf.yml`). Detected tools are pre-selected at the Checkpoint.

You then confirm or adjust the selection. Only pointer files for confirmed tools are generated.

**Always generated** (regardless of selection):

- `AGENTS.md` -- Universal agent entry point
- `.ai/rules/` -- Canonical coding conventions (single source of truth)
- `.editorconfig` -- Editor formatting (if missing)

**Conditionally generated** (based on your selection):

| Tool            | Generated files                                                                     |
| --------------- | ----------------------------------------------------------------------------------- |
| Claude Code     | `.claude/CLAUDE.md`                                                                 |
| GitHub Copilot  | `.github/copilot-instructions.md`, `.github/instructions/`, `.vscode/settings.json` |
| JetBrains Junie | `.junie/guidelines.md`                                                              |
| Cursor          | `.cursor/rules/project-conventions.mdc`                                             |
| Aider           | `.aider.conf.yml`                                                                   |

All pointer files reference `.ai/rules/` -- no rule duplication across tools.

## Output Structure

```
AGENTS.md
.ai/rules/
  project.md
  code-style.md
  testing.md
  security.md
  quality.md
.editorconfig
tasks/
  BACKLOG.md
  README.md
  A1-fix-auth-bypass.md
  B1-add-input-validation.md
  ...
.claude/CLAUDE.md                    # if selected
.github/copilot-instructions.md     # if selected
.github/instructions/               # if selected
.vscode/settings.json               # if selected
.junie/guidelines.md                 # if selected
.cursor/rules/                       # if selected
.aider.conf.yml                      # if selected
```

## Re-Running

Re-run the skill after major refactors, wave completions, dependency overhauls, or when the backlog is >30% outdated. The skill preserves completed and in-progress tasks and only updates pending items.

## File Inventory

| File                                    | Purpose                                         |
| --------------------------------------- | ----------------------------------------------- |
| `SKILL.md`                              | Main entry point and orchestrator               |
| `references/01-read-project.md`         | What to read and how to map architecture        |
| `references/02-extract-conventions.md`  | Convention extraction instructions              |
| `references/03-identify-findings.md`    | Issue categorization (Critical/High/Medium/Low) |
| `references/04-triage-todos.md`         | TODO/FIXME/HACK triage process                  |
| `references/05-measure-baseline.md`     | Quality baseline measurement                    |
| `references/06-prioritize-and-group.md` | Priority, sizing, wave grouping, IDs            |
| `references/07-task-format.md`          | Per-task file template                          |
| `references/08-backlog-format.md`       | BACKLOG.md template                             |
| `references/09-workflow-format.md`      | Agent workflow guide and PR checklist           |
| `references/10-rules-format.md`         | `.ai/rules/` file templates                     |
| `references/11-agent-pointers.md`       | Multi-agent entry points and pointer templates  |
| `references/12-quality-checklist.md`    | Verification checklists for all output          |
| `references/additional-rules.md`        | Optional domain-specific rule templates         |

## License

MIT

---
name: project-analysis
description: >
  Analyze an existing codebase or a documented project plan end-to-end and generate
  a structured, quality-enforced development backlog with prioritized tasks, coding
  conventions, and multi-agent configuration. Use when: (1) starting work on an
  existing project, (2) onboarding AI agents to a codebase, (3) creating a prioritized
  task backlog from scratch, (4) setting up coding standards and quality gates,
  (5) configuring Claude, GitHub Copilot, JetBrains Junie, Cursor, and Aider to share
  project rules without duplication, (6) scaffolding a new project from feature docs and technical
  docs. Triggers: "analyze this project", "create a task backlog", "onboard this
  codebase", "what needs fixing", "set up coding standards", "generate tasks",
  "scaffold from docs", "build this from the spec".
---

# Project Analysis

Analyze a codebase — or a documented project plan — and generate a structured development backlog with quality-enforced tasks, coding conventions, and multi-agent entry points.

## References

Read ALL reference files before starting. They contain the detailed instructions, templates, and checklists for each phase.

### Phase 1 — Deep Analysis

| Reference                                                      | Purpose                                                                 |
| -------------------------------------------------------------- | ----------------------------------------------------------------------- |
| [01-read-project](references/01-read-project.md)               | What to read, how to map architecture, tooling inventory                |
| [02-extract-conventions](references/02-extract-conventions.md) | Coding, architecture, testing, security, quality conventions to extract |
| [03-identify-findings](references/03-identify-findings.md)     | Issue categories: Critical, High, Medium, Low                           |
| [04-triage-todos](references/04-triage-todos.md)               | TODO/FIXME/HACK triage process                                          |
| [05-measure-baseline](references/05-measure-baseline.md)       | Lint, coverage, complexity, dependency health measurement               |

### Phase 2 — Prioritize and Group

| Reference                                                        | Purpose                                                                                |
| ---------------------------------------------------------------- | -------------------------------------------------------------------------------------- |
| [06-prioritize-and-group](references/06-prioritize-and-group.md) | Priority assignment, size estimation, wave grouping, dependency mapping, ID assignment |

### Phase 3 — Generate Output

| Reference                                              | Purpose                                                                    |
| ------------------------------------------------------ | -------------------------------------------------------------------------- |
| [07-task-format](references/07-task-format.md)         | Per-task file template (`tasks/*.md`)                                      |
| [08-backlog-format](references/08-backlog-format.md)   | BACKLOG.md template with guardrails and quality baseline                   |
| [09-workflow-format](references/09-workflow-format.md) | Agent workflow guide, PR review checklist (`tasks/README.md`)              |
| [10-rules-format](references/10-rules-format.md)       | How to write `.ai/rules/` files — templates for all 5 core rules           |
| [11-agent-pointers](references/11-agent-pointers.md)   | Multi-agent entry points: AGENTS.md, Claude, Copilot, Junie, Cursor, Aider |

### Phase 4 — Quality Check

| Reference                                                  | Purpose                                          |
| ---------------------------------------------------------- | ------------------------------------------------ |
| [12-quality-checklist](references/12-quality-checklist.md) | Verification checklists for all generated output |

### Optional

| Reference                                          | Purpose                                                                                                              |
| -------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------- |
| [additional-rules](references/additional-rules.md) | Templates for domain-specific rules: API, database, deployment, git, observability, performance, accessibility, i18n |

## Process

### Phase 1 — Deep Analysis

1. Read all source files, configs, tests, and docs per `01-read-project`
2. Detect existing AI tool configurations per `01-read-project` "AI Tool Detection" section — record which tools are already configured
3. Extract conventions per `02-extract-conventions`
4. Categorize findings per `03-identify-findings`
5. Triage TODOs per `04-triage-todos`
6. Measure quality baseline per `05-measure-baseline`

### Phase 2 — Prioritize and Group

Assign priority, estimate size, organize into dependency-ordered waves, and assign IDs per `06-prioritize-and-group`.

### Checkpoint — User Validation

Before generating any output files, present the user with:

1. **Summary of findings** — count by priority (N Critical, N High, N Medium, N Low)
2. **Proposed waves** — wave themes and task count per wave (e.g., "Wave A: 3 security fixes, Wave B: 5 validation tasks")
3. **Key architectural observations** — anything surprising or that requires a judgment call
4. **Quality baseline snapshot** — current coverage, lint errors, complexity hotspots
5. **AI tool selection** — Based on tool configurations detected in Phase 1, present the user with pre-selected options and ask them to confirm or adjust:

   **Always generated (not selectable):**
   - `AGENTS.md` — Universal agent entry point (read by most AI tools)
   - `.ai/rules/` — Canonical coding conventions (single source of truth)

   **Select which AI coding tools and IDEs you use (multi-select):**
   - [ ] Claude Code → generates `.claude/CLAUDE.md`
   - [ ] GitHub Copilot → generates `.github/copilot-instructions.md`, `.github/instructions/`, `.vscode/settings.json`
   - [ ] JetBrains Junie / IDEA → generates `.junie/guidelines.md`
   - [ ] Cursor → generates `.cursor/rules/`
   - [ ] Aider → generates `.aider.conf.yml`

   Pre-select any tools that already have configuration files in the project (detected in Phase 1).
   Only generate pointer files for the tools the user confirms.
   If none are selected, generate only `AGENTS.md` and `.ai/rules/`.

Ask the user to confirm the priorities, scope, and tool selection before proceeding to Phase 3. If the user wants to adjust priorities, re-scope, skip certain findings, or change the tool selection, update the plan before generating files.

### Phase 3 — Generate Output

Generate all output files:

| Output                                                   | Template                 |
| -------------------------------------------------------- | ------------------------ |
| `tasks/*.md`                                             | Per `07-task-format`     |
| `tasks/BACKLOG.md`                                       | Per `08-backlog-format`  |
| `tasks/README.md`                                        | Per `09-workflow-format` |
| `.ai/rules/*.md`                                         | Per `10-rules-format`    |
| `AGENTS.md`, `.editorconfig`, and selected tool pointers | Per `11-agent-pointers`  |

### Phase 4 — Quality Check

Validate all output per `12-quality-checklist`.

## Output Structure

```
# Always generated:
AGENTS.md                         # Universal agent entry point
.ai/rules/                        # Canonical rules (single source of truth)
  project.md, code-style.md, testing.md, security.md, quality.md
.editorconfig                     # Universal editor formatting (if missing)
tasks/
  BACKLOG.md                       # Task backlog with waves and guardrails
  README.md                        # Agent workflow guide
  A1-fix-auth-bypass.md            # Individual task files
  B1-add-input-validation.md
  ...

# Conditional (based on tool selection in Checkpoint):
.claude/CLAUDE.md                  # If Claude Code selected
.github/copilot-instructions.md   # If GitHub Copilot selected
.github/instructions/              # If GitHub Copilot selected (path-specific)
.vscode/settings.json              # If GitHub Copilot selected
.junie/guidelines.md               # If JetBrains Junie selected
.cursor/rules/                     # If Cursor selected
.aider.conf.yml                    # If Aider selected
```

## When to Re-Run

- Major refactors or architecture changes
- Wave completion (re-assess remaining work)
- Dependency overhaul
- Backlog is >30% outdated
- Post-incident review reveals systemic issues

### Re-Run Reconciliation

When output files already exist from a previous run:

1. **`tasks/BACKLOG.md`** — Preserve completed (`[x]`) and in-progress (`[~]`) tasks. Only update `[ ]` and `[!]` tasks. Add new findings as new tasks with new IDs (never reuse retired IDs).
2. **`tasks/`** — Do not overwrite completed task files. Update or replace only `[ ]` task files. Create new files for new tasks.
3. **`.ai/rules/`** — Update rules to reflect current codebase state. Rules are living documents and should always reflect current conventions.
4. **Agent entry points** — Update commands and architecture descriptions if changed. Pointer structure stays the same.
5. **Quality Baseline** — Re-measure and update the table. Compare against previous baseline to show progress.

### Documentation-First Projects (New Projects from Docs)

This skill works for new projects that have feature docs and/or technical docs but little or no source code. The process adapts automatically:

- **Phase 1:** Reads documentation instead of source code. Extracts requirements from feature docs and architecture decisions from technical docs. Derives conventions from the documented stack rather than observing them. Most tooling will be "missing" — this becomes Wave A setup tasks.
- **Phase 2:** Prioritizes in build order — scaffolding → infrastructure → core features → secondary features → polish.
- **Phase 3:** Generates tasks referencing doc requirements instead of code snippets. Rules are derived from stack choices with clear attribution.
- **Phase 4:** Same quality checks apply.

See `01-read-project` "Documentation-First Projects" section, `02-extract-conventions` "Deriving Conventions" section, `03-identify-findings` "Documentation-First Projects" section, and `06-prioritize-and-group` "Documentation-First Wave Structure" for detailed guidance.

## Output Summary

After generating all files, present the user with:

```
## Analysis Complete

**Project:** {{name}} ({{language}}/{{framework}})
**Tasks:** {{N}} across {{M}} waves
**Findings:** {{N}} Critical, {{N}} High, {{N}} Medium, {{N}} Low

### Quality Baseline
| Metric | Value |
|--------|-------|
| Coverage | {{X%}} |
| Lint errors | {{N}} |
| Type errors | {{N}} |

### Generated Files
- {{N}} task files in tasks/
- tasks/BACKLOG.md, tasks/README.md
- {{N}} rule files in .ai/rules/
- .editorconfig
- Agent entry points: AGENTS.md{{, .claude/ (Claude Code)}}{{, .github/ (Copilot)}}{{, .vscode/ (Copilot)}}{{, .junie/ (Junie)}}{{, .cursor/ (Cursor)}}{{, .aider.conf.yml (Aider)}}
  (list only the tools selected by the user)

### Recommended First Action
Start with Wave A: {{wave A theme and task count}}.
```

## Execution Checklist

```
[ ] Read all references/ files
[ ] Phase 1: Deep Analysis (01-05)
[ ] Phase 1: Detect existing AI tool configurations
[ ] Phase 2: Prioritize and Group (06)
[ ] Checkpoint: User validation (priorities, scope, tool selection)
[ ] Phase 3: Generate Output (07-11, respecting tool selection)
[ ] Phase 4: Quality Check (12)
[ ] Present summary to user
```

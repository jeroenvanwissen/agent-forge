# Quality Checklist

Run through every section before presenting results to the user.

## Task Files

- [ ] Every task has a unique ID matching `{WaveLetter}{Number}`
- [ ] Every task file is named `{ID}-{slug}.md` with 2-4 word slug
- [ ] Every task has: Goal, Current State, Scope, Implementation, Testing, Acceptance, Risk, Verification
- [ ] Current State includes a real code snippet (not hypothetical)
- [ ] Implementation steps reference specific file paths
- [ ] Testing requirements are checkboxes with specific behaviors
- [ ] Acceptance criteria include both functional and quality checks
- [ ] All commands in Verification are copy-pasteable and correct
- [ ] No task exceeds 8 files or 500 lines (XL tasks have been split)
- [ ] Dependencies reference valid task IDs that exist

## BACKLOG.md

- [ ] Every task in `tasks/` appears in BACKLOG.md with correct link
- [ ] No task appears in BACKLOG.md without a corresponding file in `tasks/`
- [ ] Wave groupings are correct — same-wave tasks don't share file conflicts
- [ ] Dependencies are acyclic (no circular references)
- [ ] Mermaid dependency graph matches the dependency fields in task files
- [ ] Quality Baseline table has real measured values (not placeholders)
- [ ] Guardrails section has correct project-specific commands
- [ ] Complexity limits are calibrated to the project (not just defaults)

## Naming

- [ ] Task IDs are sequential within waves (A1, A2, B1, B2...)
- [ ] Branch names follow `{prefix}/{id}-{slug}` pattern
- [ ] File slugs are lowercase, hyphen-separated, 2-4 words

## Content Quality

- [ ] Every task's Goal is one paragraph — states problem and outcome
- [ ] No two tasks overlap in scope (no file modified by two same-wave tasks)
- [ ] Every Critical/High task has a specific code snippet in Current State
- [ ] Testing Requirements specify behaviors, not implementations
- [ ] No task says "improve" or "refactor" without measurable criteria
- [ ] Follows naming conventions from `.ai/rules/code-style.md`
- [ ] Follows patterns from `.ai/rules/project.md`
- [ ] Follows security patterns from `.ai/rules/security.md`

## Agent Entry Points

- [ ] `AGENTS.md` at root is concise and links to `.ai/rules/`
- [ ] `.editorconfig` exists and matches project formatting conventions
- [ ] Only pointer files for user-selected tools were generated (no extras)
- [ ] (If Claude Code) `.claude/CLAUDE.md` is thin — overview and links only, no duplicated rules
- [ ] (If Copilot) `.github/copilot-instructions.md` references `.ai/rules/`
- [ ] (If Copilot) `.github/instructions/*.instructions.md` have correct `applyTo` frontmatter
- [ ] (If Copilot) `.vscode/settings.json` has Copilot instruction file pointers
- [ ] (If Junie) `.junie/guidelines.md` references `.ai/rules/`
- [ ] (If Cursor) `.cursor/rules/*.mdc` files reference `.ai/rules/` and have correct frontmatter
- [ ] (If Aider) `.aider.conf.yml` has read list pointing to `.ai/rules/` files
- [ ] No rule content is duplicated across pointer files
- [ ] Quick reference commands are consistent across all generated pointer files

## Workflow (README.md)

- [ ] `tasks/README.md` has Quick Start, Step-by-Step, PR Review Checklist, and Guardrails
- [ ] All commands are copy-pasteable and match BACKLOG.md verification commands
- [ ] Complexity limits match BACKLOG.md guardrails
- [ ] Commit message format is specified
- [ ] "When to STOP" section is present

## Rules Files

- [ ] All rules are based on observed conventions (not invented)
- [ ] Convention gaps marked with `> **No established convention.**` format
- [ ] For doc-first projects: derived conventions marked with `> **Derived from stack choice.**` format
- [ ] Every rule file uses imperative voice ("Use X" not "We use X")
- [ ] Code examples use the project's actual language and patterns
- [ ] Do/Don't tables present at end of major sections
- [ ] Quality rules include linter suppression policy
- [ ] Testing rules include required tests by task type
- [ ] Complexity limits are calibrated, not just defaults
- [ ] Additional rule files created only for domains with established conventions

## Documentation-First Projects (if applicable)

- [ ] Feature doc requirements are mapped to concrete tasks (not 1:1 — large features are split)
- [ ] Technical doc architecture decisions are reflected in rules files
- [ ] Wave A contains project scaffolding and tooling setup
- [ ] Task "Current State" sections reference source documents, not code snippets
- [ ] Derived conventions are clearly attributed to stack choice
- [ ] No conventions are presented as "observed" when they are actually derived

## Completeness

- [ ] All four phases were completed (no partial or interrupted runs)
- [ ] All files listed in Output Structure were generated (respecting tool selection)
- [ ] Output Summary was presented to the user
- [ ] User confirmed tool selection during the Checkpoint

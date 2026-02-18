# Multi-Agent Setup Guide

> How to configure Claude, GitHub Copilot, JetBrains Junie, and generic AI agents to share the same project rules — without duplicating files.

---

## The Problem

Every AI tool looks for instructions in a different place:

| Tool                    | Looks In                                          |
| ----------------------- | ------------------------------------------------- |
| Claude Code             | `.claude/CLAUDE.md` + `.claude/rules/*.md`        |
| GitHub Copilot (VS Code)| `.github/copilot-instructions.md`                 |
| Copilot Coding Agent    | `.github/copilot-instructions.md` + `AGENTS.md`   |
| Copilot path-specific   | `.github/instructions/*.instructions.md`          |
| JetBrains Junie         | `.junie/guidelines.md` (or `AGENTS.md`)           |
| Cursor, Windsurf, etc.  | `.cursorrules`, `.windsurfrules`, `AGENTS.md`     |

Maintaining the same rules in 4+ places is a maintenance nightmare. One file gets updated, the others go stale, and agents get conflicting instructions.

## The Solution: Shared Rules, Thin Pointers

Put the **canonical rules** in one shared location. Each tool gets a **thin pointer file** — a small file that either includes or references the shared content.

```
your-project/
├── AGENTS.md                           # Universal entry point (all agents read this)
│
├── .ai/                                # ═══ SHARED — canonical rules live here ═══
│   └── rules/
│       ├── project.md                  # Structure & architecture
│       ├── code-style.md              # Formatting, naming, linting
│       ├── testing.md                 # Test conventions & coverage
│       ├── security.md               # Security requirements
│       ├── quality.md                # Complexity limits & enforcement
│       └── {optional}.md             # API, database, deployment, etc.
│
├── .agent/                             # ═══ TASK MANAGEMENT ═══
│   ├── ANALYZE.md                      # Analysis instructions
│   ├── ADDITIONAL-RULES.md            # Optional rule templates
│   ├── TASKS.md                       # Task backlog
│   ├── README.md                      # Agent workflow guide
│   └── tasks/                         # Individual task files
│       └── *.md
│
├── .claude/                            # ═══ CLAUDE — thin pointers ═══
│   └── CLAUDE.md                       # Project overview + references .ai/rules/
│
├── .github/                            # ═══ COPILOT — thin pointers ═══
│   ├── copilot-instructions.md         # References .ai/rules/
│   └── instructions/                   # Path-specific instructions (optional)
│       ├── tests.instructions.md
│       └── security.instructions.md
│
├── .junie/                             # ═══ JUNIE — thin pointer ═══
│   └── guidelines.md                   # References .ai/rules/
│
└── .vscode/                            # ═══ VS CODE — settings ═══
    └── settings.json                   # Points Copilot at shared rules
```

### What lives where

| Directory      | Contains                          | Who reads it              | Committed to git? |
| -------------- | --------------------------------- | ------------------------- | ------------------ |
| `.ai/rules/`   | Canonical rules (single source)  | Referenced by all tools   | ✅ Yes             |
| `.agent/`      | Tasks, backlog, workflow         | Any agent doing dev work  | ✅ Yes             |
| `.claude/`     | Thin pointer to `.ai/rules/`     | Claude Code (auto-loaded) | ✅ Yes             |
| `.github/`     | Thin pointer to `.ai/rules/`     | GitHub Copilot            | ✅ Yes             |
| `.junie/`      | Thin pointer to `.ai/rules/`     | JetBrains Junie           | ✅ Yes             |
| `.vscode/`     | Editor settings                  | VS Code + Copilot         | ✅ Yes (team shared) |
| `.idea/`       | JetBrains IDE settings           | IntelliJ/WebStorm/etc.    | ⚠️ Usually gitignored |
| `AGENTS.md`    | Universal agent entry point      | All agents                | ✅ Yes             |

---

## File Templates

### AGENTS.md (root)

The universal entry point. Most AI tools will read this. It should be a concise overview that points to everything else.

```markdown
# {{project-name}}

> {{One-line project description.}}

## Quick Reference

| Action       | Command                    |
| ------------ | -------------------------- |
| Build        | `{{build command}}`        |
| Test         | `{{test command}}`         |
| Lint         | `{{lint command}}`         |
| Format       | `{{format command}}`       |
| Type check   | `{{type check command}}`   |
| Coverage     | `{{coverage command}}`     |

## Architecture

{{2-5 sentences describing the core architecture.}}

## Project Structure

{{Annotated directory tree.}}

## Rules

All project conventions are in `.ai/rules/`:

- [Project Structure](.ai/rules/project.md)
- [Code Style](.ai/rules/code-style.md)
- [Testing](.ai/rules/testing.md)
- [Security](.ai/rules/security.md)
- [Quality](.ai/rules/quality.md)

## Quality Standards

Before every commit:

1. Format: `{{format command}}`
2. Lint: `{{lint command}}` — zero warnings
3. Type check: `{{type check command}}` — zero errors
4. Build: `{{build command}}`
5. Test: `{{test command}}`
6. Coverage: `{{coverage command}}` — no decrease

Key limits (see [quality rules](.ai/rules/quality.md)):
- Max function length: {{N}} lines
- Max file length: {{N}} lines
- Max nesting depth: {{N}} levels
- Max function parameters: {{N}}

## Tasks

See [.agent/TASKS.md](.agent/TASKS.md) for the development backlog.
```

---

### .claude/CLAUDE.md

Claude Code auto-loads this file. Keep it thin — reference the shared rules and AGENTS.md.

```markdown
# {{project-name}}

> {{One-line project description.}}

## Quick Reference

| Action       | Command                    |
| ------------ | -------------------------- |
| Build        | `{{build command}}`        |
| Test         | `{{test command}}`         |
| Lint         | `{{lint command}}`         |
| Format       | `{{format command}}`       |
| Type check   | `{{type check command}}`   |
| Coverage     | `{{coverage command}}`     |

## Rules

All project conventions are in `.ai/rules/` (shared across all AI tools):

| File                                         | Covers                                       |
| -------------------------------------------- | -------------------------------------------- |
| [.ai/rules/project.md](../.ai/rules/project.md)       | Project structure and architecture   |
| [.ai/rules/code-style.md](../.ai/rules/code-style.md) | Code style and formatting            |
| [.ai/rules/testing.md](../.ai/rules/testing.md)       | Testing conventions and requirements |
| [.ai/rules/security.md](../.ai/rules/security.md)     | Security requirements and practices  |
| [.ai/rules/quality.md](../.ai/rules/quality.md)       | Code quality and complexity limits   |

Read all `.ai/rules/*.md` files before starting any task.

## Tasks

See [.agent/TASKS.md](../.agent/TASKS.md) for the development backlog.

## Key Decisions

- {{Decision 1 — rationale}}
- {{Decision 2 — rationale}}
- {{Decision 3 — rationale}}
```

**Why it's thin:** Claude auto-loads CLAUDE.md into every conversation. The fewer tokens here, the more context remains for actual work. The full rules are in `.ai/rules/` which Claude reads on demand.

---

### .github/copilot-instructions.md

GitHub Copilot loads this for all chat and agent interactions. Reference the shared rules.

```markdown
# Project Instructions

This project's coding conventions are in `.ai/rules/`. Read these files for complete guidance:

- `.ai/rules/project.md` — Project structure, module organization, dependencies
- `.ai/rules/code-style.md` — Formatting, naming, linting, type checking
- `.ai/rules/testing.md` — Test framework, conventions, coverage policy
- `.ai/rules/security.md` — Secrets, validation, auth, data protection
- `.ai/rules/quality.md` — Complexity limits, documentation requirements

## Quick Reference

| Action       | Command                    |
| ------------ | -------------------------- |
| Build        | `{{build command}}`        |
| Test         | `{{test command}}`         |
| Lint         | `{{lint command}}`         |
| Format       | `{{format command}}`       |
| Type check   | `{{type check command}}`   |
| Coverage     | `{{coverage command}}`     |

## Critical Rules

- Run the formatter before committing
- Zero linter warnings — fix, don't suppress
- All new code must have tests
- Coverage must not decrease
- Max function length: {{N}} lines
- Max file length: {{N}} lines

## Tasks

Development backlog is in `.agent/TASKS.md`.
```

---

### .github/instructions/ (path-specific, optional)

These apply only when Copilot is working on matching files. Use for domain-specific rules that reference the shared files.

**`.github/instructions/tests.instructions.md`:**

```markdown
---
applyTo: "**/*.test.*,**/*.spec.*,**/__tests__/**"
---

Follow the testing conventions in `.ai/rules/testing.md`.

Key rules:
- One behavior per test
- Test names: "should {behavior} when {condition}"
- Never mock business logic — only mock external I/O
- Bug fixes require regression tests
- Coverage must not decrease
```

**`.github/instructions/security.instructions.md`:**

```markdown
---
applyTo: "**/auth/**,**/middleware/**,**/security/**"
---

Follow the security rules in `.ai/rules/security.md`.

Key rules:
- Validate all inputs at the API boundary
- Never log tokens, passwords, or PII
- Use parameterized queries — never string interpolation
- All secrets via environment variables, accessed through config module
```

---

### .junie/guidelines.md

JetBrains Junie reads this. Reference the shared rules.

```markdown
# Project Guidelines

This project's coding conventions are maintained in `.ai/rules/`. Please read all files in that directory for complete guidance.

## Rules Location

- `.ai/rules/project.md` — Project structure, module organization, dependencies
- `.ai/rules/code-style.md` — Formatting, naming, linting, type checking
- `.ai/rules/testing.md` — Test framework, conventions, coverage policy
- `.ai/rules/security.md` — Secrets, validation, auth, data protection
- `.ai/rules/quality.md` — Complexity limits, documentation requirements

## Quick Reference

| Action       | Command                    |
| ------------ | -------------------------- |
| Build        | `{{build command}}`        |
| Test         | `{{test command}}`         |
| Lint         | `{{lint command}}`         |
| Format       | `{{format command}}`       |
| Type check   | `{{type check command}}`   |
| Coverage     | `{{coverage command}}`     |

## Critical Rules

- Run the formatter before committing
- Zero linter warnings — fix, don't suppress
- All new code must have tests
- Coverage must not decrease
- Max function length: {{N}} lines
- Max file length: {{N}} lines

## Tasks

Development backlog is in `.agent/TASKS.md`.
```

---

### .vscode/settings.json

VS Code settings that tell Copilot where to find instructions and configure editor behavior to match the shared rules.

```jsonc
{
  // ═══════════════════════════════════════════════
  //  GitHub Copilot — instruction file references
  // ═══════════════════════════════════════════════

  // Copilot Chat: use instruction files (auto-loads .github/copilot-instructions.md)
  "github.copilot.chat.codeGeneration.useInstructionFiles": true,

  // Copilot Chat: point review instructions at shared rules
  "github.copilot.chat.reviewSelection.instructions": [
    { "file": ".ai/rules/code-style.md" },
    { "file": ".ai/rules/quality.md" },
    { "file": ".ai/rules/security.md" }
  ],

  // Copilot Chat: point test generation at shared testing rules
  "github.copilot.chat.testGeneration.instructions": [
    { "file": ".ai/rules/testing.md" }
  ],

  // Copilot Chat: commit message instructions
  "github.copilot.chat.commitMessageGeneration.instructions": [
    {
      "text": "Use conventional commit format: <type>(<scope>): <subject>. Types: feat, fix, refactor, test, chore, docs, perf, ci. Keep subject under 72 chars."
    }
  ],

  // Copilot Chat: PR description instructions
  "github.copilot.chat.pullRequestDescriptionGeneration.instructions": [
    {
      "text": "Include sections: Changes, Quality (linter/formatter/types/complexity), Testing (new tests, coverage change), Verification (build/test pass)."
    }
  ],

  // ═══════════════════════════════════════════════
  //  Editor settings (match project conventions)
  // ═══════════════════════════════════════════════

  // These should match what's in .ai/rules/code-style.md
  // Adjust values to match your project:
  "editor.tabSize": 2,
  "editor.insertSpaces": true,
  "editor.formatOnSave": true,
  "editor.defaultFormatter": "esbenp.prettier-vscode",
  "editor.rulers": [100],

  // ═══════════════════════════════════════════════
  //  File associations and excludes
  // ═══════════════════════════════════════════════

  "files.exclude": {
    "**/.DS_Store": true,
    "**/node_modules": true
  },

  "search.exclude": {
    "**/node_modules": true,
    "**/coverage": true,
    "**/dist": true
  }
}
```

> **Note on `testGeneration` and `codeGeneration` settings:** These settings are deprecated as of VS Code 1.102+ in favor of `.instructions.md` files. If you're on an older version, the `settings.json` approach works. On newer versions, use the `.github/instructions/` approach instead — both are included here for compatibility.

---

### .idea/ (JetBrains — optional)

JetBrains IDE settings are stored in `.idea/`. Unlike VS Code, most teams **gitignore** `.idea/` because it contains user-specific settings. However, some files are safe to share.

If your team uses JetBrains IDEs and wants shared settings:

**`.idea/` files safe to commit:**

| File | Purpose | Commit? |
|------|---------|---------|
| `.idea/codeStyles/` | Shared code style settings | ✅ Yes |
| `.idea/inspectionProfiles/` | Shared inspection rules | ✅ Yes |
| `.idea/fileTemplates/` | Shared file templates | ✅ Yes |
| `.idea/dictionaries/` | Shared dictionaries | ✅ Yes |
| `.idea/*.xml` (project-level) | Encoding, VCS settings | ⚠️ Selective |
| `.idea/workspace.xml` | User-specific layout | ❌ Never |
| `.idea/tasks.xml` | User-specific tasks | ❌ Never |
| `.idea/usage.statistics.xml` | Telemetry | ❌ Never |
| `.idea/shelf/` | User-specific shelved changes | ❌ Never |

**Junie settings** in `.idea/` are configured through the IDE Settings UI (`Settings > Tools > Junie > Project Settings`) and are stored in user-specific XML. Instead of trying to share these, just commit the `.junie/guidelines.md` file — Junie reads it automatically.

If your team wants to share JetBrains code style settings that match the project conventions, you can export them:

1. Open `Settings > Editor > Code Style`
2. Click the gear icon → `Export > IntelliJ IDEA code style XML`
3. Save to `.idea/codeStyles/Project.xml`
4. Commit this file

---

## How ANALYZE.md Changes

The ANALYZE.md analysis process needs one adjustment: **generate files to `.ai/rules/` instead of `.claude/rules/`**, and generate the thin pointer files.

### Phase 3 output changes

| Before (single-tool) | After (multi-agent) |
|----------------------|---------------------|
| `.claude/rules/project.md` | `.ai/rules/project.md` |
| `.claude/rules/code-style.md` | `.ai/rules/code-style.md` |
| `.claude/rules/testing.md` | `.ai/rules/testing.md` |
| `.claude/rules/security.md` | `.ai/rules/security.md` |
| `.claude/rules/quality.md` | `.ai/rules/quality.md` |
| `.claude/CLAUDE.md` (full) | `.claude/CLAUDE.md` (thin pointer) |
| — | `AGENTS.md` (universal entry) |
| — | `.github/copilot-instructions.md` (thin pointer) |
| — | `.junie/guidelines.md` (thin pointer) |
| — | `.vscode/settings.json` |

### What the agent generates

When running the analysis, the agent should:

1. Generate canonical rules in `.ai/rules/` (same content as before)
2. Generate `AGENTS.md` at root (concise overview + links to rules)
3. Generate `.claude/CLAUDE.md` (thin — overview + links to `.ai/rules/`)
4. Generate `.github/copilot-instructions.md` (thin — overview + links to `.ai/rules/`)
5. Generate `.github/instructions/*.instructions.md` (path-specific, optional)
6. Generate `.junie/guidelines.md` (thin — overview + links to `.ai/rules/`)
7. Generate `.vscode/settings.json` (editor settings + Copilot instruction references)
8. Generate `.agent/` task files (unchanged)

---

## Handling Tool Limitations

Some tools may not follow file references well. Here's how to handle that:

### "Copilot doesn't read the .ai/rules/ files I referenced"

Copilot's ability to follow references depends on the feature:

- **Copilot Chat (VS Code):** Can follow file references if using `settings.json` `file` pointers
- **Copilot Coding Agent:** Can read files from the repo — references work well
- **Copilot Code Review:** Supports references in `.instructions.md` files

If you find Copilot ignoring references, you have two options:

1. **Inline the critical rules** directly in `.github/copilot-instructions.md` (accept some duplication for the most important rules)
2. **Use path-specific instructions** in `.github/instructions/` with the most critical rules inlined

The recommended approach: keep a short set of **critical rules** (10-15 lines) inlined in `copilot-instructions.md`, and reference `.ai/rules/` for the full details. This way, even if Copilot doesn't follow the references, it still has the most important guardrails.

### "Junie doesn't follow the references"

Junie's `guidelines.md` supports direct content. If Junie doesn't follow file references well, you can inline the content. Since `.junie/guidelines.md` is Junie-only, duplicating content there is less risky — it only affects one tool.

### Fallback: hybrid approach

If reference-following is unreliable for your tools, use a hybrid:

```
.ai/rules/           ← Canonical source of truth (edit here)
.claude/CLAUDE.md    ← Thin pointer (works — Claude reads files well)
.github/copilot-instructions.md  ← Inline critical rules + reference .ai/rules/
.junie/guidelines.md             ← Inline full content (copy from .ai/rules/)
AGENTS.md            ← Inline overview + reference .ai/rules/
```

To keep `.junie/guidelines.md` in sync if you inline, add a comment at the top:

```markdown
<!-- AUTO-SYNCED from .ai/rules/ — do not edit directly. Run: cat .ai/rules/*.md > .junie/guidelines.md -->
```

And add a simple sync script:

```bash
#!/bin/bash
# sync-rules.sh — copies canonical rules to tool-specific locations
echo "# Project Guidelines" > .junie/guidelines.md
echo "" >> .junie/guidelines.md
echo "<!-- AUTO-SYNCED from .ai/rules/ — do not edit directly -->" >> .junie/guidelines.md
echo "" >> .junie/guidelines.md
for f in .ai/rules/*.md; do
  cat "$f" >> .junie/guidelines.md
  echo -e "\n---\n" >> .junie/guidelines.md
done
echo "✓ Synced .ai/rules/ → .junie/guidelines.md"
```

---

## .gitignore Additions

```gitignore
# JetBrains — user-specific files
.idea/workspace.xml
.idea/tasks.xml
.idea/usage.statistics.xml
.idea/shelf/
.idea/httpRequests/
.idea/dataSources.local.xml

# Keep these JetBrains files if sharing code style:
# !.idea/codeStyles/
# !.idea/inspectionProfiles/
```

---

## Summary

| Concern | Solution |
|---------|----------|
| Rules duplication | Single source in `.ai/rules/` |
| Claude needs auto-loaded context | `.claude/CLAUDE.md` thin pointer |
| Copilot needs instructions | `.github/copilot-instructions.md` thin pointer |
| Copilot needs path-specific rules | `.github/instructions/*.instructions.md` |
| Junie needs guidelines | `.junie/guidelines.md` thin pointer |
| Generic agents need instructions | `AGENTS.md` at root |
| VS Code needs editor settings | `.vscode/settings.json` |
| JetBrains needs code style | `.idea/codeStyles/` (optional) |
| Tool can't follow references | Inline critical rules + sync script |
| Task management | `.agent/` directory (unchanged, tool-agnostic) |

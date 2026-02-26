# Read Project

## First: Identify the Stack

Before reading files, determine:

- **Primary language(s):** Check file extensions, package manifests
- **Framework(s):** Check dependencies (Express, Django, Spring Boot, Next.js, etc.)
- **Runtime version:** Check `.node-version`, `.python-version`, `go.mod`, `rust-toolchain.toml`
- **Monorepo?** Check for `workspaces` in package.json, `pnpm-workspace.yaml`, Nx/Turborepo config, or multiple `go.mod` files

If it's a monorepo, identify the packages/apps and decide whether to analyze the whole repo or a specific package. Ask the user if unclear.

## Determine Project State

Check whether this is an existing codebase or a documentation-first project:

- **Existing codebase:** Source files exist in `src/`, `lib/`, `app/`, etc. → follow "What to Read" below
- **Documentation-first (new project):** No source code (or only boilerplate), but feature docs, technical docs, or architecture docs exist → follow "Documentation-First Projects" below
- **Hybrid:** Some source code exists alongside docs describing planned work → read both, treat existing code as the existing codebase path and unimplemented features as the documentation-first path

## Documentation-First Projects

When no source code exists but documentation does, read the docs to understand what needs to be built.

### What to Read

1. **Feature document** — requirements, user stories, acceptance criteria, user flows, wireframes/mockups descriptions
2. **Technical document** — stack choices, architecture decisions, API design, database schema, auth approach, infrastructure
3. **Any existing scaffolding** — `package.json`, config files, CI templates, even if no source code yet
4. **External references** — linked API docs, design systems, third-party service docs mentioned in the technical doc

### What to Extract

From the **feature document:**

- List of features/user stories with acceptance criteria
- User roles and permissions
- Data entities and relationships
- Integration points (third-party APIs, payment, email, etc.)
- Priority or phasing if documented (MVP vs later)

From the **technical document:**

- Language, framework, runtime versions
- Architecture pattern (monolith, microservices, serverless, etc.)
- Database choice and schema design
- API design (REST, GraphQL, endpoints, versioning)
- Authentication and authorization approach
- Hosting/deployment target
- CI/CD approach
- Testing strategy (if documented)
- Any specific libraries or tools chosen

### What to Infer

If the technical doc specifies a stack but not conventions, derive sensible defaults:

| Decision in Doc        | Derive                                                          |
| ---------------------- | --------------------------------------------------------------- |
| "TypeScript + Express" | camelCase, ESLint + Prettier, Jest/Vitest, `src/` structure     |
| "Python + FastAPI"     | snake_case, Ruff/Black, pytest, `app/` structure                |
| "Go + Chi"             | Go conventions, golangci-lint, `cmd/` + `internal/` structure   |
| "Next.js + App Router" | feature-based `app/` dirs, React conventions, Tailwind patterns |

Mark these as `> **Derived from stack choice.** Adjust if the team has different preferences.` rather than `> **No established convention.**`

### Architecture Mapping

Answer the same questions as for existing codebases, but based on documentation:

1. **What is the module structure?** — from the technical doc's architecture section
2. **How does data flow?** — from API design and database schema
3. **What are the boundaries?** — from documented service/module boundaries
4. **What's the deployment model?** — from infrastructure section
5. **What external services exist?** — from integration points in feature doc
6. **What are the key design decisions?** — explicitly documented in technical doc

Then continue to Phase 1.2 (Quality Tooling Inventory) — for documentation-first projects, most tools will be "missing" which becomes the first wave of setup tasks.

## What to Read

Read every file that influences project behavior or conventions. Prioritize in this order:

### 0. Exclusions

Check `.gitignore`, `.dockerignore`, and build output directories first. Skip reading:

- `node_modules/`, `vendor/`, `venv/`, `.venv/`, `target/`, `dist/`, `build/`, `out/`
- Generated files (`.generated.*`, lock files contents, compiled output)
- Binary files, images, fonts

### 1. Entry Points and Configuration

- Package manifests: `package.json`, `Cargo.toml`, `pyproject.toml`, `go.mod`, `pom.xml`, `build.gradle`
- Lock files: check existence, don't read contents
- Config files: `tsconfig.json`, `.eslintrc.*`, `.prettierrc`, `jest.config.*`, `vitest.config.*`
- Environment: `.env.example`, `.env.template` (never `.env`)
- CI/CD: `.github/workflows/`, `.gitlab-ci.yml`, `Jenkinsfile`, `Dockerfile`, `docker-compose.yml`

### 2. Documentation

- `README.md`, `CONTRIBUTING.md`, `ARCHITECTURE.md`
- Any existing `CLAUDE.md`, `AGENTS.md`, `copilot-instructions.md`, `.junie/guidelines.md`
- API specs: `openapi.yaml`, `swagger.json`, `.graphql` schemas
- Database schemas: migration files, ORM schema definitions

### 3. Source Code

- Directory structure (full tree, 2-3 levels deep)
- All source files — read for patterns, not memorization
- Focus on: module boundaries, import patterns, naming conventions, error handling

### 4. Tests

- Test directory structure and naming conventions
- Test framework configuration
- A sample of test files to understand patterns (assertion style, mocking approach, fixture usage)

### 5. Quality Tooling Inventory

Record what exists:

| Tool             | Config File        | Command     | Status                 |
| ---------------- | ------------------ | ----------- | ---------------------- |
| Formatter        | {{path or "none"}} | {{command}} | {{configured/missing}} |
| Linter           | {{path or "none"}} | {{command}} | {{configured/missing}} |
| Type checker     | {{path or "none"}} | {{command}} | {{configured/missing}} |
| Test runner      | {{path or "none"}} | {{command}} | {{configured/missing}} |
| Coverage         | {{path or "none"}} | {{command}} | {{configured/missing}} |
| Bundler          | {{path or "none"}} | {{command}} | {{configured/missing}} |
| Pre-commit hooks | {{path or "none"}} | —           | {{configured/missing}} |

### 6. AI Tool Detection

Check for existing AI tool configuration files. These will be pre-selected in the Checkpoint when asking the user which tools to generate pointer files for.

| Tool            | Check for                                                    | Status            |
| --------------- | ------------------------------------------------------------ | ----------------- |
| Claude Code     | `.claude/CLAUDE.md` or `.claude/` directory                  | {{found/missing}} |
| GitHub Copilot  | `.github/copilot-instructions.md` or `.github/instructions/` | {{found/missing}} |
| JetBrains Junie | `.junie/guidelines.md` or `.junie/` directory                | {{found/missing}} |
| Cursor          | `.cursor/rules/` or `.cursorrules`                           | {{found/missing}} |
| Aider           | `.aider.conf.yml` or `CONVENTIONS.md` (Aider convention)     | {{found/missing}} |

Also check for:

- `AGENTS.md` at root (universal entry point, always regenerated)
- `.ai/rules/` directory (existing shared rules from a prior run)
- `.editorconfig` (universal editor formatting)

Record which tools are already configured — these will be pre-selected when asking the user which tools to generate files for in the Checkpoint.

## How to Map Architecture

Answer these questions:

1. **What is the module structure?** — flat, layered (controller/service/repo), feature-based, domain-driven?
2. **How does data flow?** — request → handler → service → database? event-driven? message queue?
3. **What are the boundaries?** — what depends on what? what's shared vs isolated?
4. **What's the deployment model?** — monolith, microservices, serverless, static site?
5. **What external services exist?** — databases, APIs, message queues, caches, CDNs?
6. **What are the key design decisions?** — why this framework, this ORM, this auth strategy?

## Large Codebase Strategy

If the project has more than ~500 source files or reading all files would exceed practical limits:

1. **Read all of Priority 0-2** (exclusions, entry points, configs, docs) — these are small and essential
2. **Sample Priority 3 (source code):**
   - Read the full directory tree (structure only, not file contents)
   - Read entry points and main modules fully
   - For each directory/module, read 2-3 representative files
   - Prioritize files with the most imports (they are central to the architecture)
   - Skip test fixtures, generated code, and vendored dependencies
3. **Sample Priority 4 (tests):** Read test config and 3-5 test files across different modules
4. **Read all of Priority 5-6** (quality tooling inventory, AI tool detection) — these are file-existence checks
5. **Note limitations:** In the Checkpoint, tell the user which modules were sampled vs fully read, so they can flag anything important that was missed

The goal is to capture the architecture and conventions accurately, not to read every line of code.

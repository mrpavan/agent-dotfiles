---
name: init
description: First-time codebase analysis that produces a project intelligence file (AGENTS.md) for AI coding agents. Use when the user runs /init or asks to "init the project", "generate AGENTS.md", or "create project intelligence for agents".
---

# Init: Project Intelligence for AI Agents

When invoked (e.g. via `/init`), act as a senior engineer performing a **first-time analysis** of the codebase. Scan the repository thoroughly and generate an **`AGENTS.md`** file in the **project root**. This file is persistent context for AI coding agents (Claude, Cursor, Copilot, etc.). Aim for **CLAUDE.md-style** output: concise, copy-paste ready, with a **folder tree**, **Commands table**, and **Key Files** so agents can run the project and find the right files without guessing. Produce enough detail on the first run so a second pass is not needed.

## What to Do

1. **Scan the entire repository** using read_file, list_dir (or `find`/`ls` in key dirs), grep, and codebase_search as needed.
2. **Verify facts only** from lock files, manifests, config files, and source code. Do not invent library names, commands, or structure.
3. **Write exactly one file**: `AGENTS.md` in the repository root, using the structure below.

## Style (CLAUDE.md-Aligned)

- **Commands**: Use a **markdown table** (`| Command | Description |`) so the agent can run install, dev, test, lint, build without guessing. Include the exact command to run a single test if the project has one.
- **Architecture**: Include a **folder tree** as a fenced **text** block (e.g. ` ```text `). Show directory structure 2–3 levels deep; add one-line comments only for directories with non-obvious purpose (e.g. `# API calls and business logic`). This answers “where does this file go?”
- **Key Files**: A **short list** of entry points and critical files (bootstrap, root config, main routes, base HTTP service, env/config file, test config). Enables the agent to open the right file first.
- **Conciseness**: Prefer bullets and tables over long prose. Project-specific only; skip generic best practices. Every line should be actionable or copy-paste ready. Aim to stay under ~300 lines; shorter is better when the essentials are covered.

## Depth of Scan (First Run)

- **Routes**: Root router config; follow `loadChildren`/`loadComponent` to layout and feature routes; list path segments and guards/feature flags; summarize in Architecture (and optionally in the tree).
- **Core / shared**: Base HTTP service and main methods (e.g. getData/postData); request/response model folders; interceptors (auth, error, loading) and what they do.
- **Shared UI**: List shared component directories or key component names (table, pagination, filter, dialog, button, input, etc.).
- **Config**: Where env vars and API base URLs live; central constants (error codes, debounce, date formats).
- **Project rules**: If `.cursor/rules/*.mdc` (or similar) exist, list them and reference in Code Style and Agent Instructions.
- **Tests**: Runner, config file, spec location, command for single spec.
- **File map**: “Where to add what” (new feature → path, new shared component → path, new guard → path, etc.).

## AGENTS.md Structure (Use This)

Output the Markdown file starting with `# Project Intelligence`. Follow this structure (section order and required elements):

```markdown
---
# Project Intelligence: <Project Name>

<One short paragraph or 2–3 bullets: what the project does, main product areas if app, tech stack and runtime.>

## Commands

| Command | Description |
|---------|-------------|
| `<install>` | Install dependencies (note any postinstall) |
| `<dev/start>` | Start dev server (port if fixed) |
| `<test>` | Run unit tests |
| `<test single>` | Run single spec/file if applicable |
| `<lint>` | Lint |
| `<build>` | Build (output path if relevant) |

## Architecture

### Folder tree

\`\`\`text
<root>/
  <src or app>/
    <main dirs with 1-line comment only when non-obvious>
  <shared lib or components>/
  <config/env dir>/
\`\`\`

- **Entry**: <e.g. index.html → main.ts → app.config → AppComponent>
- **Routes**: <top-level segments; which use guards/feature flags; wildcard/redirect>

## Key Files

- `<bootstrap/entry>` — <one line>
- `<root config or app config>` — <one line>
- `<main routes file>` — <one line>
- `<base HTTP or API service>` — <one line>
- `<env/config file>` — <one line>
- `<build/test config>` — <one line>
- <any critical gotcha in config, e.g. launcher typo>

## Code Style

- Naming, prefix, spec placement.
- State management; auth (guards, redirect).
- API: preferred service, request/response pattern, pagination.
- Listings/tables: pattern and .cursor rule reference if present.
- Node/diagram UIs: library and rule reference if present.
- Dialogs, errors, import style (path aliases).

## Gotchas

- Config typos, single env file, large files to avoid, build/deploy quirks, feature flags.

## Environment

- Where config lives (.env vs env file); required vars or keys; auth/API bases; no secrets in repo.

## File map (where to add what)

- New feature → path; new API/client → path; new shared component → path; new guard/interceptor → path; constants; project rules path.

## Agent Instructions

- Use base service / shared components / .cursor rules when relevant; do not auto-edit lockfile or commit secrets; after changes run lint and test; single spec command.
---
```

## Rules (Mandatory)

- **Be factual.** Only include what you can verify from the repo.
- **Folder tree**: Always include a fenced ` ```text ` tree under Architecture; 2–3 levels; one-line comments only for non-obvious dirs.
- **Commands**: Always in table form. Include install, dev/start, test, lint, build; add single-test command if the project supports it.
- **Key Files**: Always a short list (5–10 items) of entry points and critical files.
- **Project name:** Infer from package.json, README, or repo directory name.
- **Versions:** Only from lock files or manifests.
- **Paths and commands:** Only those that exist in scripts, README, or config.

## Scan Checklist (Before Writing)

- [ ] Root: package.json (name, scripts), README, lockfile, build config, Dockerfile if any.
- [ ] Entry: index.html, main entry, bootstrap; baseUrl/paths.
- [ ] Routes: root routes; follow loadChildren to layout and one level of feature routes; guards and flags.
- [ ] Core: base HTTP service, request/response models, interceptors, guards, constants.
- [ ] Feature modules: list and route segments.
- [ ] Shared UI: list component dirs or key components.
- [ ] Environment: env file or .env.example; API URLs, feature flags.
- [ ] Tests: config file, spec pattern, single-spec command.
- [ ] Lint: script and config.
- [ ] .cursor/rules: list and reference in Code Style / Agent Instructions.

Then write `AGENTS.md` to the repository root. The result should let an agent run the project, find the right files, and follow project conventions without a second init pass.

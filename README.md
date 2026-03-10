# Project Scaffold

A modern TypeScript project scaffold with pre-configured tooling for linting, formatting, git hooks, CI/CD, and AI assistant integration.

## Table of Contents

- [Quick Start](#quick-start)
- [What's Included](#whats-included)
- [Customizing for Your Project](#customizing-for-your-project)
- [Development Commands](#development-commands)
- [Configuration Files](#configuration-files)
- [Git Hooks System](#git-hooks-system)
- [AI Assistant Integration](#ai-assistant-integration)
- [CI/CD Pipeline](#cicd-pipeline)
- [Project Structure](#project-structure)
- [Promote to Turborepo Monorepo](#promote-to-turborepo-monorepo)

---

## Quick Start

```bash
# Clone the scaffold
git clone <this-repo-url> my-new-project
cd my-new-project

# Remove existing git history and start fresh
rm -rf .git
git init

# Install dependencies (hooks are auto-installed)
pnpm install

# Initialize Open Agent Toolkit (use `npx oat` if not installed globally)
oat init --scope project
oat tools install
oat sync --scope all

# Verify everything works
pnpm lint
pnpm type-check
pnpm build
```

---

## What's Included

### Core Tooling

| Tool            | Version | Purpose                                         |
| --------------- | ------- | ----------------------------------------------- |
| **TypeScript**  | 5.8.3   | Type-safe JavaScript with modern ES2022 target  |
| **oxlint**      | 1.51.0  | Fast linting                                    |
| **oxfmt**       | 0.36.0  | Fast formatting                                 |
| **tsx**         | 4.21.0  | Direct TypeScript execution without compilation |
| **tsc-alias**   | 1.8.10  | Path alias resolution in compiled output        |
| **CommitLint**  | 19.8.1  | Conventional commit message enforcement         |
| **lint-staged** | 15.2.11 | Run linters on staged files only                |

### Pre-configured Features

- **Git Hooks**: Pre-commit linting, commit message validation, pre-push checks
- **CI Workflow**: GitHub Actions for lint, type-check, and build on PRs
- **Editor Config**: VSCode settings with oxc as default formatter
- **AI Assistants**: Open Agent Toolkit (OAT) with Claude, Cursor, and Codex provider sync
- **Project Tracking**: OAT project lifecycle for structured multi-session development

### Requirements

- **Node.js**: >= 22.17.0
- **pnpm**: >= 10.13.1

---

## Customizing for Your Project

### 1. Update package.json

```json
{
  "name": "your-project-name",
  "description": "Your project description",
  "version": "0.1.0"
}
```

### 2. Update CLAUDE.md

This file provides context to Claude Code. Update it to describe your project:

```markdown
# CLAUDE.md

This file provides guidance to Claude Code when working with this repository.

## Project Overview

<!-- Describe what your project does -->

## Development Commands

<!-- List your main commands -->

## Architecture

<!-- Describe your project structure and key decisions -->
```

### 3. Configure GitHub Repository

1. Push to your new GitHub repository
2. The CI workflow will automatically run on PRs to `main`
3. Optionally update `.github/PULL_REQUEST_TEMPLATE.md` for your team

### 4. Optional: Customize TypeScript Config

The `tsconfig.json` includes strict settings. Adjust if needed:

```json
{
  "compilerOptions": {
    // Disable if too strict for your use case
    "noUncheckedIndexedAccess": false,
    "noPropertyAccessFromIndexSignature": false
  }
}
```

### 5. Optional: Add Path Aliases

To use path aliases like `@/utils`:

```json
// tsconfig.json
{
  "compilerOptions": {
    "baseUrl": ".",
    "paths": {
      "@/*": ["src/*"]
    }
  }
}
```

The `tsc-alias` tool will resolve these in the compiled output.

---

## Development Commands

### Building & Running

```bash
pnpm build        # Compile TypeScript to dist/ with alias resolution
pnpm clean        # Remove dist directory
pnpm type-check   # Run TypeScript type checking (no emit)
```

### Linting & Formatting

```bash
pnpm lint         # Check for lint errors
pnpm lint:fix     # Fix lint errors automatically
pnpm format       # Check code formatting
pnpm format:fix   # Format code automatically
```

### Git Hooks Management

```bash
pnpm hooks:status       # Show which hooks are enabled
pnpm hooks:enable-all   # Enable all hooks
pnpm hooks:disable-all  # Disable all hooks
pnpm hooks enable <hook>   # Enable specific hook
pnpm hooks disable <hook>  # Disable specific hook
```

### Running TypeScript Directly

```bash
npx tsx src/index.ts           # Run without compilation
npx tsx watch src/index.ts     # Run with hot reload
```

---

## Configuration Files

### .oxlintrc.json - Linting

oxlint provides fast linting with ESLint-compatible rules.

**Key Settings:**

- Categories-based config (`correctness` + `suspicious` enable ~260 rules)
- TypeScript plugin for TS-specific rules
- Test file exceptions (e.g., `no-explicit-any` allowed in test files)
- Auto-fix on save (via VSCode settings)

### .oxfmtrc.json - Formatting

oxfmt provides fast code formatting.

**Key Settings:**

- 2-space indentation, single quotes, trailing commas
- 80 character line width
- Automatic import sorting
- Consistent formatting across TypeScript, JSON, and Markdown

### tsconfig.json - TypeScript

**Compiler Options:**

- ES2022 target with ESNext modules
- Bundler module resolution
- Strict mode with additional checks:
  - `noUncheckedIndexedAccess` - Safer array/object access
  - `noImplicitOverride` - Explicit override keyword required
  - `noImplicitReturns` - All code paths must return
  - `verbatimModuleSyntax` - Explicit import/export types

**Output:**

- Compiles to `dist/`
- Generates `.d.ts` declaration files
- Incremental compilation enabled

**tsc-alias Integration:**

```json
{
  "tsc-alias": {
    "verbose": false,
    "resolveFullPaths": true,
    "resolveFullExtension": ".js"
  }
}
```

### commitlint.config.js - Commit Messages

Enforces [Conventional Commits](https://www.conventionalcommits.org/) format:

```
type(scope): subject

feat(api): add user authentication
fix(ui): resolve button alignment issue
docs: update README with examples
```

**Allowed Types:** feat, fix, docs, style, refactor, perf, test, build, ci, chore, revert

### .lintstagedrc.mjs - Pre-commit Checks

Runs on staged files before each commit:

```javascript
export default {
  '*.{ts,tsx,js,jsx}': ['oxlint --fix', 'oxfmt --write'],
  '*.json': ['oxfmt --write'],
  '*.md': ['oxfmt --write'],
};
```

### .nvmrc - Node Version

Specifies Node.js 22.17.0. Use with nvm:

```bash
nvm use  # Automatically uses version from .nvmrc
```

---

## Git Hooks System

Located in `tools/git-hooks/`, this system provides automated code quality checks.

### Available Hooks

| Hook              | Trigger                      | Action                                         |
| ----------------- | ---------------------------- | ---------------------------------------------- |
| **pre-commit**    | Before commit                | Runs lint-staged on staged files               |
| **commit-msg**    | After writing commit message | Validates conventional commit format           |
| **pre-push**      | Before push                  | Runs full lint suite                           |
| **post-checkout** | After branch switch          | Auto-installs dependencies if lockfile changed |

### Automatic Installation

Hooks are automatically installed when you run `pnpm install` via the `prepare` script.

### Disabling Hooks

**Temporarily (single command):**

```bash
git commit --no-verify -m "skip hooks"
```

**For CI/Docker environments:**

```bash
GIT_HOOKS=0 pnpm install
```

**Disable specific hook:**

```bash
pnpm hooks disable pre-push
```

### How It Works

The `manage-hooks.js` script creates symlinks from `.git/hooks/` to `tools/git-hooks/`. This approach:

- Keeps hooks in version control
- Allows easy enable/disable without deleting files
- Tracks intentionally disabled hooks in `.git/hooks/.disabled-hooks`

---

## AI Assistant Integration

This scaffold uses **Open Agent Toolkit (OAT)** for AI assistant integration across multiple providers.

### How It Works

- **Canonical skills** live in `.agents/skills/` — the single source of truth
- **Provider sync** via `oat sync --scope all` generates provider-specific views (`.claude/skills/`, `.cursor/skills/`, etc.)
- **Supported providers**: Claude Code, Cursor, Codex

### Project Lifecycle

OAT provides structured skills for multi-session development:

| Skill                     | Purpose                                            |
| ------------------------- | -------------------------------------------------- |
| `oat-project-new`         | Start a new project with discovery/planning phases |
| `oat-project-quick-start` | Quick-start a project with minimal setup           |
| `oat-project-open`        | Resume an existing project                         |
| `oat-project-progress`    | Check project progress and status                  |
| `oat-project-pr-final`    | Generate a comprehensive PR description            |

Projects are stored in `.oat/projects/` with structured artifacts.

### Local Path Management

OAT manages **local-only paths** — directories that are gitignored but need to persist locally (and sync to worktrees). Common examples:

- `.oat/ideas/` — project-level idea brainstorming
- `.oat/projects/**/reviews` — review artifacts scoped to specific projects

Configure them in `.oat/config.json`:

```json
{
  "version": 1,
  "localPaths": [".oat/ideas", ".oat/projects/**/reviews"]
}
```

Then apply to `.gitignore`:

```bash
oat local apply             # Write managed .gitignore section for local paths
oat local status            # Check which paths exist and are gitignored
oat local add <path>        # Add a new local path
oat local remove <path>     # Remove a local path
```

When using git worktrees, `oat local sync <worktree-path>` copies local paths between the main repo and worktrees automatically.

### Key Commands

```bash
oat init --scope project    # Initialize OAT in a new project
oat tools install           # Install/update skill packs
oat sync --scope all        # Sync skills to all providers
oat status --scope all      # Check sync status
```

---

## CI/CD Pipeline

### GitHub Actions Workflow

Located at `.github/workflows/ci.yml`:

```yaml
name: CI
on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  ci:
    runs-on: ubuntu-latest
    steps:
      - Checkout
      - Setup pnpm
      - Setup Node.js (from .nvmrc)
      - Install dependencies
      - Run: pnpm lint
      - Run: pnpm type-check
      - Run: pnpm build
```

**Triggers:**

- Push to `main` branch
- Pull requests targeting `main`

### PR Template

`.github/PULL_REQUEST_TEMPLATE.md` provides a standard structure:

```markdown
## Purpose

<!-- Brief description -->

## Changes

- [ ] Change 1
- [ ] Change 2

## Testing

- [ ] Unit tests
- [ ] Manual testing

## Notes

<!-- Additional context -->
```

---

## Project Structure

```
.
├── src/                          # Source code
│   └── index.ts                  # Entry point
├── dist/                         # Compiled output (gitignored)
├── tools/
│   └── git-hooks/                # Git hook scripts
│       ├── manage-hooks.js       # Hook management CLI
│       ├── pre-commit            # Lint staged files
│       ├── commit-msg            # Validate commit message
│       ├── pre-push              # Full lint before push
│       └── post-checkout         # Auto-install deps
├── .agents/                      # OAT canonical skills & agents
│   ├── skills/                   # Skill definitions (source of truth)
│   └── agents/                   # Agent definitions
├── .oat/                         # OAT local state (mostly gitignored)
├── .claude/                      # Claude Code config
│   ├── settings.json
│   └── skills/                   # OAT-synced skills (auto-generated)
├── .cursor/                      # Cursor AI config
│   └── skills/                   # OAT-synced skills (auto-generated)
├── .github/
│   ├── workflows/ci.yml          # CI pipeline
│   └── PULL_REQUEST_TEMPLATE.md
├── .vscode/
│   └── settings.json             # Editor config
├── .oxlintrc.json                # Linting config
├── .oxfmtrc.json                 # Formatting config
├── tsconfig.json                 # TypeScript config
├── commitlint.config.js          # Commit message rules
├── .lintstagedrc.mjs             # Pre-commit checks
├── .nvmrc                        # Node version
├── package.json
├── pnpm-lock.yaml
├── AGENTS.md                     # Agent instructions (OAT skills discovery)
├── CLAUDE.md                     # Claude Code context (references AGENTS.md)
└── README.md                     # This file
```

---

## Promote to Turborepo Monorepo

Guide for converting a `project-scaffold` instance into a Turborepo monorepo with pnpm workspaces.

### Prerequisites

- Node >= 22.17.0
- pnpm >= 10.13.1
- A project scaffolded from `project-scaffold`

### Steps

#### 1. Install Turbo

```bash
pnpm add -D turbo
```

#### 2. Create `pnpm-workspace.yaml`

```yaml
packages:
  - 'apps/*'
  - 'packages/*'
```

#### 3. Create `turbo.json`

```jsonc
{
  "$schema": "https://turbo.build/schema.json",
  "tasks": {
    "build": {
      "dependsOn": ["^build"],
      "outputs": ["dist/**"],
    },
    "check": {
      "dependsOn": ["^build"],
    },
    "check:fix": {
      "dependsOn": ["^build"],
    },
    "dev": {
      "cache": false,
      "persistent": true,
      "dependsOn": ["^build"],
    },
    "test": {
      "dependsOn": ["build"],
      "outputs": [],
    },
    "lint": {
      "dependsOn": ["^build"],
    },
    "lint:fix": {
      "dependsOn": ["^build"],
    },
    "format": {
      "dependsOn": ["^build"],
    },
    "format:fix": {
      "dependsOn": ["^build"],
    },
    "type-check": {
      "dependsOn": ["^build"],
    },
    "clean": {
      "cache": false,
    },
  },
}
```

#### 4. Create workspace directories

```bash
mkdir -p apps packages
touch apps/.gitkeep packages/.gitkeep
```

#### 5. Update root `package.json`

Rename the package and replace scripts with turbo-delegated versions.

**Before:**

```json
{
  "name": "project-scaffold",
  "scripts": {
    "build": "tsc && tsc-alias",
    "clean": "rm -rf dist",
    "format": "oxfmt --check .",
    "format:fix": "oxfmt --write .",
    "lint": "oxlint .",
    "lint:fix": "oxlint --fix .",
    "test": "echo \"No tests configured\" && exit 0",
    "type-check": "tsc --noEmit"
  }
}
```

**After:**

```json
{
  "name": "your-project-name",
  "scripts": {
    "build": "turbo run build",
    "check": "turbo run check",
    "check:fix": "turbo run check:fix",
    "clean": "turbo run clean",
    "dev": "turbo run dev",
    "format": "turbo run format",
    "format:fix": "turbo run format:fix",
    "lint": "turbo run lint",
    "lint:fix": "turbo run lint:fix",
    "test": "turbo run test",
    "type-check": "turbo run type-check"
  }
}
```

Keep these scripts unchanged (they are root-level tooling, not per-package):

- `hooks`, `hooks:disable-all`, `hooks:enable-all`, `hooks:status`
- `prepare`

#### 6. Update root `tsconfig.json`

Convert to a **base config** that workspace packages will extend. Remove single-package fields:

- Remove `outDir` and `rootDir`
- Remove `declaration` and `declarationMap`
- Remove `include`

Keep everything else (`compilerOptions` strictness, `tsc-alias`, `exclude`).

Each workspace package will extend the root config:

```json
{
  "extends": "../../tsconfig.json",
  "compilerOptions": {
    "outDir": "dist",
    "rootDir": "src",
    "declaration": true,
    "declarationMap": true
  },
  "include": ["src/**/*"]
}
```

#### 7. Update `.gitignore`

Add the Turborepo cache directory:

```gitignore
# turborepo
.turbo/
```

#### 8. Remove scaffold boilerplate

```bash
rm -rf src/ dist/
```

These only exist as placeholder content from the scaffold. Actual source code will live inside `apps/` and `packages/`.

#### 9. Reinstall dependencies

```bash
rm -rf node_modules pnpm-lock.yaml
pnpm install
```

### Verification

After completing the steps:

- `pnpm turbo build` runs without errors (no packages yet, so no-op)
- `pnpm turbo lint` runs without errors
- Git hooks still work (commit, lint-staged, commitlint)
- `pnpm-workspace.yaml` exists and points to `apps/*` and `packages/*`

### Adding workspace packages

Once the monorepo is set up, add packages under `apps/` or `packages/`:

```
apps/       # deployable applications (servers, CLIs)
packages/   # shared libraries, adapters, types
```

Each package needs at minimum:

- `package.json` with a unique `name` and its own `scripts`
- `tsconfig.json` extending `../../tsconfig.json`
- `src/` directory

---

## Troubleshooting

### Hooks not running

```bash
# Check hook status
pnpm hooks:status

# Re-enable all hooks
pnpm hooks:enable-all
```

### oxc not formatting on save

Ensure you have the oxc VSCode extension installed:

```bash
code --install-extension oxc.oxc-vscode
```

### TypeScript path aliases not resolving

Make sure you're running `pnpm build` (not just `tsc`) to include the `tsc-alias` step.

### Dependencies out of sync after branch switch

The `post-checkout` hook should auto-install, but if not:

```bash
pnpm install --frozen-lockfile
```

---

## License

MIT

# AGENTS

<skills_system priority="1">

## Skills Discovery

- Canonical skills live in `.agents/skills/`.
- Provider-linked views are managed by OAT sync tooling; do not manually edit provider skill directories.
- Refresh provider views: `oat sync --scope all`
- Check sync status: `oat status --scope all`
- Install/update skill packs: `oat tools install`

</skills_system>

## Development Commands

### Essential Commands
- `pnpm build` - Build the project
- `pnpm lint` - Lint code using Biome
- `pnpm format` - Format code using Biome
- `pnpm type-check` - TypeScript type checking
- `pnpm test` - Run tests

### Code Quality
- Commit messages follow Conventional Commits format
- Pre-commit hooks run lint-staged (Biome check on staged files)
- Pre-push hooks run full lint suite

## Agent Workflow

For multi-session or complex development tasks, use OAT's structured project lifecycle:

- **Start a project**: Use the `oat-project-new` or `oat-project-quick-start` skill
- **Resume a project**: Use the `oat-project-open` skill
- **Check progress**: Use the `oat-project-progress` skill
- **Create PR**: Use the `oat-project-pr-final` skill

Projects are stored in `.oat/projects/` with structured artifacts (discovery, plan, implementation, reviews).

# CLAUDE.md

## Project Overview

This repository is **Docsify**, a client-side documentation site generator. The project is written primarily in JavaScript and TypeScript and uses Rollup/PostCSS for builds, Jest for unit/integration tests, and Playwright for E2E tests.

## Environment

- Node.js: `>=20.11.0`
- Package manager: npm
- Module system: ESM (`"type": "module"`)
- Main development branch: `develop`
- Documentation site: `docs/`

Use the repository's existing package versions and scripts. Do not introduce a new package manager or tooling without a clear reason.

## Repository Structure

- `src/` — Docsify source code
- `build/` — build and release scripts
- `test/` — unit, integration, E2E, and consumer tests
- `docs/` — documentation website content and Docsify site configuration
- `dist/` — generated build output; do not edit manually
- `lib/` — generated/legacy distribution output; do not edit manually
- `themes/` — generated theme output; do not edit manually
- `.husky/` — Git hooks

## Development

Install dependencies:

```bash
npm install
```

Start the development server:

```bash
npm run dev
```

Build the project:

```bash
npm run build
```

Serve the project in development mode:

```bash
npm run serve:dev
```

## Validation

Before submitting code changes, run the smallest relevant checks first.

Type checking:

```bash
npm run typecheck
```

Lint:

```bash
npm run lint
```

Unit tests:

```bash
npm run test:unit
```

Integration tests:

```bash
npm run test:integration
```

E2E tests:

```bash
npm run test:e2e
```

Full test suite:

```bash
npm test
```

If a check fails, report the failure rather than claiming the change is verified.

## Documentation Changes

The documentation site lives under `docs/` and is served by Docsify.

- `docs/index.html` is the Docsify site entry point and configuration.
- `docs/_sidebar.md` controls the sidebar navigation.
- Markdown files in `docs/` are documentation pages.
- Keep the sidebar intentionally minimal when requested; do not restore removed links or pages without explicit instruction.
- The current site is configured for a light/white theme. Avoid reintroducing automatic dark-mode CSS unless explicitly requested.
- Do not edit generated build output when changing source or documentation configuration.

## Coding Guidelines

- Follow the existing code style and naming conventions.
- Prefer small, focused changes.
- Reuse existing dependencies and utilities before adding new ones.
- Preserve existing behavior unless the task explicitly asks for a behavior change.
- Keep comments useful and concise.
- Use ESM imports/exports consistently with the repository.
- Run formatting/linting relevant to changed files before finishing.

## Git Guidelines

- Do not create commits unless explicitly requested.
- Do not push branches or open pull requests unless explicitly requested.
- Do not reset, checkout, or discard user changes without explicit approval.
- Before destructive operations such as deleting or overwriting files, inspect the target and confirm scope when it is not already explicit.
- Keep unrelated working-tree changes untouched.

## Agent Workflow

1. Inspect the relevant files before changing them.
2. Understand the existing implementation and repository conventions.
3. Make the smallest change that satisfies the request.
4. Validate with the most relevant available checks.
5. Report exactly what changed and which checks were run.

When a request is ambiguous, prefer the interpretation that preserves existing project behavior and minimizes unrelated changes.

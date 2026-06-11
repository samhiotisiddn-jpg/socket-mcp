# CLAUDE.md

**MANDATORY**: Act as principal-level engineer. Follow these guidelines exactly.

## Project Overview

socket-mcp is the Socket Model Context Protocol server — exposes Socket dependency scanning and Socket.dev API surfaces to MCP-aware clients (Claude Desktop, Cursor, etc.).

## Layout

- `index.ts` — entry; registers MCP tools and starts the stdio server.
- `lib/` — tool implementations and Socket API wrappers.
- `artifacts.test.ts` — co-located unit tests; additional fixtures under `docs/`.
- `Dockerfile` — container build for hosted deployment.

## Commands

```bash
pnpm install          # install dependencies
pnpm run type         # type check
pnpm test             # run tests (vitest)
pnpm run lint         # lint
pnpm run format       # format
pnpm run check        # lint + type + format check
pnpm run fix          # auto-fix all
pnpm run build        # build
node index.js         # run locally
```

## MCP-Specific Notes

- The MCP protocol is stdio-framed JSON-RPC. Never print to stdout outside the framing — use `getDefaultLogger()` (writes to stderr) for diagnostics, never `console.log`.
- Tool descriptions are part of the wire contract — changing one is a breaking change for clients that key on the description text. Bump the server version when modifying tool descriptions or input schemas.
- Container image must include a CA bundle (`ca-certificates`) — the Socket API uses TLS.

## Tooling

- **Package manager**: `pnpm` — never `npx`, `yarn dlx`, or `npm`.
- **Bundler**: rolldown.
- Run `pnpm install` after `package.json` edits.

## Testing

- Tests are `*.test.ts` co-located with sources.
- Never use `--` before test paths.
- Mock the Socket API surface; never let tests hit production.
- `pnpm test path/to/file.test.ts` to run a single file.

## Commits & PRs

- Conventional Commits: `<type>(<scope>): <description>`, lowercase type.
- No AI attribution in commits.
- Push direct → PR only on rejection.

## Code Style

- No `TODO`/`FIXME`/stubs.
- `undefined` over `null`.
- `getDefaultLogger()` over `console.*`.
- Every top-level function/interface/type/class in `src/` is `export`ed.
- No `any` type — ever. Use `unknown` + narrowing guards.
- Module-scope functions use `function foo() {}` declarations, not `const foo = () => {}`.
- No underscore-prefixed identifiers (e.g., `_resetX`, `_cache`).
- File soft cap: 500 lines. Hard cap: 1000 lines.

# AGENTS.md

Long-term maintenance repository for **Nitro v2** (`nitropack` on npm).

Active development happens upstream in [nitrojs/nitro](https://github.com/nitrojs/nitro) (`main`, Nitro v3+). This repo only keeps v2 stable for existing users.

## Repo

- `src/` — `cli`, `config`, `core`, `rollup`, `runtime`, `presets` (deployment presets), `types`, `kit`
- `test/` — vitest tests and fixture (`test/fixture`)
- `playground/`, `examples/`, `docs/`
- Package manager: pnpm. Useful scripts: `pnpm build`, `pnpm test` (lint + vitest), `pnpm vitest run`, `pnpm test:types`, `pnpm lint:fix`
- After changing presets, run `pnpm gen-presets` (generated files: `src/presets/_*.gen.ts`)
- Development needs Node.js >= 22.19 (dev tooling such as vitest 5 and undici 8), even though the published `engines` range still allows Node 20.
- Preset tests write to a shared `/tmp/nitro-tests` by default. When several test runs may happen at once (e.g. multiple worktrees), set `NITRO_TEST_TMP_DIR` to a private directory to avoid flaky `EEXIST`/`ENOENT` failures.

## Contribution scope

Accepted:

- **Bug fixes**
- **Deployment preset updates** (provider changes, compatibility, security)

Fixes/preset changes should stay in sync with upstream: backport only changes that have **already landed on upstream `main` and shipped in the latest upstream release**. Link the upstream PR/commit and release in the PR description. v2-only fixes are fine when the bug does not exist upstream.

**New features are not accepted** by default. They must be discussed with the maintainer first (open a discussion) and are only considered if they make sense for long-term support and benefit enough users.

## Dependencies

Be careful with dependency upgrades. Several major dependencies are pinned to the major line Nitro v2 is built on and **cannot be upgraded** without breaking users:

- `h3` (v1), `unstorage` (v1), `crossws`, `db0`, `radix3`, `hookable` (v5), `ofetch` (v1), `unenv`, `listhen`, `rollup`
- `unctx` (v2): v3 holds the async context behind a `WeakRef` (breaks `useEvent()` after the response with `experimental.asyncContext`) and shares a global context registry with other unctx users in the process
- `chokidar` (v5), `@cloudflare/kv-asset-handler` (v0.4): newer majors require Node >= 22
- Dev only: `typescript` (v6; v7 lacks the compiler API `rollup-plugin-dts` needs), `@cloudflare/workers-types` (v4; feeds published preset types), `@azure/functions` (v3; presets use the v3 programming model), `ohash-v1` (intentional ohash v1 alias for hash compatibility tests)

Rules:

- Do not bump a dependency across a major version unless explicitly approved.
- Avoid adding new dependencies.
- Keep the Node.js `engines` range and public API/types unchanged.

Before a major (or 0.x minor) bump of a runtime dependency:

1. Find every call site and whether it runs at build time or is bundled into user output (`src/runtime`, `src/presets/*/runtime`), and whether its types leak into public types.
2. Map each breaking change from the changelog to those call sites, and check upstream `main` for the same bump (link it).
3. Compare old vs new behaviour directly (install both versions side by side via npm aliases, diff generated output), not only the test suite.
4. If behaviour changes, keep v2 behaviour with a small shim plus a regression test, and document any accepted difference in the PR.

# AGENTS.md

Long-term maintenance repository for **Nitro v2** (`nitropack` on npm).

Active development happens upstream in [nitrojs/nitro](https://github.com/nitrojs/nitro) (`main`, Nitro v3+). This repo only keeps v2 stable for existing users.

## Repo

- `src/` — `cli`, `config`, `core`, `rollup`, `runtime`, `presets` (deployment presets), `types`, `kit`
- `test/` — vitest tests and fixture (`test/fixture`)
- `playground/`, `examples/`, `docs/`
- Package manager: pnpm. Useful scripts: `pnpm build`, `pnpm test` (lint + vitest), `pnpm vitest run`, `pnpm test:types`, `pnpm lint:fix`
- After changing presets, run `pnpm gen-presets` (generated files: `src/presets/_*.gen.ts`)

## Contribution scope

Accepted:

- **Bug fixes**
- **Deployment preset updates** (provider changes, compatibility, security)

Fixes/preset changes should stay in sync with upstream: backport only changes that have **already landed on upstream `main` and shipped in the latest upstream release**. Link the upstream PR/commit and release in the PR description. v2-only fixes are fine when the bug does not exist upstream.

**New features are not accepted** by default. They must be discussed with the maintainer first (open a discussion) and are only considered if they make sense for long-term support and benefit enough users.

## Dependencies

Be careful with dependency upgrades. Several major dependencies are pinned to the major line Nitro v2 is built on and **cannot be upgraded** without breaking users, for example: `h3` (v1), `unstorage` (v1), `crossws`, `db0`, `radix3`, `hookable` (v5), `ofetch` (v1), `unenv`, `listhen`, `rollup`.

- Do not bump a dependency across a major version unless explicitly approved.
- Avoid adding new dependencies.
- Keep the Node.js `engines` range and public API/types unchanged.

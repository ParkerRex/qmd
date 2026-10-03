# Prefer exact collection paths during document retrieval

Planned at `04e4dbd8245c527a88f1a8f0bda547aef9ca81fb`, 2026-09-30. Priority P1; effort S; risk LOW; no dependencies.

## Execution contract

You are the executor. Work only in `/tmp/qmd-contributions-20260930/exact-document-lookup`, branch `fix/get-exact-collection-path`. Use Opus 5.5 with high reasoning. Do not create subagents. The orchestrator reviews after your commit; do not push, publish, create issues/PRs, or merge. Commit exactly one focused change. Do not modify this plan or index.

Read the current `/Users/bazelrex/Developer/maintainer-profiles/tobi-lutke/{AGENTS.md,projects/qmd.md,PR_CHECKLIST.md,REVIEW_RUBRIC.md}`, local CLAUDE.md, and `/Users/bazelrex/.agents/skills/test-audit/SKILL.md`. User preferences: simple type-safe TypeScript, no any, professional names, no unrelated changes, never write unit tests after implementation, meaningful behavior coverage only. The user authorized isolated scratch indexing for verification; never touch the user's real config/index.

## Why and current state

`src/store.ts:4851` owns `findDocument`. Its first exact query at 4888 checks only `'qmd://' || d.collection || '/' || d.path = ?`. The next query at 4897 checks suffix LIKE and LIMIT 1, before absolute/path resolution.

Baseline repro: insert `other/docs/readme.md` first, then `docs/readme.md`, with distinct content. `store.get('docs/readme.md', {includeBody:true})` returns `qmd://other/docs/readme.md`. Real HTTP MCP `resources/read` for `qmd://docs/readme.md` returns the wrong document under the requested URI, because `src/mcp/server.ts:227` passes decoded `docs/readme.md` into SDK get. Equivalent accepted precedence exists in `resolveCommaListName` (merged #868), but that resolver serves multi-get only. Preserve existing single-get fuzzy fallback, docid handling, absolute paths, line suffixes, and ignore behavior.

Existing coverage: `test/store.test.ts` describe findDocument around 1937 includes exact/display/partial/body/missing/ignore/line paths, but does not distinguish colliding suffix paths. `test/mcp.test.ts` exercises MCP boundaries.

## Scope

Allowed changes: `src/store.ts` findDocument exact lookup, existing `test/store.test.ts` retrieval case, `test/mcp.test.ts` only if a distinct MCP transport risk warrants retained coverage, and one concise Fixed entry under Unreleased in CHANGELOG.md. Do not edit other retrieval behavior, multi-get, dependencies, lockfiles, Nix, or general formatting. No new source exports, wrappers, casts, or module mocks for tests.

## Steps and verification

1. Check `git diff --stat 04e4dbd..HEAD -- src/store.ts test/store.test.ts test/mcp.test.ts CHANGELOG.md`; it must be empty initially. Confirm current code matches the excerpts. Read complete existing relevant tests and owner before choosing coverage.
2. Before source changes, extend the strongest existing retrieval case with two different collections whose suffixes collide. Exercise public retrieval and independently expect the exact document content/path, including reversed insertion order if useful in the same case. Save a failing baseline command and output outside the worktree under `/tmp/qmd-execution-20260930/exact-document-lookup/`. Expectations must not be derived by the resolver under test. No duplicated test at every layer.
3. Implement the smallest exact collection/path precedence repair. Keep existing suffix fallback behavior. Update the changelog in user language.
4. Re-run focused coverage on Bun and Node and preserve logs. Exercise the real HTTP MCP resources/read path with scratch config and SQLite and preserve a repeatable script and JSON output under the artifact directory. A baseline scratch example is `/tmp/qmd-retrieval-audit.ts` and `/tmp/qmd-mcp-retrieval-audit.ts`; adapt them to this worktree and fresh temporary data, never the main clone imports or real user configuration.
5. Run lint `bun node_modules/oxlint/bin/oxlint`, typecheck `node node_modules/typescript/bin/tsc -p tsconfig.build.json --noEmit`, full Node `CI=true node node_modules/vitest/vitest.mjs run --testTimeout 60000 test/`, full Bun `CI=true bun test --timeout 60000 --preload ./src/test-preload.ts test/`, and build/package smoke `node scripts/package-smoke.mjs`. Record every exact command, exit status, pass/skip counts, and artifact. Copied node_modules already supplied; do not install or alter lockfiles. Nix unavailable locally; report that check unverified.
6. Read full diff and tests, `git diff --check` must pass, scope must match, then stage only allowed files and commit `fix(store): prefer exact collection paths in get`. No push or PR.

## Done criteria and report

Exact retrieval returns requested content despite suffix collisions; real MCP returns correct content under the requested URI; existing retrieval paths stay green; lint/typecheck/full Node/full Bun/package smoke pass; baseline coverage failed for the intended reason before implementation; no out-of-scope diff; one commit and clean worktree.

Report STATUS COMPLETE or STOPPED, commit SHA, files changed, root cause/fix, baseline failure evidence, exact checks/counts/artifact paths, and limitations. Stop and report if drift, duplicate upstream work, unavoidable out-of-scope changes, or repeated unexplained failures arise. Never claim checks you did not run.

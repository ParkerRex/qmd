# Keep indexed documents accessible after an SDK collection rename

Planned at `04e4dbd8245c527a88f1a8f0bda547aef9ca81fb`, 2026-09-30. Priority P1; effort S; risk MED; no dependencies.

## Execution contract

You are the executor. Work only in `/tmp/qmd-contributions-20260930/sdk-collection-rename`, branch `fix/sdk-rename-indexed-collection`. Use Opus 5.5 with high reasoning. No subagents. The orchestrator reviews after your commit; do not push, publish, create issues/PRs, or merge. Commit exactly one focused change. Do not modify this plan or index.

Read the current `/Users/bazelrex/Developer/maintainer-profiles/tobi-lutke/{AGENTS.md,projects/qmd.md,PR_CHECKLIST.md,REVIEW_RUBRIC.md}`, local CLAUDE.md, and `/Users/bazelrex/.agents/skills/test-audit/SKILL.md`. User preferences: simple type-safe TypeScript, no any, professional names, no unrelated changes, never write unit tests after implementation, meaningful behavior coverage only. User authorized isolated scratch SDK indexing; never touch the user's real config/index.

## Why and current state

`src/index.ts:505` SDK rename calls `renameStoreCollection(db, oldName, newName)` then collection-config write-through. `src/store.ts:1386` checks target-name collision and updates only store_collections. Indexed `documents.collection` stays old. Baseline: SDK update indexes original/auth.md; rename original to renamed returns true, renamed stats count 0, scoped search [], and get(qmd://renamed/auth.md) not_found. Original scope still finds it.

Existing `src/store.ts:3703` renameCollection updates documents and settings, but is not atomic and its triggers drop CJK FTS normalization (open #1007). Do not blindly call it and introduce that regression. Keep this PR scoped to SDK rename document state; do not duplicate the CLI CJK repair or unrelated SDK remove/ignore/config-source fixes (#1017, #1015).

Read callers of renameStoreCollection and renameCollection before choosing the smallest correct implementation. Preserve return false for missing source, throw for existing target, and prevent partial changes on collisions. Preserve content hashes, document ids/metadata, and searchability including CJK. Prefer existing real store mechanisms without speculative layers. Flag overlap or unavoidable CLI changes to the orchestrator before bundling them.

Existing `test/sdk.test.ts:198` rename happy path tests an empty collection. Missing source and collision tests follow. Extend those cases to exercise indexed documents rather than asserting only configuration names.

## Scope

Allowed: `src/index.ts` SDK rename; `src/store.ts` only the small storage operation needed to atomically keep documents/settings/FTS consistent; `test/sdk.test.ts` focused existing SDK rename cases; one concise Fixed entry under Unreleased in CHANGELOG.md. No dependency, lockfile, Nix, formatting, unrelated collection operations, or global config-source redesign. No any, module mocks, source-grep tests, test-only exports or wrappers.

## Steps and verification

1. Check `git diff --stat 04e4dbd..HEAD -- src/index.ts src/store.ts test/sdk.test.ts CHANGELOG.md`; empty initially. Read relevant complete tests and source/callers.
2. Before implementation, extend existing rename coverage to index real temp files through SDK update, rename, and verify get(new URI), new scoped lexical search, counts, and old URI absence. Extend collision case to show indexed documents/config stay unchanged. Preserve existing missing-source assertions. Include meaningful YAML persistence and CJK coverage if needed to protect supported contracts, with all new cases written before code. Save baseline failures under `/tmp/qmd-execution-20260930/sdk-collection-rename/`.
3. Make SDK rename move indexed documents with settings atomically, preserve normalized search content, and retain return/collision behavior and config write-through. Add concise changelog entry. Do not invent a general abstraction for one operation.
4. Run focused Node/Bun SDK tests, then a repeatable scratch SDK update/rename/search/get/reopen flow with real temporary files/database/config. Keep script and output under artifact directory. `/tmp/qmd_sdk_audit_reproduction.ts` illustrates the failure but imports main; adapt to this worktree and avoid its unrelated scenarios.
5. Required: lint `bun node_modules/oxlint/bin/oxlint`, typecheck `node node_modules/typescript/bin/tsc -p tsconfig.build.json --noEmit`, full Node `CI=true node node_modules/vitest/vitest.mjs run --testTimeout 60000 test/`, full Bun `CI=true bun test --timeout 60000 --preload ./src/test-preload.ts test/`, package smoke `node scripts/package-smoke.mjs`. Save exact commands/exit statuses/counts/logs. Copied node_modules supplied; do not install or change lockfiles. Nix unavailable; report unverified.
6. Read full diff/tests, `git diff --check` passes, scope matches, commit only allowed files as `fix(sdk): keep indexed documents accessible after collection rename`. One commit, no push/PR.

## Done criteria and report

New-name document retrieval, scoped search, and stats work after rename and reopening; old-name URI no longer finds the document; CJK search preserved; existing destination failure and missing source cause no state loss; metadata/content identity preserved; required local gates pass; baseline coverage demonstrated intended failure before implementation; focused diff; one commit and clean worktree.

Report STATUS COMPLETE or STOPPED, SHA, changed files, cause/fix, baseline failure evidence, exact commands/pass/skip counts/artifact paths, and limitations. Stop for unexpected drift, duplicate work, unavoidable out-of-scope changes, or repeated unexplained failures. Reviewer maintains the plan index. Never claim checks you did not run.

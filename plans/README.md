# QMD contribution plans

This directory archives the September 30 execution plans. Their executor
instructions and verification counts describe that work at the time; use the
current repository instructions and PR heads for future work.

Prepared on 2026-09-30 against upstream main `04e4dbd8245c527a88f1a8f0bda547aef9ca81fb`.

The user authorized two independent Opus 5.5 executors with high reasoning, separate worktrees and commits, followed by orchestrator review, validation, and PR submission. Upstream merge requires maintainer permission: this account has READ access.

| Plan | Worktree | Branch | Status |
| --- | --- | --- | --- |
| 001 exact document lookup | `/tmp/qmd-contributions-20260930/exact-document-lookup` | `fix/get-exact-collection-path` | APPROVED; PR [#1026](https://github.com/tobi/qmd/pull/1026) |
| 002 SDK collection rename | `/tmp/qmd-contributions-20260930/sdk-collection-rename` | `fix/sdk-rename-indexed-collection` | APPROVED; PR [#1027](https://github.com/tobi/qmd/pull/1027) |

No implementation dependency. Each executor edits only its own worktree. The reviewer maintains this index. Plans are local coordination material, not PR contents.

Duplicate checks excluded multi-get resolution (merged #868), CJK normalization after CLI rename (open #1007), and SDK collection removal (#1017).

The orchestrator read both committed diffs and reran lint, typecheck, full Node/Bun suites, package smoke checks, and independently authored real-file/SQLite integration checks on the final commits. Per branch: Node 1,246 passed/79 skipped, Bun 1,246 passed/88 skipped/0 failed. Nix, Windows, and native models were not verified. Both branches were rebased on the latest main before submission; both PRs are non-draft and mergeable.

Commit 001: `40e8db1ea76f997d398d067a7bc65b8ef0349589`. Commit 002: `380d2ba7d53189f8ba211433a23e7a69f9a16f54`.

Executor transcripts, model receipts, baseline failures, review feedback, independent reports, and full verification logs are under `/tmp/qmd-execution-20260930/`. Security checks passed after submission. Upstream CI/maintainer merge remain external steps.

## Later verification

- PR #1027 was updated on October 1 at `0fc2b2e263c63b354f39bced50d51b67c01c17cb`
  to roll back an SDK rename when configuration writing fails.
- PR #1017 was rebased onto main `26b703c` and updated on October 2 at
  `a4252e53be9fa5f818c6dde5f6664d5421c22fce` to roll back collection removal
  when configuration writing fails.
- Dated reproduction scripts, reports and verification logs are preserved in
  [maintainer-profiles](https://github.com/ParkerRex/maintainer-profiles/tree/main/evidence).
  The October 2 observations record both PRs as open; integration upstream
  remains unverified.

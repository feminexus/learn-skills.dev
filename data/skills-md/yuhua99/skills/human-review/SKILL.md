---
name: human-review
description: "Explain a code change to a human reviewer. Use only when the user explicitly says they will review the change themselves."
---

# Human Review

Write comments in **zh-tw**.

## Tool
`{baseDir}/scripts/tuicr-review.sh`:
- `start [--repo PATH] [SCOPE]` — open a review and print its slug.
- `comments [--repo PATH]` — print new comments as JSON.
- `add [--repo PATH] <flags> "text"` — post a comment. Flags: `--target-file`, `--line`, `--end-line`, `--side old|new`, `--type issue|suggestion|note|praise`.

Ask once if SCOPE is missing. Use `working`, a revision/range, or `pr <n>`.

## Workflow
1. Read the scoped `git show/diff` and any code needed to understand it.
2. Group hunks by concern and draft all comments before `start`. For each non-trivial group, cover how, why, and the likely objection. Annotate repetitions once, summarize them review-wide, and redact secrets.
3. Run `start`, then `add`:
   - File-level: one-sentence summary.
   - Relevant line/range: how, why, and objection, separated by blank lines. Omit the objection for rote changes.
   - Review-level: overview and deliberate scope cuts with reasons.
   - Use `issue` only for problems. Anchor every group to code.
4. Follow-up:
   - `approved` → stop.
   - New comments → address each, fix as needed, restart the same scope, and reply nearby.
   - Fetch failure → run `comments` manually.

## Gotchas
- Existing open review → ask the human to press `q`, then retry.
- Abnormal exit sends no trigger.
- Amend/rebase creates a new scope identity; start a new session.

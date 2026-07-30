---
name: commit-and-push
description: Check, commit, and push current Git changes with a branch-derived Conventional Commit scope.
disable-model-invocation: true
---

# Commit and Push

1. Run `git status --short` and inspect the uncommitted diff once. Stage the current task's files, then run `git diff --cached --check`. This is the complete check; continue when it passes and the staged set is non-empty.
2. Create one commitlint-compatible commit using only `<type>(<scope>): <terse imperative summary>`. When the branch contains a tracker identifier, use it unchanged as `<scope>`, such as `BILLING-2819`, `CPA-1411`, or `#48`. Otherwise use the concise branch topic, then the narrowest changed component. Keep the summary terse and imperative.
3. Push to the configured upstream. If none exists, run `git push -u origin HEAD`.
4. Return `<short-sha> <subject>` and `Pushed <branch> -> <upstream>`.

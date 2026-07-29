---
name: git-commit-batcher
description: Analyze current Git changes, split them into minimal reversible commit batches, and draft or execute Conventional Commits after explicit confirmation. Trigger when the user wants to inspect Git changes, generate commit messages, split commits by intent, commit staged or unstaged work, or says phrases like "帮我提交", "生成提交批次", "commit changes", "split commits", "拆分 commit", "按 Conventional Commits 提交", or "生成 commit message". Works for any agent tool; agent-specific entry files must stay thin shells that route here instead of copying these rules.
---

# Git Commit Batcher

## When to trigger

Activate when the user wants any of these:

- Analyze the current Git working tree or staged changes.
- Generate one or more commit messages.
- Split current changes into the smallest reversible commit batches.
- Commit changes using Conventional Commits.
- Review or execute a staged, unstaged, or mixed Git commit workflow.

Do not trigger for Git history analysis, branch management, release tagging, or pull
request writing unless the user's immediate task is to form or execute commits.

## Always read first

1. [rules/git-safety.md](rules/git-safety.md) - non-negotiable safety rules
2. [rules/commit-format.md](rules/commit-format.md) - Conventional Commit and Chinese message rules
3. [rules/batching.md](rules/batching.md) - minimal reversible batch rules

## Task routing

| User wants... | Read |
|---|---|
| Analyze current changes | [workflows/analyze-changes.md](workflows/analyze-changes.md) |
| Discover local commit rules | [references/config-discovery.md](references/config-discovery.md) |
| Generate batches and messages | [workflows/analyze-changes.md](workflows/analyze-changes.md), then [workflows/plan-batches.md](workflows/plan-batches.md) |
| Commit after approval | Complete analysis, batch planning, and 询问用户并等待确认 first; then read [workflows/confirm-and-commit.md](workflows/confirm-and-commit.md) |
| Run final architecture and safety checks | [references/self-check.md](references/self-check.md) |

## Hard rules

- Local commit configuration has priority over default Conventional Commits.
- If the index already contains staged changes, 询问用户并等待确认 whether to analyze
  only staged changes before expanding scope.
- Never commit before showing all batches and commit messages and receiving explicit
  confirmation from the user.
- All confirmation loops must be described generically as "询问用户并等待确认";
  do not name one agent tool's interaction feature.
- Preserve staging awareness when mixing staged and unstaged changes; do not
  accidentally merge unrelated staged content into another batch.
- Stage files with specific paths only, such as `git add -- path/to/file`; never use
  `git add .`.
- Do not touch unrelated changes, overwrite user work, or clean untracked files.
- If the user asks for "only commit message", output only the raw commit message text.

## Output contract

When proposing batches, show each batch with:

- Batch number
- Files
- Commit message
- Split rationale
- Risk or breaking-change notes

Then 询问用户并等待确认 before running any commit command.

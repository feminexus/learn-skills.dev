---
name: git-weekly-report
description: Scan local disks or selected roots for Git repositories and turn commits, changed files, and meaningful uncommitted work from a default or custom time range into a concise Chinese or English weekly report. Use when the user asks for a Git weekly report, work summary, Friday update, recent development summary, 周报, 本周工作总结, or multi-repository activity report.
---

# Git Weekly Report

Generate a factual weekly report from local Git evidence.

## Workflow

1. Determine scope and period.
   - Use roots and dates explicitly supplied by the user.
   - Otherwise scan all local fixed disks on every run; do not reuse a cached repository list.
   - Default to the previous Saturday at 00:00 in the user's local timezone through now.
   - Treat “last 7 days” or “最近7天” as a rolling seven-day period.

2. Collect evidence.
   - On Windows, prefer `scripts/collect-weekly-git.ps1`.
   - On macOS or Linux, run `python3 scripts/collect_weekly_git.py`.
   - Pass one or more explicit roots when requested.
   - Pass a custom `--since` or `-Since` value when requested.
   - Discover repositories on every run and report scan roots, duration, and repository count.
   - Read Git identity per repository and filter commits to that identity when available.
   - Use per-command `safe.directory`; never modify global Git configuration.
   - Exclude merge commits unless they contain meaningful implementation work.
   - Keep uncommitted changes separate from completed work.
   - Ignore IDE metadata, caches, generated output, and paths already recognized as nested repositories.

3. Convert changes into outcomes.
   - Group related commits into business-facing items.
   - Explain delivered behavior or solved problems rather than listing files.
   - Inspect a focused diff only when commit messages are too vague.
   - Do not invent completion status, business value, metrics, blockers, or plans.

4. Choose language.
   - Follow an explicit Chinese or English request.
   - Otherwise answer in the user's language.
   - Use the matching template in `references/report-formats.md`.

5. Return the report in the conversation unless the user asks for a file.

## Guardrails

- Perform read-only Git operations.
- Do not commit, push, switch branches, rewrite history, or edit repositories.
- Do not expose email addresses, absolute local paths, commit hashes, or raw file lists in the main report.
- State clearly when inaccessible paths, missing Git identity, or absent activity limit the result.

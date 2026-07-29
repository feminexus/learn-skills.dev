---
name: nm-init-project-v3
description: Initialize, adopt, inspect, migrate, update, recover, or validate a project with the NM V3.2 hidden lightweight Goal workflow. Use when Codex needs to create a V3.2 project, adopt an existing Git project without taking ownership of host-root package or formatting assets, migrate V3 3.0/3.1 into .nm-docs, check source/layout/dependency/Hook drift, create or stamp a hidden Spec, run structured verification commands, test optional dual-channel Feishu notifications, or install the exact offline V3 Skill bundle.
---

# NM Init Project V3

Use the digest-verified deterministic tool and offline payload. Do not manually
copy template files or download a mutable executable.

## Entry point

When the repository checkout is known:

```bash
python3 /path/to/nm-docs/tools/nm-v3/nm_v3.py <command> ...
```

Otherwise use the installed Skill:

```bash
python3 "$HOME/.agents/skills/nm-init-project-v3/scripts/run_nm_v3.py" <command> ...
```

The wrapper verifies the exact bundled tool, manifest, all fixed payload files,
and the complete payload digest before execution. Default source mode is that
offline payload. An explicit `--source-dir` or an explicit immutable
`--source-commit` plus `--bundle-sha256` selects the other mutually exclusive
source modes.

## Inspect first

`status`, `check`, and every `--dry-run` are strictly read-only. They do not
fetch, create branches, update tracking refs, write state, install packages, or
change the Git index.

```bash
python3 /path/to/nm-docs/tools/nm-v3/nm_v3.py status --target /absolute/project --json
python3 /path/to/nm-docs/tools/nm-v3/nm_v3.py check --target /absolute/project --json
```

A branch or tag is accepted only as a dry-run candidate through `--remote-ref`;
review the reported SHA, obtain the complete expected bundle digest, then use
the immutable commit mode for a write.

## New and existing projects

- `init` is for an empty project or an explicitly authorized non-Git project.
- `adopt` is for an ordinary existing Git project with synchronized
  `origin/dev`.
- `update` is limited to compatible V3.2 patch/state repair and never downgrades.
- `migrate` supports `3.0.0` and `3.1.0` and requires
  `--root-transition clean|compat`.
- `recover` validates an unfinished journal and restores original bytes and
  modes. Do not manually remove transaction state.

Examples:

```bash
python3 /path/to/nm-docs/tools/nm-v3/nm_v3.py init \
  --target /absolute/new-project \
  --source-dir /path/to/nm-docs

python3 /path/to/nm-docs/tools/nm-v3/nm_v3.py adopt \
  --target /absolute/existing-project \
  --source-dir /path/to/nm-docs \
  --dry-run

python3 /path/to/nm-docs/tools/nm-v3/nm_v3.py migrate \
  --target /absolute/v3-project \
  --source-dir /path/to/nm-docs \
  --root-transition clean \
  --dry-run
```

For a non-empty non-Git project, first save the `init --dry-run --json`
`planSha256`, then pass it as `--plan-digest` with `--no-git-init`. This binds
the authorized candidate and leaves Git bootstrap to the administrator.

Every existing-Git write requires a clean Git root, exact local/remote `dev`
equality, a fresh fetch immediately before branch creation, an unused allowed
task branch, and a second remote check after the transaction. A moved remote
retains the isolated result but sets `git_ready=false`.

## Hidden runtime

Initialization, adoption, migration, and update never run npm. When the
administrator chooses to prepare dependencies:

```bash
npm --prefix .nm-docs ci --ignore-scripts
.nm-docs/0d-scripts/nm workflow-check
.nm-docs/0d-scripts/nm verify
```

The stable generated-project command is `.nm-docs/0d-scripts/nm`. Use its
`fm`, `lm`, `workflow-check`, `verify`, `verify-goal`, `verify-plan`,
`notify-event`, `status`, `check`, and `hook-attest` subcommands.

Project commands are structured IDs in `.nm-docs/project.json`; they use
explicit argv/cwd/timeouts, no implicit shell, controlled environment
inheritance, process-group timeout termination, no automatic retry, and a fresh
authorization flag for network/external effects. Receipts contain bounded
digests and results, never raw logs or secret values.

## Spec and completion

Create or stamp the hidden Spec only when the administrator requests it:

```bash
python3 /path/to/nm-docs/tools/nm-v3/nm_v3.py create-spec \
  --target /absolute/project \
  --source-dir /path/to/nm-docs \
  --dry-run

python3 /path/to/nm-docs/tools/nm-v3/nm_v3.py spec-stamp \
  --target /absolute/project
```

Body changes require a `spec_version` increment and reset acceptance to pending.
`spec-stamp` records the new binding; it never records administrator acceptance.

Use `finish` for an idempotent final attention handoff after verification. A
notification attempt records `sending` before transport; interruption never
causes an automatic resend.

## Safety

- Stop on unexplained changes, `AGENTS.override.md`, symlinks, submodules,
  special files, LFS, sparse/partial objects, untracked or ignored migration
  inputs, mixed installs, Hook ambiguity, or customized legacy commands.
- Never stash, absorb, or overwrite administrator changes.
- Never install dependencies or execute legacy scripts during migration.
- Keep `.delete-pending` tracked; permanent deletion needs separate authority.
- The Hook is optional support, not workflow authority. Script drift requires a
  new `/hooks` review and machine-local attestation.
- V3.2 does not provide autonomous authorization, deployment, concurrent
  scheduling, cryptographic evidence, or an OS sandbox.

Read `references/v3-lifecycle.md` before migration or lifecycle explanation.
Read `references/install.md` for Skill installation or release questions.

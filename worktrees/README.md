<!--
SPDX-FileCopyrightText: 2026 Benoit Rolandeau <borlnov.obsessio@gmail.com>

SPDX-License-Identifier: Apache-2.0
-->

# Git worktrees

This directory is the home for `git worktree` checkouts. Its contents are gitignored (only this
README is tracked, so the directory survives a fresh clone), which lets a worktree live inside the
repository without ever showing up as untracked content.

Create one with git first, then enter it by path:

```bash
git worktree add worktrees/<name> -b <branch>
```

A worktree created here resolves both inside the dev container (`/workspaces/swayve`) and on the
host, whose clone path differs, because `worktree.useRelativePaths` is set on the container's system
git config (see [`../.devcontainer/devcontainer.json`](../.devcontainer/devcontainer.json)); this
needs git >= 2.48, which the dev container's from-source git provides.

A fresh worktree is not usable until its dependencies are installed (`flutter pub get`, the ACT
packages' `pub_get_all.sh`, and any codegen) — the same steps `postCreateCommand` runs for the main
checkout.

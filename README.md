<!--
SPDX-FileCopyrightText: 2026 Benoit Rolandeau <borlnov.obsessio@gmail.com>

SPDX-License-Identifier: Apache-2.0
-->

# Swayve

**A body-first rhythm trainer.** Swayve is an experimental Flutter application that trains the sense
of rhythm through the body — the natural sway of the torso and head — rather than through notation,
counting or a traditional metronome. It uses the phone's inertial sensors (accelerometer,
gyroscope) to observe how the body entrains to a pulse, and builds exercises around the pedagogy:

```text
BODY → PULSE → AWARENESS OF THE PULSE → SUBDIVISION → VOICE / HANDS → MUSIC
```

instead of the usual `NOTATION → COUNTING → EXECUTION`.

The design deliberately protects intrinsic motivation: competence-based feedback over points and
loot, non-punitive cumulative history over loss-framed streaks, and score-free spaces (free dance)
that are never turned into a performance task.

> Status: early design. The scientific model, the measurement protocol, the pedagogical progression,
> the motivation model and an MVP are being worked out before the app is built. See the design notes
> as they land.

## Technical basis

Swayve reuses the engineering foundations of
[open_cine_prod_tools](https://github.com/borlnov/open_cine_prod_tools):

- **Flutter** (SDK pinned to the version the ACT packages expect), Android-first (inertial sensors),
  with the Linux desktop build kept for fast iteration and tests.
- The **ACT Flutter packages** (`actlibs/` git submodule), consumed as `path:` dependencies.
- A **Docker Compose dev container** ([`.devcontainer/`](.devcontainer/README.md)) carrying the whole
  toolchain (Flutter, Android SDK, git, `gh`, `reuse`, Claude Code).
- **REUSE/SPDX** licensing discipline (Apache-2.0).
- An **`AGENTS.md`** development guide for AI agent sessions (added once the architecture is set).

## Getting started

Open the folder in VS Code and reopen in the dev container (see
[`.devcontainer/README.md`](.devcontainer/README.md)). Everything runs inside it.

## License

Apache-2.0. Every file carries SPDX information and `reuse lint` stays compliant.

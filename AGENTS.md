<!--
SPDX-FileCopyrightText: 2026 Benoit Rolandeau <borlnov.obsessio@gmail.com>

SPDX-License-Identifier: Apache-2.0
-->

# Development guide for AI agents

This file gives any agent session or subagent working in this repository what it must know
whatever it is doing: what the project is, where it stands, the architectural direction, and the
norms the code, commits and workflow must follow. Read it entirely before writing anything.

> **Swayve is in its design phase — there is no app code yet.** This guide states only what is
> already settled (the engineering foundations, reused from `open_cine_prod_tools`, and the ways of
> working). Product architecture is decided and written down area by area as we go; until a
> `docs/architecture/` file exists for an area, the direction below and the design notes in
> `docs/plans/` are the record. **Start by reading**
> [`docs/plans/design-kickoff.md`](docs/plans/design-kickoff.md).

`CLAUDE.md` is a symlink to this file, so Claude Code and any agent that looks for `AGENTS.md` read
the same guide. Edit `AGENTS.md`; never replace the symlink with a copy.

## Project overview

Swayve is a **body-first rhythm trainer** (Apache-2.0, github.com/borlnov/swayve). It trains the
sense of rhythm through the body — the natural sway of the torso and head — rather than notation,
counting or a metronome, using the phone's inertial sensors (accelerometer, gyroscope). The core
pedagogy is `BODY → PULSE → AWARENESS OF THE PULSE → SUBDIVISION → VOICE/HANDS → MUSIC`.

- **Target platforms: Android + iOS first** — it reads the phone's inertial sensors, so a real
  device is the primary target. The Linux desktop build is kept for fast iteration and tests.
- **Evidence-based design.** Design claims are grounded in the scientific literature, with each one
  graded established / plausible / hypothesis; do not presume an interpretation is correct.
- **Protect intrinsic motivation.** No punitive streaks, competence feedback over points/XP/loot,
  and score-free spaces (free dance) that are never turned into a performance task. Success is skill
  **transfer** to real dance/singing/piano — the app should become *less* necessary over time, not
  maximise time-in-app.
- The authoritative product brief is the maintainer's `new-app-needs.md` (kept with the maintainer,
  outside this repo). Read it before doing design work.

## Development status

Design phase. Nothing has shipped. The engineering foundations are in place: the dev container, the
ACT packages submodule (`actlibs/`), REUSE/SPDX licensing, and the `worktrees/` layout. **No
`flutter create` scaffold and no application code yet** — the platform set and package id are design
decisions taken before scaffolding. What is planned, and the immediate next step, live in
`docs/plans/` (start with `design-kickoff.md`); a plan is deleted once its outcome is folded into
`docs/architecture/`.

## Ways of working

- The maintainer communicates in French; **all code, comments, commits, branches and GitHub
  content are in English**.
- Work happens on an issue-named branch (`<issue-number>-<slug>`), merged into `main` through a
  pull request.
- A session that needs a checkout of its own puts it under `worktrees/`
  (`git worktree add worktrees/<name> -b <branch>`), never in the `.claude/worktrees/` directory
  Claude Code's own worktree feature defaults to: create it with git first, then enter it by path.
  A fresh worktree is not usable until its dependencies are installed — see `worktrees/README.md`.
- A plan in `docs/plans/` describes work not yet done. Once its step ships and its outcome is folded
  into `docs/architecture/`, the plan is deleted — the code, that directory and the ADRs are the
  record from then on.
- Model tiers: the orchestrating (main) session — the one that discusses, plans, tests, debugs and
  reviews — runs on **Opus 4.8**. It is the **default planner**; implementation is delegated to
  **Sonnet 5** agents; **Fable** is reserved for planning a piece of work that spans several areas
  or crosses an architecture/ADR boundary. Opus reasons and reviews, Sonnet executes, Fable plans
  the heavy work; Opus **proposes an escalation to Fable rather than taking it silently**.
- One commit per logical change. Never reference the plan, steps, or these instructions in code or
  commit messages (issue numbers are allowed in commits/PRs).
- Dependencies never reference their dependents.

## Toolchain

The Flutter SDK (**3.44.6**, the version pinned by `actlibs/tool/.flutter_version`) lives in the dev
container; the developer's host has **no usable one**. An agent session normally starts **inside
that container already** — cwd `/workspaces/swayve`, `flutter` on the `PATH`, and no `docker` binary
at all — so run `flutter analyze`, `flutter test`, `dart run …` and `reuse lint` directly, from the
repo root. Check with `flutter --version` if in doubt; only a session running on the host itself
needs the wrapper:

```bash
cd .devcontainer && docker compose run --rm dev bash -lc 'cd /workspaces/swayve && <command>'
```

Git commands run from the repo root, in the container like everything else. The container carries no
SSH key, so push over HTTPS with the `gh` credentials the named volume persists, and use `gh` itself
for everything else on GitHub (pull requests, issues, the API):

```bash
git -c credential.helper='!gh auth git-credential' \
  push https://github.com/borlnov/swayve.git <branch>
```

The dev container persists the pub cache and the gh / Claude logins in named volumes; X11 is
forwarded so `flutter run -d linux` can open a window, and Android is built and run over wireless
adb (see `.devcontainer/README.md`).

## Architecture (direction)

The app is built on the **ACT Flutter packages** (git submodule `actlibs/`, consumed as plain
`path: actlibs/<pkg>` dependencies). The detail will live in `docs/architecture/`, one file per
area, and reading the file covering the code you are about to change is part of the job once those
files exist. The rules below follow from the ACT stack and hold from the start:

- **Before reaching for a pub.dev package, look in `actlibs/` first.** The app is built on the ACT
  packages, and several needs are already met there. A new pub.dev dependency is the fallback, taken
  only once no ACT package covers the need. When one nearly does but falls short, that is a gap to
  raise for the submodule (a separate change against `actlibs/`, never an edit in place here). The
  one exception is a build failure: when an ACT package's transitive dependencies do not compile on
  a platform this app ships, a maintained pub.dev package is used and the ACT gap is raised
  separately.
- Follow the **ACT patterns**: managers as `AbsWithLifeCycle` classes owned by a global manager and
  resolved via `globalGetIt()`; the ACT **BLoC** pattern (`BlocForMixin`, `BlocStateForMixin`,
  sealed events, one bloc per page, pages split into UI/bloc/state/event files).
- **Never use `Navigator` directly.** All navigation, including closing a dialog and returning its
  result, goes through the ACT router manager resolved from `globalGetIt()`.
- Layering: keep pure, Flutter-free logic (rules, models) separate from the manager and UI layers; a
  pure rule both layers read belongs in a `utils`/logic layer, not in a widget.
- Generated code (`**/*.g.dart`, generated l10n) is git-ignored and regenerated; never edit it, and
  **never touch the `actlibs/` submodule** from this repo.

## Coding standards

Follow the ACT company guidelines (read them when in doubt; they live with the maintainer, outside
this repo — ask if a checkout does not have them):

- `flutter-coding-guidelines.md` (RD/RFL rules)
- `coding-guidelines.md` (durability rules)
- `flutter-project-setup.md` (project setup / l10n)

House style: **`Swayve` prefix** on app classes, doc comments on every declaration, and match the
existing files' idioms exactly. Tests use inline private test doubles (no shared helpers directory).
Do not touch `actlibs/` (submodule) or generated code.

## Licensing / REUSE

Every file needs SPDX info. Header (comment for code, HTML comment for markdown, `.license` sidecar
for uncommentable files, `REUSE.toml` annotations for blanket cases):

```text
SPDX-FileCopyrightText: 2026 Benoit Rolandeau <borlnov.obsessio@gmail.com>

SPDX-License-Identifier: Apache-2.0
```

`reuse lint` must stay 100% compliant. Licenses live in `LICENSES/` (Apache-2.0 for now; others are
added there as a dependency or asset requires one).

## Commits

Conventional Commits, English, subject ≤50 characters, meaningful body when useful. Every commit
ends with a `Co-Authored-By` trailer naming the **exact model and version of the agent that authored
it** — the orchestrating session names its Opus, a delegated worker names its Sonnet or Fable, and
each writes its own current version, never one copied from this file. So, whichever applies:

```text
Co-Authored-By: Claude Opus 4.8 <noreply@anthropic.com>
Co-Authored-By: Claude Sonnet 5 <noreply@anthropic.com>
Co-Authored-By: Claude Fable 5.1 <noreply@anthropic.com>
```

## Localization

The intended approach mirrors OCPT: ARB files (`lib/l10n/intl_en_GB.arb` main +
`lib/l10n/intl_fr.arb`), a generated `Tr` class via `dart run intl_utils:generate`, and every
user-visible string reached
through `Tr.of(context)` and added to both ARB files. The dev container already installs the
arb-editor and runs `intl_utils:generate` in `postCreateCommand`; the ARB files arrive with the app
scaffold.

## Verification gates

All inside the dev container. As the app takes shape, these must pass before finishing any step
(analyze + test at minimum before each commit); the Flutter-specific gates apply once the app is
scaffolded and the relevant dependencies (intl_utils, build_runner) are in `pubspec.yaml`:

1. `flutter pub get`
2. `dart run intl_utils:generate` (once l10n exists)
3. `dart run build_runner build` (once codegen exists)
4. `flutter analyze` → 0 issues
5. `flutter test` → all green
6. `flutter build linux --debug` (local build gate; Android is additionally checked by CI)
7. `reuse lint` → compliant
8. Markdown line length ≤ 100 and the other markdownlint rules, whenever a `.md` file was touched.

Until there is a `pubspec.yaml`, the gates that apply are `reuse lint` and the markdown checks.

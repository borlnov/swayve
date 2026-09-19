<!--
SPDX-FileCopyrightText: 2026 Benoit Rolandeau <borlnov.obsessio@gmail.com>

SPDX-License-Identifier: Apache-2.0
-->

# Swayve — resume-here / design kickoff

This file is a **handoff note** so a fresh agent session (e.g. one launched inside the Swayve dev
container) can pick up exactly where the bootstrap session left off. The maintainer will point a
new session at this file. Read it fully, then continue at **"Immediate next action"** below.

The maintainer communicates in **French**; all repo content (code, comments, commits, docs) is in
**English**, mirroring `open_cine_prod_tools` (OCPT), the sibling project whose engineering
foundations Swayve reuses.

## What Swayve is

A **body-first rhythm trainer**. It trains the maintainer's own sense of rhythm through the body
(the natural sway of torso/head) rather than notation, counting or a metronome, using the phone's
inertial sensors (accelerometer, gyroscope). Core pedagogy:

```text
BODY → PULSE → AWARENESS OF THE PULSE → SUBDIVISION → VOICE / HANDS → MUSIC
```

**The full, authoritative brief is `/new-app-needs.md` on the host** (outside this repo, at
`/home/brolandeau/projects/new-app-needs.md`). It is long and precise — read it before doing design
work. Key points from it:

- The maintainer's profile: strong at **free/improvised dance** and **melodic imitation**; weak at
  **conscious/explicit** pulse extraction, at **keeping internal tempo** once the acoustic cue
  disappears, and at **voluntary subdivision** (triplets are hard). Counting "1-2-3-4" out loud can
  *break* an otherwise-working automatic movement. Torso/head feel the beat better than hands/feet.
- Working hypothesis (his words, to be tested, **not** assumed true): not simply "bad at rhythm",
  but a dissociation between implicit/bodily rhythm processing (preserved) and conscious
  extraction/maintenance/subdivision of the pulse (costly).
- He wants the design to be **evidence-based**: cite publications/DOIs, and clearly separate what is
  **established** vs **plausible** vs **only a hypothesis in his case**. Do not presume our
  interpretation is correct.
- Motivation must **protect intrinsic motivation**: no punitive streaks, competence feedback over
  points/XP/loot, score-free spaces (free dance) that are never turned into a performance task. The
  app should become *less* necessary as skills transfer to dance, singing, and eventually piano —
  success is transfer, not time-in-app.
- Seven deliverables he wants **before** building the whole app, in this order:
  1. a coherent scientific model of his rhythmic functioning;
  2. an initial measurement protocol;
  3. a pedagogical progression;
  4. a motivation model that protects intrinsic motivation;
  5. a reward/feedback system;
  6. an MVP to test the central hypothesis on himself;
  7. criteria to judge, after several weeks, whether the app actually works.

Seven exercise ideas are described in the brief (Phantom pulse, Resist the drummer, Body
subdivisions, Find the "1", Enter on time, Body→voice, Free dance) plus sensor-analysis ideas
(mean offset to beat, variability, tempo drift, recovery time, effect of counting, torso vs head vs
hand) and an adaptive, non-binary difficulty ladder. He also wants a specific scientific analysis
of the **motivation system** (SDT, gamification, expected/performance-contingent rewards, streaks
and loss effects, informational vs controlling rewards, long-term effects after rewards stop),
including possible **negative** effects — do not assume gamification is beneficial.

## Decisions already locked

- **Name:** Swayve. Repo: <https://github.com/borlnov/swayve> (public, Apache-2.0). Verified free of
  homonymous rhythm apps on the stores.
- **Tech basis mirrors OCPT** ("same technical elements"): Flutter (SDK **3.44.6**, pinned by
  `actlibs/tool/.flutter_version`), the **ACT Flutter packages** (`actlibs/` submodule, consumed as
  `path:` deps — prefer an ACT package over a new pub.dev dependency), a **Docker Compose dev
  container**, **REUSE/SPDX** (Apache-2.0), and an **`AGENTS.md`** guide (to be written once the
  architecture is set).
- **Design docs live in `docs/`** OCPT-style: `docs/architecture/` (one file per area, what the code
  does) and `docs/adr/` (why a structural choice was made). `docs/plans/` holds work-not-yet-done
  (this file); a plan is deleted once its outcome is folded into `docs/architecture/`.
- **First design step chosen by the maintainer: "Revue de littérature d'abord"** — do the
  literature review first, then propose an evidence-backed model. (The other offered options were: a
  quick knowledge-based framing first; small discriminating self-experiments first; or literature +
  a self-measurement protocol in parallel.)

## What is done vs not done

Done (committed on `main`):

- `.devcontainer/` (Docker Compose): Flutter 3.44.6, **Android SDK + JDK** (primary target — sensor
  app), Linux desktop toolchain (fast iteration/tests), git-from-source, `gh`, `reuse`, Claude Code.
  README covers wireless-adb testing on a real phone.
- `actlibs/` submodule (the ACT packages).
- REUSE/SPDX, `.gitignore`, `REUSE.toml`, `worktrees/` layout, project `README.md`.

**Not** done yet, on purpose:

- **`flutter create` scaffold** — deferred: the platform set (Android/iOS first, unlike OCPT's
  desktop-first) and the package id are design decisions. Decide platforms, then scaffold.
- **`AGENTS.md`** — content depends on the architecture still to be decided.
- Everything in the seven deliverables above.

## Operating notes (how to work in this repo)

- Inside the dev container: `flutter`, `dart`, `git`, `gh`, `reuse` are on `PATH`; run them from the
  repo root (`/workspaces/swayve`). Run `gh auth login` once in the container (the login persists in
  a named volume). The container has **no SSH key**, so push over HTTPS with the gh credential
  helper:

  ```bash
  git -c credential.helper='!gh auth git-credential' \
    push https://github.com/borlnov/swayve.git <branch>
  ```

- Work on an issue-named branch (`<n>-<slug>`), merge via PR. One commit per logical change.
- Every file needs SPDX info; keep `reuse lint` compliant. Markdown lines ≤ 100 chars (MD013).
- Commits end with a `Co-Authored-By` trailer naming the exact model/version that authored them.
- History note: the bootstrap repo+push was done from the *host* via a throwaway container reusing
  OCPT's image and gh login, because the host has no `gh`, no usable Flutter, and its SSH to GitHub
  fails. From inside the Swayve dev container this is moot — operate normally.

## Immediate next action

Start **deliverable 1 (scientific model)** by running a proper **literature review**, then propose
an evidence-backed model of the maintainer's rhythmic functioning. Requirements from the brief:
give publications/DOIs where possible, and grade each claim as **established / plausible /
hypothesis-in-his-case**. Confront (and correct if needed) his working hypothesis — do not rubber-
stamp it.

Suggested topics to cover (map each back to his profile):

- Beat perception & **beat induction / entrainment**; the Dynamic Attending framework.
- **Sensorimotor synchronization (SMS)** — tapping literature (Repp's reviews), negative mean
  asynchrony, subdivision benefit.
- **Internal tempo maintenance / continuation** when the pacing cue disappears (synchronization-
  continuation paradigm) and tempo drift.
- **Dual-task cost**: why conscious counting can disrupt an automatic motor rhythm (working-memory
  load, explicit vs implicit / procedural motor control).
- **Whole-body / full-body movement, accelerometers, smartphones, wearables, motion capture** used
  to study or train rhythmic synchronization — is torso/axial entrainment genuinely easier than
  effector tapping? Look for studies.
- **Motor learning & transfer**: does movement-based rhythm training transfer to singing/piano?
  Feedback in motor learning (KR/KP, guidance hypothesis, self-controlled feedback).
- **Motivation** (its own analysis, deliverable 4): Self-Determination Theory; intrinsic vs
  extrinsic; expected & performance-contingent rewards (undermining effect / overjustification);
  gamification evidence and its risks; streaks and loss aversion; informational vs controlling
  feedback; habit formation; long-term effects after rewards are withdrawn.

Record the result under `docs/` (start a `docs/architecture/` file for the scientific model, and an
ADR if a structural choice falls out of it). Then continue down the seven deliverables in order,
checkpointing with the maintainer between them. If information is missing to separate competing
hypotheses about his profile, propose small discriminating self-experiments before concluding —
he explicitly invited this.

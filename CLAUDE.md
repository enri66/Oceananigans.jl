# Oceananigans.jl (fork) — project context for Claude Code

This file exists so a Claude Code session started in this fork has context
immediately. Set up 2026-09-20, splitting this out from
`~/Dropbox/Models/NumericalEarth/CLAUDE.md` as Enri's Oceananigans and
NumericalEarth work grew large enough to track separately (see that file's
own session log for the reasoning).

## What this repo is for

Enri's personal fork of `CliMA/Oceananigans.jl`, used for upstream
contributions and PR review/validation work — as distinct from
**consuming** Oceananigans, which happens in `~/Dropbox/Models/NumericalEarth`
(realistic regional ocean sims) and `~/Dropbox/Models/JuliaOceananigans`
(the tutorial curriculum). Bugs and gaps found while working in either of
those often turn into branches/PRs here; when that happens, log the
*investigation* in the consuming project's own CLAUDE.md and log the
*upstream code work* (branch, commit, PR, review back-and-forth) here —
cross-reference rather than duplicate the write-up.

Related repos:
- `~/Dropbox/Models/OceananigansOBC` — private DEVLOG + validation scripts
  for the OBC work (`ObliqueRadiation`, `TracerReservoir`); its own
  `DEVLOG.md` is the detailed record for those two PRs specifically, this
  file is the general/other-PRs log plus the branch map.
- `~/Dropbox/Models/OpenBoundaryTestCases` — public validation scripts +
  media (images embedded in PR/issue comments via `raw.githubusercontent.com`).
- `~/Dropbox/Models/NumericalEarthSandbox` — combines several of this
  fork's in-progress branches with NumericalEarth for integrated testing
  before each PR lands; see its README for the branch-to-PR table.

## Environment

- Remotes: `origin` = `CliMA/Oceananigans.jl` (upstream), `fork` =
  `enri66/Oceananigans.jl` (push target for all branches/PRs).
- Git identity here: `Enrique Curchitser <curchitser@gmail.com>` (matches
  the `enri66` GitHub account) — NOT the `Enri <enri@me.com>` identity
  used in the curriculum/NumericalEarth repos. Check `git config user.name`
  if a commit fails with "Author identity unknown."
- Never switch branches while a background job (a running Julia process)
  in this checkout depends on the current tree — check `ps aux | grep
  julia` first.
- Write commit messages and PR bodies to a scratch file and use `-F`/
  `--body-file` rather than inline heredocs (recurring apostrophe/
  special-character heredoc bug with `$(cat <<'EOF' ... EOF)`).
- Always branch new PR work from the correct base (`origin/main`), never
  from an unrelated in-progress feature branch.

## Upstream contribution rules

- Run NumericalEarth's `restraint-rules.md` checklist on every diff bound
  for upstream — no "claudisms," descriptive names over eponyms (e.g.
  `oblique_radiation_update` not `raymond_kuo_radiation`, per glwagner
  review feedback on #5962).
- Never justify code to Oceananigans reviewers by comparing to ROMS/MOM6
  flags or conventions — state the actual requirement directly. (ROMS/MOM6
  are fine as *design references* while building, per #5962/#5964's own
  history — just not as the justification presented to reviewers.)
- PR bodies carry only the Claude Code footer — never also open with "I
  used Claude...".
- Before assuming a PR's status, check it (`gh pr view <n> --repo
  CliMA/Oceananigans.jl`) — several of the branches below were long-lived
  enough that "open" went stale in memory more than once.

## Branches

| branch | PR | status (2026-09-23) |
|---|---|---|
| `pr/oblique-radiation` | [#5962](https://github.com/CliMA/Oceananigans.jl/pull/5962) `ObliqueRadiation` | **merged** |
| `pr/tracer-reservoir` | [#5964](https://github.com/CliMA/Oceananigans.jl/pull/5964) `TracerReservoir` | **merged** (main has the reviewed, refactored version) |
| `tidal-forcing` | [#5970](https://github.com/CliMA/Oceananigans.jl/pull/5970) tidal astronomy/forcing/BCs | open, **approved** (Simone, 9/21), mergeable; only the non-required `oceananigans-dot-jl` pipeline fails |
| `low-pass-filter` | [#5971](https://github.com/CliMA/Oceananigans.jl/pull/5971) `FilteredTimeInterval` (was `LowPassFilter`) | open, no approvals yet; last push `0c9844a` (CI rerunning) |
| `fix-split-explicit-substep-clock` | [#5982](https://github.com/CliMA/Oceananigans.jl/pull/5982) advance clock through substeps | open, mergeable, waiting on review (only `codecov/patch` red) |
| `fix-actuation-counter-ambiguity` | [#6055](https://github.com/CliMA/Oceananigans.jl/pull/6055) | **closed**, duplicate of Giordano's #6042 |
| `pr-6024` | (Greg Wagner's PR, not ours) [#6024](https://github.com/CliMA/Oceananigans.jl/pull/6024) CATKE `Cᵇ` fix | fetched to cherry-pick onto `everything` for validation, see below |
| `obc/oblique-radiation` | — | superseded by `pr/oblique-radiation` after the #5962 refactor; keep or prune next time this repo is touched |
| `everything` | — | combined branch (all of the above merged together) that `NumericalEarthSandbox` consumes; not itself a PR. Re-merged 2026-09-23 (`6638c209f`), Oceananigans 0.113.1 |

## Session log

Newest entries at the bottom.

### 2026-09-17 — validated CliMA/Oceananigans.jl#6024 (CATKE `Cᵇ` fix)

Cherry-picked Greg Wagner's fix (commit `93d9dda30` from `pr-6024`) onto
`everything` as `9c54b4f49`, pushed to `fork`. Full validation (three-way
MAB M2 tide comparison, results posted to the PR, stratification
follow-up) lives in `NumericalEarth/CLAUDE.md`'s 2026-09-17/19 session
entry — this repo's side of the story is just the cherry-pick + push,
recorded here so the branch table above stays accurate.

### 2026-09-22/23 — #5971 refactor into `FilteredTimeInterval`, CI failures, `everything` re-merge

**CI ambiguity failure on #5971 was not ours.** `cpu-all-tests` failed Aqua's ambiguity check:
`restore_prognostic_state!(obj, ::Nothing)` (`checkpointer.jl:246`) vs the test-only
`restore_prognostic_state!(::ActuationCounter, state)` in `test/simulation/checkpointer.jl`. Both are
on plain `main`; Giordano merged the identical one-line fix as #6042 the same day, so our duplicate
#6055 was closed. **Blind spot found:** my isolated scratch-environment `detect_ambiguities` check
(baseline 9) never loads test-file-defined methods, so it cannot see this class of ambiguity; CI's does.

**#5971 design history** (all pushed to `low-pass-filter`):
- Generalized the JLD2-only timestamp hack into two generic hooks, `output_time(clock, schedule)` and
  `should_write_initial_output(schedule)`, wired into JLD2, NetCDF and Zarr writers (this also fixed
  `time_average_outputs` never wrapping `OrderedDict` outputs, and NetCDF `materialize_output` /
  `define_output_variable!`). Fetch every output BEFORE calling `output_time`, which advances state.
- Renamed `LowPassFilter` to `TemporalLowPassFilter` (Tomas); the sed pass double-prefixed the output
  type (`TemporalTemporalLowPassFilteredOutput`), caught later and fixed. Field renames per Greg:
  `next_frame_number`, `sum_buffer`, `Nbuffer` (kept "frame" over "snapshot").
- Greg's Slack proposal, adopted: `FilteredTimeInterval(kernel; interval)` alongside
  `AveragedTimeInterval`, with `AbstractFilterKernel` subtypes `LanczosKernel(window; cutoff)`,
  `HanningKernel(window)`, `BoxcarKernel(window)`; the window lives in the kernel and kernels are
  called as `kernel(τ)`. `HighPass`/`BandPass` deferred: `run_diagnostic!` divides by the summed
  weights (about zero for them) and a high-pass needs the field at the frame center, which is not
  stored. PR title and body updated. A boxcar frame equals the trailing `AveragedTimeInterval` value
  shifted by `window/2`, so the two are not the same schedule.

**Mistakes to avoid next time:** (1) two commits carried a stray `.commitmsg.txt` because the message
file was written before `git add -A`; removed in `7563e6a67`. Write message files outside the tree
(scratchpad) and add files by name. (2) the kernel refactor left `days`/`hours` as stale explicit
imports, failing `ExplicitImports` in `cpu-all-tests` (`0c9844a`). Run
`ExplicitImports.check_no_stale_explicit_imports(Oceananigans)` locally before pushing.

**#5970:** Simone approved 9/21. His "is this function expensive?" was already addressed by
`653d862de` (latitude structure computed once per point); I had missed the commit and the reply was
posted on 9/23.

**`everything` re-merge (`6638c209f`, pushed):** four conflicts because `everything` held pre-merge
versions of branches main has since absorbed: `tracer_reservoir.jl` took main's reviewed version,
`BoundaryConditions.jl` kept both sides, `step_split_explicit_free_surface.jl` combines #5982's
`substep_clock` with main's `pin_barotropic_faces!`, and the OBC test file keeps both sides' tests.
Checked: filter tests 18/18, hydrostatic OBC tests 15/15, split-explicit boundaries pass, ambiguities 9.
`everything` still has the two stale imports fixed in `0c9844a`; fold that in at the next re-merge.

**Compat break (not our bug):** main's #5866 makes `set_velocities!` call `set!(field, value, clock,
fields(model))`. NumericalEarth's `set!(::Field, ::Metadatum; kw...)` does not accept those positional
arguments, so it fell through to a broadcast and crashed. Patched on NumericalEarth.jl's fork
`everything` (`7b625d36`); upstream NumericalEarth has the same signature and will hit this when it
moves to a newer Oceananigans. The sandbox run this came up in is logged in
`NumericalEarth/CLAUDE.md`'s 2026-09-23 entry.

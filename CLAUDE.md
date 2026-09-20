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

| branch | PR | status (2026-09-20) |
|---|---|---|
| `pr/oblique-radiation` | [#5962](https://github.com/CliMA/Oceananigans.jl/pull/5962) `ObliqueRadiation` | **merged** |
| `pr/tracer-reservoir` | [#5964](https://github.com/CliMA/Oceananigans.jl/pull/5964) `TracerReservoir` | open |
| `tidal-forcing` | [#5970](https://github.com/CliMA/Oceananigans.jl/pull/5970) tidal astronomy/forcing/BCs | open |
| `low-pass-filter` | [#5971](https://github.com/CliMA/Oceananigans.jl/pull/5971) `LowPassFilter` | open |
| `fix-split-explicit-substep-clock` | [#5982](https://github.com/CliMA/Oceananigans.jl/pull/5982) advance clock through substeps | open |
| `pr-6024` | (Greg Wagner's PR, not ours) [#6024](https://github.com/CliMA/Oceananigans.jl/pull/6024) CATKE `Cᵇ` fix | open — fetched to cherry-pick onto `everything` for validation, see below |
| `obc/oblique-radiation` | — | superseded by `pr/oblique-radiation` after the #5962 refactor; keep or prune next time this repo is touched |
| `everything` | — | combined branch (all of the above merged together, conflict-free) that `NumericalEarthSandbox` consumes; not itself a PR |

## Session log

Newest entries at the bottom.

### 2026-09-17 — validated CliMA/Oceananigans.jl#6024 (CATKE `Cᵇ` fix)

Cherry-picked Greg Wagner's fix (commit `93d9dda30` from `pr-6024`) onto
`everything` as `9c54b4f49`, pushed to `fork`. Full validation (three-way
MAB M2 tide comparison, results posted to the PR, stratification
follow-up) lives in `NumericalEarth/CLAUDE.md`'s 2026-09-17/19 session
entry — this repo's side of the story is just the cherry-pick + push,
recorded here so the branch table above stays accurate.

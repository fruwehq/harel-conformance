# AGENTS.md — determa-state-conformance

Guidance for AI/coding agents working in this repository. (Tool-agnostic; not specific to any one assistant.)

## What this repo is
The **language-agnostic conformance suite** — the executable definition of *correct*
Determa State behavior. This repo, not any single implementation, is the arbiter (SPEC §2).
Layout:
- `conformance/01`–`NN` — **engine** cases: each `<case>/` has `machine.yaml` (the
  definition(s)) and `test.yaml` (a scenario of `send`/`advance`/… steps + `expect`).
- `conformance/cli/*` — **CLI** cases: a `cli.yaml` of steps run black-box against the
  implementation's `determa-state` binary, plus referenced machine files.
- `conformance/run_cli.py` — the black-box CLI runner.
- `VERSION` — the synchronized spec version this suite targets.

**No CI here.** Correctness is exercised by each implementation's harness, which fetches
this suite at the release tag matching its version.

## Determa in one paragraph
**Determa** is a family of tools for defining/running well-specified, verifiable behavior.
Its first product, **Determa State**, is a language-agnostic **statechart engine**
(Harel/UML lineage, PSiCC RTC semantics): one YAML/JSON machine runs identically under any
implementation because all are validated against *this* suite. Guards/action values are
**CEL**. An umbrella `determa` launcher dispatches `determa <product> …` → `determa-<product>`.

## Repositories (org `fruwehq`, local folders under `~/src/personal/`)
| Repo | Role |
|---|---|
| determa-state-spec | normative prose spec + schema. No CI. |
| **determa-state-conformance** (this) | the conformance suite. No CI. |
| determa-state-python | Python impl (dist `determa-state`, import `determa.state`). |
| determa-state-rust | Rust impl (crate `determa-state`). |
| determa | umbrella launcher (`python/`, `rust/`, `node/`). |

## Working rules (every Determa repo)
- **One issue → one PR**, branch → PR → **squash-merge**, linear history, resolve threads; `main` protected.
- **No AI/assistant attribution** anywhere (commits, PRs, comments, docs).
- **Conformance-first:** spec text → the case here → implementations. This repo is where a new behavior is pinned executably.
- **Synchronized SemVer** with spec + impls (currently **0.0.6**).
- **No abbreviations** in JSON/identifiers (`definition` not `def`); `config`, machine-keywords (`esvs`, `on_events`, …), and `def_id`/`def_version`/`spawn.def` deliberately kept pending a separate migration.

## Running the suite locally
```sh
# CLI cases against an installed implementation:
python conformance/run_cli.py --cmd "determa-state"          # or "python -m determa.state", or a rust binary path
python conformance/run_cli.py --cmd "determa-state" cli/01-turnstile   # one case
```
Engine cases are driven by each implementation's own harness (loads the definitions, runs
each `test.yaml` to quiescence, checks `expect`).

## Releasing
Bump `VERSION`, tag `vX.Y.Z` after merge. Implementations pin the suite at that tag
(python fetches it into `.cache/`; rust as a git submodule that CI force-pins to the tag).

## Pointers
- Coverage table + case index: `README.md`.
- The runner: `conformance/run_cli.py`. The spec it targets: `determa-state-spec/SPEC.md`.

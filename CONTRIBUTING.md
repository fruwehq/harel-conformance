# Contributing to harel-conformance

**harel-conformance** is the **language-agnostic conformance suite** — the executable
correctness target for the harel statechart engine. The prose specification lives in
[`fruwehq/harel`](https://github.com/fruwehq/harel) (`SPEC.md`, the JSON Schema, and
examples); this repository is the **normative** definition of correct behavior, and the
Python reference implementation ([`fruwehq/harel-python`](https://github.com/fruwehq/harel-python))
is correct iff it passes. There is **no CI here**; implementations consume this repo as a
pinned source of truth.

## What lives here

- `conformance/01`–`NN` — **engine** cases (SPEC §9). Each `<case>/` has:
  - `machine.yaml` (or versioned `v*.yaml` for migration) — the definition(s),
  - `test.yaml` — the scenario (`steps:` of `send`/`advance`/`upgrade` + `expect`),
  - `contracts/*.yaml` — optional, for contract-validation cases.
- `conformance/cli/<case>` — **CLI** cases (SPEC §13.6): a `cli.yaml` of steps plus the
  referenced `machine.yaml`.
- `conformance/run_cli.py` — the **black-box CLI runner**.

## Adding a case

**Engine case (SPEC §9):** add `conformance/<case>/machine.yaml` (one or more
`---`-separated definitions; the first is root) and `conformance/<case>/test.yaml` with
the scenario and `expect` blocks. Reuse a minimal machine fixture where possible.

**CLI case (SPEC §13.6):** add `conformance/cli/<case>/` with a `cli.yaml` (and the
referenced `machine.yaml`). Each step is an argv array plus `expect:` (`exit:`,
`json:`/`stdout:`, or the batch `stdin:`/`expect.stream:` form of §13.7). Remember argv
tokens are **strings** — quote numeric flags (e.g. `--steps, "1"`).

## Running the CLI runner

```sh
python conformance/run_cli.py --cmd "harel"                 # or "python -m harel", "node …"
python conformance/run_cli.py --cmd "python -m harel" 03-stepping   # one case
```

`--cmd` is the command that invokes the implementation's CLI (shell-quoted); optional
positional case names restrict the run. The runner shells out as a **subprocess**, so it
is truly language-agnostic and catches packaging/entry-point regressions. Exit code is
non-zero on any failure.

## Workflow

1. Branch from `main`, open a Pull Request, and **squash-merge** — `main` stays linear.
2. Resolve all review threads before merging.
3. **Never push to `main` directly.**
4. **No AI/assistant attribution anywhere** — not in commits, PR bodies, comments, or
   docs (no `Co-Authored-By:`, no "Generated with…"). Commits and PRs read as the
   author's own work.
5. A new case should land **after** the spec text it pins (link the `fruwehq/harel`
   issue/PR) and before, or alongside, the implementation that must pass it.

## Versioning

This repository carries the synchronized version in `VERSION` (currently `0.0.1`); this
repo's version tracks the spec version in lockstep.

> harel, harel-conformance, and harel-python share one synchronized SemVer version
> (currently pre-1.0 `0.0.x`). A release tags all three `vX.Y.Z` in lockstep; an
> implementation declares "implements harel spec vX.Y.Z" and pins the conformance suite
> at that tag.

## License

Contributions are made under the project's [MIT license](LICENSE).

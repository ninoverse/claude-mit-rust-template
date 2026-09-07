# Contributing

The rules that govern this repository live in [`CLAUDE.md`](CLAUDE.md) and
[`.claude/`](.claude/). They are written for Claude Code but they are not
agent-specific — they are the conventions, and they apply to humans identically.
This file is the short version and points at the authoritative one for each
topic.

## Setup

```bash
cargo install --locked just
just setup      # cargo-nextest, cargo-watch, cargo-deny, cargo-audit
just ci         # confirm a clean checkout passes
```

The toolchain installs itself: [`rust-toolchain.toml`](rust-toolchain.toml) pins
the channel, so the first `cargo` command pulls the right compiler.

Prefer a container? `docker compose up -d dev && docker compose exec dev just ci`,
or open the repo in VS Code and reopen in the devcontainer — the tooling above
is already built into the image.

## The loop

One branch, one commit, one PR, merged before the next begins. No stacked PRs.
Full rules in [`.claude/git-flow.md`](.claude/git-flow.md).

```bash
git switch main && git pull --ff-only
git switch -c <type>/<short-description>     # .claude/branch-naming.md
# ... change ...
just ci                                      # must pass before you push
git commit                                   # .claude/commit-conventions.md
git push -u origin <branch>
```

Then open a PR using the template. If Claude Code prepared the branch, it stops
before opening the PR by design — that step is yours.

## The four gates

```bash
just ci
```

`fmt-check` · `lint` · `test` · `deny`. All four, zero warnings, before you push.
CI runs the same recipes, one job per gate, plus an MSRV job.
See [`.claude/testing-requirements.md`](.claude/testing-requirements.md).

## Adding a crate

Follow the nine steps in
[`.claude/crate-workflow.md`](.claude/crate-workflow.md) — or run `/new-crate
<name>` in Claude Code, which executes them.

Two things that are easy to miss and that the gates will catch:

- `[lints] workspace = true` in the crate manifest. Without it the crate opts out
  of the workspace lints entirely.
- `#![allow(clippy::unwrap_used, clippy::expect_used)]` inside the test module.
  Those lints are workspace-wide and fire in test targets too.

## What gets declined

This template biases toward simplicity. Additions that only serve one downstream
project, abstractions with a single caller, and configuration for situations that
have not happened yet are likely to be turned down — see the Behavioral
Guidelines in [`CLAUDE.md`](CLAUDE.md).

## Dependency updates

Renovate opens them. Review the changelog rather than rubber-stamping, and check
that the MSRV job still passes — a dependency raising its own MSRV is the usual
reason that job goes red.

# Claude Code Rust Template

[![CI](https://github.com/ninoverse/claude-mit-rust-template/actions/workflows/ci.yml/badge.svg)](https://github.com/ninoverse/claude-mit-rust-template/actions/workflows/ci.yml)
[![Audit](https://github.com/ninoverse/claude-mit-rust-template/actions/workflows/audit.yml/badge.svg)](https://github.com/ninoverse/claude-mit-rust-template/actions/workflows/audit.yml)

Claude Code configuration scaffolding for **Rust cargo-workspace** projects.
Fork or copy this repo to start a new Rust project that ships with the latest
stable toolchain, opinionated lint/format/test commands, and Claude Code rule
files already wired up.

## What's bundled

| File | Purpose |
|------|---------|
| `Cargo.toml` | Workspace root. `members = ["crates/*"]`, shared `[workspace.package]`, `[workspace.dependencies]` and `[workspace.lints]`. |
| `justfile` | Task runner. Canonical form of every command; CI and the rules call these recipes. |
| `.cargo/config.toml` | Cargo aliases mirroring the justfile, plus a commented faster-linker block. |
| `.github/workflows/ci.yml` | The four gates as separate jobs, plus an MSRV job and a coverage artifact. |
| `.github/workflows/audit.yml` | Weekly `cargo audit` + `cargo deny check advisories` on a cron. |
| `rust-toolchain.toml` | Pins channel = `stable` so every contributor auto-pulls the latest stable Rust. |
| `rustfmt.toml` | Format config (edition 2024, 100-col, module-granular imports). |
| `clippy.toml` | MSRV pin for clippy lints. |
| `deny.toml` | `cargo-deny` config: allowed licenses, advisory denials, source restrictions. |
| `.gitignore` | Ignores `target/`. |
| `crates/` | Workspace member dir — add crates here via `cargo new --lib crates/<name>`. |
| `crates/example/` | Placeholder crate. A workspace with zero members is a hard cargo error, so this keeps the gates green on a fresh clone. Delete it *after* adding your first real crate. |
| `CLAUDE.md` | Top-level rules surfaced to Claude Code. |
| `.claude/*.md` | Per-task rule files (see table below). |
| `.claude/settings.json` | Permission allowlist + hooks: rustfmt on save, `cargo check` when Claude stops. |
| `.claude/commands/` | Project slash commands: `/gates`, `/new-crate`. |

## Bootstrap a project from this template

```bash
# 1. Clone and rename
git clone https://github.com/ninoverse/claude_mit_rust_template my-project
cd my-project
rm -rf .git && git init

# 2. Update workspace identity in Cargo.toml
#    - [workspace.package].repository
#    - [workspace.package].license (if not MIT)

# 3. Install the task runner and auxiliary tools (once per machine)
cargo install --locked just
just setup

# 4. Add your first crate, then drop the placeholder
just new-crate <your-crate>
rm -rf crates/example

# 5. Verify the toolchain and workspace
just ci
```

## Daily commands

The `justfile` is the single source of truth for every command — CI and the
`.claude/` rules call these recipes rather than repeating cargo invocations.

```bash
just            # list every recipe
just ci         # all four merge gates — run before every commit
just build      # build all crates
just check      # type-check, faster than build
just watch      # re-check on save
just fmt        # format in place
just test       # nextest (or cargo test) plus doc-tests
just doc        # build and open workspace docs
just audit      # CVE check
```

No `just`? `.cargo/config.toml` defines `cargo lint`, `cargo fmt-check` and
`cargo check-all`. There is no `cargo ci` equivalent — a cargo alias can only
wrap a single subcommand, so run the four gates in sequence.

## Rule files

| File | Purpose |
|------|---------|
| `.claude/git-flow.md` | The branch → commit → PR loop. One branch in flight, no stacked PRs |
| `.claude/branch-naming.md` | Branch prefix and format conventions |
| `.claude/commit-conventions.md` | Conventional Commits rules |
| `.claude/pr-guidelines.md` | PR title, description template, size guidance |
| `.claude/testing-requirements.md` | Test gates (fmt, clippy, nextest, deny) |
| `.claude/file-naming.md` | Workspace and per-crate layout |
| `.claude/code-review.md` | Review checklist (lint, error handling, unsafe, docs, deps) |
| `.claude/crate-workflow.md` | Step-by-step procedure to add a crate |
| `.claude/execution-order.md` | What order to build things in, and one PR per what |

# Testing Requirements

## Before merging any change

```bash
just ci
```

That runs the four gates in order:

- [ ] `just fmt-check` — formatting is clean
- [ ] `just lint` — clippy, warnings as errors
- [ ] `just test` — nextest *(falls back to `cargo test` if not installed)* plus doc-tests
- [ ] `just deny` — licenses + advisories

All four must pass before pushing the branch. The underlying cargo commands live
in the `justfile`; call the recipe rather than copying them.

## Test layout

| Test type | Location | When to use |
|-----------|----------|-------------|
| Unit | `#[cfg(test)] mod tests { … }` inline at the bottom of the file under test | Testing private functions or internal logic |
| Integration | `crates/<name>/tests/<feature>.rs` | Testing the crate's public API end-to-end. Each file is compiled as a separate binary. |
| Doc test | `///` doc comment on a public item | Verifying that documented usage examples actually compile and run |
| Property | with `proptest` crate, inside unit or integration tests | Invariant-style tests across a generated input space |
| Benchmark | `crates/<name>/benches/<name>.rs` | Performance regression tracking. Optional. |

## Running specific test types

```bash
cargo test --doc                              # doc-tests only
cargo nextest run -p <crate>                  # one crate
cargo nextest run -p <crate> <test_name>      # one test
cargo test --test <integration_file>          # one integration file
```

## Watching tests during development

```bash
cargo watch -x 'nextest run --workspace'
```

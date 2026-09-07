# Security Policy

## Reporting a vulnerability

Report privately through
[GitHub Security Advisories](https://github.com/ninoverse/claude-mit-rust-template/security/advisories/new).
Do not open a public issue.

Expect an acknowledgement within a week. If the report is valid, you will get an
estimated fix timeline and credit in the advisory unless you ask otherwise.

## Supported versions

This is a template, not a deployed service. Only `main` is maintained — there
are no release branches to backport to. Projects created from it are the
responsibility of whoever created them.

## What is in scope

The security-relevant surface of a template is mostly its supply chain and its
defaults:

- `deny.toml` allowing a license or source it should not
- A CI workflow with excessive `permissions:`, or one that could execute
  untrusted input from a pull request
- A pinned GitHub Action or base image with a known vulnerability
- Anything in `.claude/settings.json` that would let an agent take a
  destructive or credential-touching action without a prompt
- A `Dockerfile` default that weakens the shipped image — running as root,
  leaking build-time secrets into a layer

## What is not

- Advisories against dependencies of *your* project. Run `just audit`; the
  weekly `audit.yml` workflow covers this repository itself.
- The absence of a hardening measure that was never claimed. Please open a
  feature request instead.

## Running the checks yourself

```bash
just deny     # licenses and advisories
just audit    # known CVEs in the dependency tree
```

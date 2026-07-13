# Contributing to the PTI Specification

Thank you for helping evolve Portable Trust Infrastructure as an open standard.

## Before you start

1. Read [PTI Origin](origin/index.md) for historical context and stewardship model.
2. Read [Governance principles](governance/governance-principles.md) and [Public governance statement](governance/public-governance-statement.md).
3. Check [RFC index](rfcs/index.md) for related work.

## How to propose a change

### Specification or protocol (RFC)

1. Open an issue describing the problem, scope, and compatibility impact.
2. Copy the RFC template from `rfcs/`.
3. Submit a pull request with status **Draft**.
4. Work with maintainers through **Review** → **Proposed** → **Stable** per [RFC process](governance/rfc-process.md).

### Errata (typos, clarifications)

Open a PR with a minimal fix and reference the normative section. Non-normative clarifications **MAY** merge with a single maintainer approval.

### Conformance tests

Submit tests that map to normative **MUST** requirements. See [Conformance program](governance/conformance-program.md).

### Security

Do **not** file public issues for exploitable vulnerabilities. Follow [Security disclosure](governance/security-disclosure.md).

## Review expectations

- Use RFC 2119 keywords consistently in normative text.
- Include conformance test IDs where behavior changes.
- Document migration paths for implementers.
- Disclose employer affiliation when relevant.

## Unified documentation site

Published documentation lives at [docs.tumitrust.com/pti](https://tumitrust.com/docs/pti/) alongside TumiTrust implementation guides. This repository is the **source of truth** for PTI specification content; the monorepo vendors it via git subtree.

## Contact

- Governance: see [Working Group](governance/working-group.md)
- Security: [Security disclosure](governance/security-disclosure.md)
- General: [Community participation](governance/community-participation.md)

---
title: Contributing to PTI
description: How to participate in the Portable Trust Infrastructure specification — open GitHub contributions and governance paths.
sidebar_position: 0
slug: /pti/contributing/
displayed_sidebar: ptiSidebar
---

# Contributing to Portable Trust Infrastructure

Portable Trust Infrastructure (PTI) is an **open technical specification**. This project welcomes community participation directly on GitHub — you do not need a lengthy onboarding process to ask questions, report errata, or propose improvements.

The public specification repository is **[github.com/tumitrust/pti-specification](https://github.com/tumitrust/pti-specification)**. Published documentation appears at **[tumitrust.com/docs/pti](https://tumitrust.com/docs/pti/)** on the main TumiTrust website.

## Quick start

| You want to… | Do this |
|--------------|---------|
| Fix a typo or clarify wording | [Open a PR](https://github.com/tumitrust/pti-specification/pulls) on the specification repo |
| Ask a question or suggest an idea | [Open an issue](https://github.com/tumitrust/pti-specification/issues) |
| Propose a protocol or schema change | Open an issue, then submit an RFC pull request |
| Report a security vulnerability | **Do not** use public issues — see [Security disclosure](/pti/governance/security-disclosure) |

## Who can contribute

Anyone **MAY** contribute without organizational membership or a commercial relationship to TumiTrust. Disclose employer affiliation when relevant in issues and pull requests.

## What you can contribute

| Contribution | Where | Process |
|--------------|-------|---------|
| **Errata & clarifications** | GitHub PR | Lightweight review |
| **Specification / RFC** | [`/pti/rfcs/`](/pti/rfcs/) | [RFC process](/pti/governance/rfc-process) |
| **Conformance tests** | Conformance suite | [Conformance Program](/pti/governance/conformance-program) |
| **Governance docs** | [`/pti/governance/`](/pti/governance/) | PR + Working Group notice |

Detailed step-by-step for normative changes: [Contribution process](/pti/governance/contribution-process).

## Proposing a specification change (RFC path)

1. [Open a GitHub issue](https://github.com/tumitrust/pti-specification/issues) with the problem statement and compatibility notes.
2. Fork the [pti-specification](https://github.com/tumitrust/pti-specification) repository.
3. Add an RFC under `rfcs/` using the template, status **Draft**.
4. Request review; maintainers advance status per [RFC process](/pti/governance/rfc-process).

## Review expectations

- RFC 2119 keywords in normative text.
- Conformance test IDs when behaviour changes.
- Migration notes for implementers.

## Contact

- **GitHub (preferred):** [Issues](https://github.com/tumitrust/pti-specification/issues) and [pull requests](https://github.com/tumitrust/pti-specification/pulls)
- **Governance:** [Working Group](/pti/governance/working-group)
- **Security:** [Security disclosure](/pti/governance/security-disclosure) — coordinated disclosure only
- **General:** [Community participation](/pti/governance/community-participation)

# Portable Trust Infrastructure (PTI) Specification

Portable Trust Infrastructure (PTI) is an open technical specification for enabling **portable, programmable, and interoperable trust** across digital ecosystems.

PTI was pioneered by **TumiTrust Technologies**, which developed the initial architecture, terminology, specifications, and reference implementation. This repository contains the evolving PTI specification and welcomes contributions from architects, researchers, engineers, institutions, and ecosystem participants.

## Status

| | |
|---|---|
| **Version** | 1.0 |
| **Status** | Stable |
| **Published** | [docs.tumitrust.com/pti](https://tumitrust.com/docs/pti/) |

## Repository layout

```
origin/                   Historical foundation and stewardship
introduction/             Why PTI exists, design principles
specification/v1.0/      Normative specification bundle
reference-architecture/   Conceptual models (informative)
rfcs/                     Protocol and schema RFCs
governance/               Ecosystem governance and process
conformance/              Profiles, certification, tests
build-your-pti/           Implementation guidance
comparisons/              Positioning vs identity, KYC, bureaus
glossary/                 Normative terminology
```

## Relationship to TumiTrust

| | PTI | TumiTrust |
|---|-----|-----------|
| **Role** | Open specification & ecosystem | Pioneering company & reference implementation |
| **Audience** | Any implementer | Customers, partners, operators |
| **Docs** | `/pti/` | `/tumitrust/` |

TumiTrust is the **founding steward**, not the owner of PTI. Conformance is measured against public RFCs and tests, not vendor affiliation.

## Contributing

Read [CONTRIBUTING.md](./CONTRIBUTING.md) and the published [Contributing guide](https://tumitrust.com/docs/pti/contributing/).

- Propose changes via **RFCs**
- Report security issues through coordinated disclosure
- Join community participation via the Working Group process

## Unified documentation site

Published documentation is built from the [tumitrust](https://github.com/tumitrust/tumitrust) monorepo and served at **docs.tumitrust.com** (today: `tumitrust.com/docs/`). This repository is the **standalone source tree** for PTI specification content, synced via git subtree.

## License

Specification text copyright © TumiTrust Ltd. Redistribution and implementation **SHOULD** follow governance and trademark policies in `governance/`.

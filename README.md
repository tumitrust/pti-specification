# Portable Trust Infrastructure (PTI) Specification

Portable Trust Infrastructure (PTI) is an open technical specification for enabling **portable, programmable, and interoperable trust** across digital ecosystems.

PTI was pioneered by **TumiTrust Technologies**, which developed the initial architecture, terminology, specifications, and reference implementation. This repository is **open for community participation** — welcome contributions from architects, researchers, engineers, institutions, and ecosystem participants.

## Status

| | |
|---|---|
| **Version** | 1.0 |
| **Status** | Stable |
| **Published** | [tumitrust.com/docs/pti](https://tumitrust.com/docs/pti/) |

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

This repository is public. You do **not** need a formal onboarding process to get started:

- **[Open an issue](https://github.com/tumitrust/pti-specification/issues)** — questions, errata, or ideas
- **[Submit a pull request](https://github.com/tumitrust/pti-specification/pulls)** — typos, clarifications, RFC drafts, tests

Read [CONTRIBUTING.md](./CONTRIBUTING.md) for lightweight guidance. Larger specification changes follow the [RFC process](rfcs/index.md); day-to-day fixes and discussion happen here on GitHub.

## Published documentation

Specification pages are published on the main TumiTrust site at **[tumitrust.com/docs/pti](https://tumitrust.com/docs/pti/)** (under `/docs/` on [tumitrust.com](https://tumitrust.com)), alongside TumiTrust implementation guides. This repository is the standalone source tree for PTI content, synced from the [tumitrust](https://github.com/tumitrust/tumitrust) monorepo via git subtree.

## License

Specification text copyright © TumiTrust Ltd. Redistribution and implementation **SHOULD** follow governance and trademark policies in `governance/`.

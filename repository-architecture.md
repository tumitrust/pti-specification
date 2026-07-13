---
title: Repository architecture
description: How PTI specification content is organized for independent development and unified publication at tumitrust.com/docs/.
sidebar_position: 1
slug: /pti/repository-architecture/
displayed_sidebar: ptiSidebar
---

# Documentation repository architecture

This page describes how **Portable Trust Infrastructure (PTI)** specification content and **TumiTrust Platform** implementation documentation are separated in source control while remaining a **single documentation experience** for visitors.

## Design goals

| Goal | Approach |
|------|----------|
| PTI as open specification | Independent `docs/docs/pti/` corpus, vendor-neutral editorial rules |
| TumiTrust as reference implementation | Separate `docs/docs/tumitrust/` corpus with implementation notes |
| One visitor experience | Single Docusaurus site at **[tumitrust.com/docs/](https://tumitrust.com/docs/)** |
| Contributor-friendly | No git submodules; git subtree for future repo split |
| Stable URLs | Redirects in `docusaurus.config.ts` for legacy paths |

## Current layout (monorepo)

```
tumitrust/docs/
├── docs/
│   ├── pti/                    ← open specification (future pti-specification repo)
│   │   ├── origin/
│   │   ├── introduction/
│   │   ├── specification/v1.0/
│   │   ├── reference-architecture/
│   │   ├── rfcs/
│   │   ├── governance/
│   │   ├── conformance/
│   │   ├── build-your-pti/
│   │   └── …
│   └── tumitrust/              ← commercial implementation docs
│       ├── product-overview/
│       ├── platform/
│       ├── developer-guides/
│       ├── solutions/
│       └── …
├── sidebars.ts                 ← ptiSidebar + tumitrustSidebar
├── docusaurus.config.ts
└── scripts/sync-pti-subtree.sh ← export PTI to standalone repo
```

**Staff-only** engineering runbooks live in `tumitrust/staff-docs/` — not in this public build.

## Published routes

| Corpus | Route prefix | Audience |
|--------|--------------|----------|
| PTI specification | `/pti/` | Architects, researchers, implementers, standards bodies |
| TumiTrust platform | `/tumitrust/` | Customers, partners, integrators, operators |

Homepage dual-lane entry: `/` (docs root).

## Future repository split

### Repository 1: `github.com/tumitrust/pti-specification`

Purpose: independently cloneable PTI specification.

```
pti-specification/
├── README.md
├── CONTRIBUTING.md
├── docs/                 ← subtree copy of docs/docs/pti/
├── schemas/              ← reference JSON schemas (as published)
└── examples/
```

### Repository 2: `tumitrust` monorepo (this repository)

Purpose: TumiTrust product docs + vendored PTI snapshot for unified site build.

```
tumitrust/docs/docs/tumitrust/
```

### Synchronization: git subtree (not submodules)

**Export** (maintainers, from tumitrust repo root):

```bash
./docs/scripts/sync-pti-subtree.sh push
```

**Import** (unified site build, CI):

```bash
./docs/scripts/sync-pti-subtree.sh pull
```

Contributors **never** run `git clone --recursive`.

## Docusaurus configuration

Single `@docusaurus/preset-classic` instance with:

- `routeBasePath: '/'` and site `baseUrl: '/docs/'`
- Dual sidebars (`ptiSidebar`, `tumitrustSidebar`)
- Navbar: **PTI** | **v1.0 Stable badge** | **TumiTrust** | Ecosystem | Developers
- Local search indexes both corpora
- Legacy redirects preserve bookmarks from pre-split IA

Multi-instance Docusaurus is **not required** unless PTI needs a fully independent build artifact before the subtree repo exists.

## Editorial separation

Read [PUBLIC_DOCUMENTATION_NOTICE.md](https://github.com/tumitrust/kops/blob/main/tumitrust/docs/PUBLIC_DOCUMENTATION_NOTICE.md):

- PTI pages use RFC 2119 normative language
- TumiTrust pages open with an implementation note linking to the relevant PTI spec

## Related

- [Contributing](/pti/contributing/)
- [Specification vs implementation](/pti/governance/specification-vs-implementation/)
- [Reference implementations](/pti/governance/reference-implementations/)

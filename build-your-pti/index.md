---
sidebar_position: 1
title: Build Your PTI
description: Overview for independent PTI implementers.
---

# Build Your PTI

This section guides **independent implementers** building Portable Trust Infrastructure without relying on a specific vendor stack. It translates PTI RFCs into an actionable build path from zero to certified deployment.

## Who this is for

| Audience | Goal |
|----------|------|
| **Fintech / MFI engineering** | Operate producer + consumer APIs for partner network |
| **Government IT** | National trust registry with federation |
| **System integrators** | Deliver accredited operator for clients |
| **Open-source contributors** | Reference implementation components |

## What you are building

A PTI implementation is **not** a monolithic "score engine." At minimum you build:

1. **Event ingest plane** — validate and persist trust events (RFC-003)
2. **Identity resolution** — assign and link PTI-IDs (RFC-011)
3. **Trust graph + processing** — signals and context-scoped outcomes (RFC-005)
4. **Lookup API** — consumer reports with evidence (RFC-004, RFC-012)
5. **Control plane** — consent, audit, security, privacy (RFC-007, RFC-008, RFC-009)

Optional: **federation gateway** (RFC-006) for Enterprise and Government profiles.

## Recommended build sequence

```
Week 1–2   Context catalogue + policy engine (RFC-002)
Week 3–5   Event ingest + identity binding (RFC-003, RFC-011)
Week 6–8   Graph storage + scoring pipeline (RFC-005)
Week 9–11  Lookup API + evidence signing (RFC-004, RFC-012)
Week 12–14 Governance + privacy workflows (RFC-007, RFC-009)
Week 15–16 Security hardening + OpenAPI (RFC-008, RFC-010)
Week 17+   Conformance self-test → certification (optional)
```

Adjust timeline for team size and profile target.

## Architecture decision record

Before coding, decide:

| Decision | Options | RFC guidance |
|----------|---------|--------------|
| Deployment | Cloud, on-prem, hybrid | RFC-001 topologies |
| Graph store | Relational edges, property graph | RFC-005 |
| Auth | API keys, OAuth 2.0 | RFC-008 |
| Signing | Software KMS, HSM | RFC-008, Government profile |
| Scoring | Rules engine, ML | Versioned derivation rules in evidence |
| Federation | None, bilateral, hub-spoke | RFC-006, profile |

Document choices in your conformance statement.

## Minimum viable profile

Most teams target **PTI Core** first:

- One primary context (e.g., `lending`)
- API ingest only
- `basic` + `detailed` lookup tiers
- Signed evidence without public verify (optional)
- Manual DSAR initially

Expand to Enterprise when multi-tenant SLA and webhooks are required.

## Documentation map

| Document | Purpose |
|----------|---------|
| [Required architecture](./required-architecture) | Layer and service boundaries |
| [Required components](./required-components) | Services, data stores, integrations |
| [Security requirements](./security-requirements) | RFC-008 implementation checklist |
| [Privacy requirements](./privacy-requirements) | RFC-009 implementation checklist |
| [Interoperability rules](./interoperability-rules) | Exchange and API compatibility |
| [Implementation examples](./implementation-examples) | Reference patterns |
| [Migration examples](./migration-examples) | Legacy system cutover |

## RFC reading list

Read in order: RFC-001 → RFC-002 → RFC-003 → RFC-011 → RFC-005 → RFC-012 → RFC-004 → RFC-007/008/009 → RFC-010 → RFC-006 (if federating).

## Conformance path

1. Complete [conformance test checklists](/pti/conformance/conformance-tests)
2. Publish OpenAPI and conformance statement
3. Apply for [certification](/pti/conformance/certification-guide) when institution contracts require it

## Vendor neutrality commitment

Reference implementations **MAY** ship as open source. Certification **MUST NOT** require proprietary licenses. Use this documentation with any stack — the RFCs define behavior, not frameworks.

## Related

- [PTI RFC index](/pti/rfcs/)
- [Conformance profiles](/pti/conformance/profiles)

---
sidebar_position: 2
title: Conformance Profiles
description: PTI Core, Enterprise, Government, and Edge conformance profiles.
---

# PTI Conformance Profiles

PTI defines four conformance profiles. Each profile inherits requirements from lower tiers. An implementation **MUST** declare exactly one primary profile and **MAY** publish additive capability flags.

## Profile summary

| Profile | Intended deployer | Federation | Typical scale |
|---------|-------------------|------------|---------------|
| **Core** | Fintech, MFI, startup operator | No | Single jurisdiction |
| **Enterprise** | Multi-tenant SaaS, sector bureaus | Optional bilateral | Regional |
| **Government** | National registry, public agency | Mandatory accredited | National |
| **Edge** | Offline-first, field operations | Sync to hub | Local with periodic uplink |
| **Extended** | Ecosystem operators, marketplaces | Multi-party federation | Global / multi-region |

## Capability matrix

Legend: **M** = mandatory, **O** = optional, **—** = not applicable

| Capability | Core | Enterprise | Government | Edge |
|------------|------|------------|------------|------|
| RFC-001 architecture | M | M | M | M |
| RFC-002 primary contexts (≥1) | M | M | M (≥5) | M (≥1) |
| RFC-002 lens contexts | O | M (≥3) | M (all configured) | O |
| RFC-003 API ingest | M | M | M | M |
| RFC-003 webhook ingest | O | M | M | O |
| RFC-003 CSV batch ingest | O | M | M | M |
| RFC-004 subject search | M | M | M | M |
| RFC-004 report tiers `basic` + `detailed` | M | M | M | M |
| RFC-004 tier `predictive` | O | M | M | — |
| RFC-004 tier `screening_dossier` | O | M | M | O |
| RFC-005 trust graph | M | M | M | M (local partition) |
| RFC-006 trust exchange | — | O (bilateral) | M (accredited) | O (sync packages) |
| RFC-007 consent + audit | M | M | M | M |
| RFC-007 DSAR automation | O | M | M | O (deferred sync) |
| RFC-008 TLS + scoped credentials | M | M | M | M |
| RFC-008 annual penetration test | — | M | M | O |
| RFC-008 HSM signing keys | — | O | M | — |
| RFC-009 purpose binding | M | M | M | M |
| RFC-009 cross-border policy gates | O | M | M | O |
| RFC-010 API `/v1/` + deprecation headers | M | M | M | M |
| RFC-011 PTI-ID + entity binding | M | M | M | M |
| RFC-011 manual merge/split workflow | O | M | M | O |
| RFC-012 signed evidence manifests | M | M | M | M |
| RFC-012 public verification endpoint | O | M | M | O (hub-verified) |
| Multi-tenant isolation | M | M | M | M (device + hub) |
| Sandbox environment | M | M | M | M |
| OpenAPI publication | M | M | M | M | M |

## PTI Extended

**Audience:** Ecosystem operators extending PTI with marketplace capabilities, third-party signal vendors, or multi-region accredited federation beyond single-government scope.

### Inherits Government, plus mandatory

- Published connector marketplace or signal vendor onboarding API
- Revenue-share or metering interoperability (abstract billing events)
- Multi-region federation with documented cross-border policy matrix
- AI agent scoped authentication (RFC-aligned agent tokens)
- SDK generation from published OpenAPI for ≥2 languages

### Optional capabilities

- Confidential computing for scoring
- Workflow template marketplace
- Third-party report pack distribution

See [implementation badges](/pti/conformance/implementation-badges) for **PTI Extended Certified**.

## PTI Core

**Audience:** Organizations launching a minimal interoperable trust network.

### Mandatory capabilities

- Ingest trust events via API for at least one primary context
- Resolve `entity_id` → `pti_id`
- Generate `basic` and `detailed` lookup reports with signed evidence
- Enforce context enablement and consumer entitlements
- Operate consent validation where configured
- Maintain append-only audit logs for lookups

### Optional capabilities

- Webhook and CSV ingest
- Predictive tier
- Public verification endpoint
- Lens contexts

### Out of scope

- Accredited federation
- HSM requirements
- Full DSAR automation (manual process allowed with documented SLA)

## PTI Enterprise

**Audience:** Regional operators serving multiple producers and consumers with SLA commitments.

### Inherits Core, plus mandatory

- Webhook and CSV ingest channels
- At least three lens contexts operational
- Predictive report tier with `model_version` in evidence
- DSAR automation (access, export, erasure propagation)
- Annual third-party penetration test
- Public evidence verification endpoint
- Manual identity merge/split operator workflow
- Cross-border policy configuration

### Optional capabilities

- Bilateral trust exchange (RFC-006)
- Screening dossier tier
- HSM signing keys

## PTI Government

**Audience:** National or supranational accredited trust infrastructure.

### Inherits Enterprise, plus mandatory

- Minimum five primary contexts in production catalogue
- Accredited federation with published JWKS and PRL polling ≤ 6 hours
- HSM-backed manifest signing keys
- Screening dossier tier where lawful
- Full DSAR within statutory timelines
- Independent governance audit annually
- 99.9% lookup API availability SLA (excluding declared maintenance)

### Optional capabilities

- Full evidence exchange profile
- Confidential computing for scoring

## PTI Edge

**Audience:** Field deployments with intermittent connectivity — agricultural co-ops, rural MFIs, event capture offline.

### Inherits Core constraints, with differences

- **Local graph partition** — events and outcomes stored on edge node; sync to hub via exchange packages
- **Deferred DSAR** — erasure propagated on next sync within 72 hours
- **Predictive tier** — not applicable on device; hub may upgrade after sync
- **Verification** — may delegate to hub `reports.verify` for edge-generated report IDs

### Mandatory edge-specific

- Offline ingest queue with idempotency
- Conflict resolution on sync (server wins on binding; client wins on timestamp for duplicate events with proof)
- Tamper-evident local audit log

## Declaring profile capabilities

Conformance statements **MUST** use:

```json
{
  "pti_spec_version": "1.0",
  "profile": "enterprise",
  "primary_contexts": ["lending", "merchant", "employment"],
  "lens_contexts": ["digital_platform", "risk_compliance", "education"],
  "exchange": { "enabled": true, "profiles": ["manifest_only", "evidence_summary"] },
  "limitations": []
}
```

## Profile migration

| From | To | Typical work |
|------|-----|--------------|
| Core | Enterprise | DSAR automation, webhook, predictive tier, pen test |
| Enterprise | Government | HSM, accredited federation, expanded contexts |
| Core | Edge | Local queue, sync protocol, hub verification |

Migration **MUST** re-run conformance tests for target profile.

## Related

- [Conformance overview](./index)
- [Certification guide](./certification-guide)
- [Conformance tests](./conformance-tests)

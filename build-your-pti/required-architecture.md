---
sidebar_position: 2
title: Required Architecture
description: Required architecture for independent PTI implementations.
---

# Required Architecture

This document specifies the **required logical architecture** for PTI-compatible implementations. Physical deployment may vary; logical boundaries **MUST NOT** be bypassed.

## Layer model

```
                    ┌──────────────────────┐
                    │   Consumer clients   │
                    └──────────┬───────────┘
                               │
┌──────────────────────────────▼──────────────────────────────┐
│ LOOKUP PLANE (RFC-004, RFC-012)                             │
│  Search · Generate · Verify · Report cache                  │
└──────────────────────────────┬──────────────────────────────┘
                               │
┌──────────────────────────────▼──────────────────────────────┐
│ PROCESSING PLANE (RFC-005)                                  │
│  Scoring · Signal derivation · Lens refresh · Outcomes      │
└──────────────────────────────┬──────────────────────────────┘
                               │
┌──────────────────────────────▼──────────────────────────────┐
│ INGEST PLANE (RFC-003)                                      │
│  API · Webhook · CSV · Connector adapters                   │
└──────────────────────────────┬──────────────────────────────┘
                               │
┌──────────────────────────────▼──────────────────────────────┐
│ IDENTITY PLANE (RFC-011)                                    │
│  PTI-ID · Entity binding · Match · Review queue             │
└──────────────────────────────┬──────────────────────────────┘
                               │
┌──────────────────────────────▼──────────────────────────────┐
│ CONTROL PLANE (RFC-007, RFC-008, RFC-009)                   │
│  Policy · Consent · Audit · Auth · Privacy · Metering       │
└──────────────────────────────┬──────────────────────────────┘
                               │
                    ┌──────────▼───────────┐
                    │  Graph + event store   │
                    └──────────────────────┘
```

## Mandatory service boundaries

| Service | Responsibility | **MUST NOT** |
|---------|----------------|--------------|
| **Ingest gateway** | Auth, validation, idempotency | Compute scores |
| **Identity resolver** | PTI-ID assignment | Expose bulk export API |
| **Graph writer** | Persist nodes/edges | Skip context checks |
| **Scoring worker** | Derive signals/outcomes | Accept consumer requests |
| **Lookup API** | Reports + evidence | Ingest events |
| **Policy engine** | Entitlements, consent, purpose | Store long-term PII unnecessarily |
| **Audit logger** | Append-only security/privacy events | Allow tenant cross-read |
| **Signing service** | Manifest signatures | Hold application session state |

Services **MAY** co-reside in one process in Core deployments if boundaries enforced by module isolation and separate credentials.

## Data stores

| Store | Contents | Requirements |
|-------|----------|--------------|
| **Event store** | Immutable trust events | Per-tenant partition |
| **Graph store** | Nodes, edges, metadata | Point-in-time queries |
| **Outcome cache** | Materialized scores | Keyed by pti_id + context |
| **Report store** | Immutable report + manifest | Content-addressed hash |
| **Consent store** | Consent records | Fast withdraw propagation |
| **Audit store** | Lookup and admin events | WORM or append-only |
| **Credential store** | Hashed API keys, JWKS | Encrypted at rest |

Edge deployments **MAY** use embedded databases for local partition with sync to hub stores.

## Network zones

| Zone | Exposure | Components |
|------|----------|------------|
| **Public** | Internet | Lookup API, webhook receivers, verify endpoint |
| **Partner** | Allowlisted | High-volume ingest, CSV upload |
| **Internal** | Private | Workers, graph, scoring |
| **Control** | Restricted | Policy admin, identity review, signing |

All zones **MUST** use TLS for HTTP traffic.

## Trust lifecycle wiring

1. Ingest gateway → policy (context enabled?) → identity (resolve entity) → event store → graph writer
2. Graph writer → async queue → scoring worker → outcome cache
3. Lookup API → policy (entitlement, consent) → outcome cache + graph explain → evidence builder → signing service → report store

## Federation extension (optional)

```
┌─────────────┐     mTLS      ┌─────────────┐
│ Operator A  │◄────────────►│ Operator B  │
│ exchange gw │   packages    │ exchange gw │
└─────────────┘               └─────────────┘
```

Exchange gateway **MUST** verify signatures before merging foreign packages into lookup responses.

## High availability

| Profile | Lookup availability | RPO / RTO guidance |
|---------|---------------------|------------------|
| Core | Best effort | RPO 24h, RTO 8h |
| Enterprise | 99.5% | RPO 1h, RTO 2h |
| Government | 99.9% | RPO 15m, RTO 1h |

## Observability

Implementations **SHOULD** emit:

- Ingest acceptance rate by context
- Scoring lag (ingested_at → outcome refresh)
- Lookup latency p95 by tier
- Evidence verification success rate
- Consent withdraw propagation time

## Anti-patterns (non-conformant)

| Anti-pattern | Why it fails |
|--------------|--------------|
| Monolith consumer ingests via lookup API | Violates RFC-001 role separation |
| Scores without graph edges | Cannot produce RFC-012 evidence |
| Shared DB row without tenant_id | Cross-tenant leakage risk |
| Unsigned PDF reports | No verification hook |
| Global context-off flag only in UI | Must enforce in ingest gateway |

## Related

- [Required components](./required-components)
- [RFC-001 Architecture](/pti/rfcs/rfc-001-architecture)

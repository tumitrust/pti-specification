---
sidebar_position: 3
title: Required Components
description: Required components for PTI implementations.
---

# Required Components

This document lists **required software components** for each conformance profile. Implementations **MAY** substitute equivalent technology if behavioral requirements are met.

## Component catalogue

### 1. Ingest gateway

| Attribute | Requirement |
|-----------|-------------|
| **Purpose** | Accept trust events from producers |
| **Protocols** | HTTPS JSON (mandatory); webhook HMAC (Enterprise+) |
| **Validation** | JSON Schema per event catalogue |
| **Outputs** | Acceptance receipt with `pti_id` |

**Core:** API only. **Enterprise+:** webhook + CSV batch processor.

### 2. Event catalogue service

| Attribute | Requirement |
|-----------|-------------|
| **Purpose** | Versioned registry of `event_type` → schema |
| **Versioning** | `catalogue_version` per RFC-010 |
| **Access** | Read-only for producers; admin publish workflow |

Minimum: one primary context with ≥3 event types (e.g., `loan_disbursement`, `loan_repayment`, `loan_default`).

### 3. Identity resolution service

| Attribute | Requirement |
|-----------|-------------|
| **Purpose** | PTI-ID lifecycle and entity binding |
| **Inputs** | `entity_id`, search attributes |
| **Outputs** | `pti_id`, confidence, review tickets |
| **Storage** | Hashed strong identifiers |

Enterprise+ **MUST** include operator review UI or API for merge/split.

### 4. Graph persistence layer

| Attribute | Requirement |
|-----------|-------------|
| **Purpose** | Store RFC-005 nodes and edges |
| **Operations** | `append_event`, `explain_outcome`, `timeline` |
| **Integrity** | No orphan events; immutable edges |

Acceptable technologies: PostgreSQL with adjacency tables, Neo4j, Amazon Neptune, etc.

### 5. Scoring / processing workers

| Attribute | Requirement |
|-----------|-------------|
| **Purpose** | Derive signals and materialize outcomes |
| **Trigger** | Queue messages on new events |
| **Rules** | Versioned `derivation_rule` strings |
| **Idempotency** | Safe replay on duplicate queue delivery |

ML models **MAY** be used; `model_version` **MUST** appear in predictive tier evidence.

### 6. Outcome cache

| Attribute | Requirement |
|-----------|-------------|
| **Purpose** | Fast lookup reads |
| **Key** | (`pti_id`, `context_id`, `outcome_version`) |
| **Invalidation** | On relevant event or rule version bump |

### 7. Lookup API service

| Attribute | Requirement |
|-----------|-------------|
| **Endpoints** | `subjects.search`, `reports.generate`, `reports.get` |
| **Optional** | `reports.verify` (Enterprise+) |
| **Auth** | Consumer-scoped credentials |
| **Contract** | OpenAPI 3.1 published |

### 8. Evidence builder + signing service

| Attribute | Requirement |
|-----------|-------------|
| **Purpose** | Construct and sign RFC-012 manifests |
| **Canonicalization** | JCS before sign |
| **Algorithms** | Ed25519 or RSA-3072+ |
| **Isolation** | Separate from lookup read path |

Government profile **MUST** use HSM-backed signing.

### 9. Policy / entitlement engine

| Attribute | Requirement |
|-----------|-------------|
| **Evaluates** | Context enablement, tier, purpose, consent |
| **Integration** | Called synchronously on ingest and lookup |
| **Default** | Deny |

Centralized engine **RECOMMENDED** over scattered conditional logic.

### 10. Consent service

| Attribute | Requirement |
|-----------|-------------|
| **Stores** | `consent_id`, scope, timestamps |
| **API** | Grant, withdraw, query |
| **Propagation** | Cache invalidation ≤ 15 minutes |

### 11. Audit logger

| Attribute | Requirement |
|-----------|-------------|
| **Events** | Lookups, consent changes, admin actions |
| **Properties** | Append-only, tenant-scoped read |
| **Retention** | Per governance schedule |

### 12. DSAR workflow (Enterprise+)

| Attribute | Requirement |
|-----------|-------------|
| **Handles** | Access, export, erasure, rectification |
| **Orchestration** | Graph + report + consent stores |
| **SLA** | Published in privacy policy |

Core **MAY** use manual ticket workflow with same outcomes.

### 13. Federation gateway (optional)

| Attribute | Requirement |
|-----------|-------------|
| **Purpose** | Send/receive RFC-006 packages |
| **Components** | JWKS cache, PRL poller, signature verifier |
| **Required for** | Government profile; optional Enterprise |

### 14. Edge sync agent (Edge profile)

| Attribute | Requirement |
|-----------|-------------|
| **Purpose** | Offline queue uplink, conflict resolution |
| **Protocol** | HTTPS package upload |
| **Local** | Embedded event store + audit |

### 15. Sandbox environment

| Attribute | Requirement |
|-----------|-------------|
| **Isolation** | Separate credentials and data |
| **Parity** | Same RFC behavior as production |
| **Fixtures** | Seed kit for conformance tests |

## Supporting infrastructure

| Component | Requirement |
|-----------|-------------|
| **TLS termination** | Public endpoints |
| **Secrets manager** | API keys, webhook secrets |
| **Message queue** | Async scoring (SQS, RabbitMQ, Kafka, etc.) |
| **Object storage** | CSV uploads, report archives |
| **CI/CD** | Schema and OpenAPI contract tests |
| **SIEM** | Security event aggregation (Enterprise+) |

## Component interaction matrix

| From → To | Ingest | Identity | Graph | Scoring | Lookup | Signing | Policy |
|-----------|--------|----------|-------|---------|--------|---------|--------|
| Ingest | — | ✓ | ✓ | queue | — | — | ✓ |
| Lookup | — | ✓ | ✓ | — | — | ✓ | ✓ |
| Scoring | — | — | ✓ | — | — | — | — |
| Federation | — | ✓ | ✓ | — | ✓ | verify | ✓ |

## Minimum hardware (reference)

| Profile | vCPU | RAM | Storage |
|---------|------|-----|---------|
| Core pilot | 4 | 16 GB | 200 GB SSD |
| Enterprise | 16+ | 64 GB+ | 1 TB+ with replication |
| Government | Multi-AZ per operator policy | | |
| Edge node | 2 | 8 GB | 64 GB local |

## Related

- [Required architecture](./required-architecture)
- [Implementation examples](./implementation-examples)

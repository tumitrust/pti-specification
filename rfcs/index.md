---
sidebar_position: 1
title: PTI RFC Index
description: Index of Portable Trust Infrastructure Request for Comments documents.
---

# PTI RFC Index

Portable Trust Infrastructure (PTI) normative specifications are published as numbered RFCs. Each RFC is self-contained but cross-references related documents. RFCs use [RFC 2119](https://www.rfc-editor.org/rfc/rfc2119) normative keywords: **MUST**, **MUST NOT**, **SHOULD**, **SHOULD NOT**, **MAY**, and **RECOMMENDED**.

## RFC lifecycle

| Status | Meaning |
|--------|---------|
| **Draft** | Under community review; breaking changes permitted |
| **Proposed** | Stable enough for pilot implementations |
| **Final** | Normative; backward-compatible changes only via versioning RFC |
| **Deprecated** | Superseded; implementations SHOULD migrate |

All RFCs listed below are **Proposed** as of PTI specification v1.0.

## RFC catalogue

| RFC | Title | Scope | Status |
|-----|-------|-------|--------|
| [RFC-001](./rfc-001-architecture) | PTI Architecture | System layers, roles, data planes, and trust lifecycle | Proposed |
| [RFC-002](./rfc-002-trust-contexts) | Trust Contexts | Context identifiers, isolation, lens derivation | Proposed |
| [RFC-003](./rfc-003-trust-events) | Trust Events | Event schema, ingest channels, validation | Proposed |
| [RFC-004](./rfc-004-trust-lookup-api) | Trust Lookup API | Consumer-facing lookup operations and report tiers | Proposed |
| [RFC-005](./rfc-005-trust-graph) | Trust Graph | Subject graph model, edges, signal propagation | Proposed |
| [RFC-006](./rfc-006-trust-exchange) | Trust Exchange | Cross-implementation portability and federation | Proposed |
| [RFC-007](./rfc-007-governance) | Governance | Consent, retention, accountability, audit | Proposed |
| [RFC-008](./rfc-008-security) | Security | Authentication, encryption, threat model | Proposed |
| [RFC-009](./rfc-009-privacy) | Privacy | Data minimization, subject rights, purpose binding | Proposed |
| [RFC-010](./rfc-010-versioning) | Versioning | Schema evolution, compatibility, deprecation | Proposed |
| [RFC-011](./rfc-011-identity-resolution) | Identity Resolution | PTI-ID assignment, deduplication, external subjects | Proposed |
| [RFC-012](./rfc-012-trust-evidence) | Trust Evidence | Provenance chains, verification hooks, explainability | Proposed |

## Reading order

Implementers building a **minimal compatible system** SHOULD read RFCs in this order:

1. RFC-001 (Architecture) — establishes vocabulary and boundaries
2. RFC-002 (Trust Contexts) — scopes all downstream data
3. RFC-003 (Trust Events) — producer ingest contract
4. RFC-011 (Identity Resolution) — subject identity model
4. RFC-005 (Trust Graph) — internal representation
5. RFC-012 (Trust Evidence) — outcome provenance
6. RFC-004 (Trust Lookup API) — consumer contract
7. RFC-007, RFC-008, RFC-009 — governance, security, privacy obligations
8. RFC-006, RFC-010 — federation and evolution

## Relationship to other documentation

| Document set | Purpose |
|--------------|---------|
| [Conformance](/pti/conformance/) | Profiles, certification, test categories |
| [Build your PTI](/pti/build-your-pti/) | Independent implementer guide |
| [Specification v1.0](/pti/specification/v1.0/) | Operational compliance and explainability guides |

## Contributing

RFC changes require a revision PR with:

1. Motivation for the change
2. Backward-compatibility analysis per RFC-010
3. Updated conformance test references where applicable

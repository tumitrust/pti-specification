---
sidebar_position: 11
title: RFC-010 — Versioning
description: Normative versioning and compatibility rules for PTI.
---

# RFC-010: Versioning

| Field | Value |
|-------|-------|
| **RFC** | 010 |
| **Title** | Versioning |
| **Status** | Proposed |
| **Depends on** | RFC-001 |
| **Updates** | — |

## Abstract

This document defines versioning rules for PTI specifications, API endpoints, event catalogues, report schemas, and exchange packages. It establishes semantic versioning policy, deprecation timelines, and backward-compatibility requirements for conforming implementations.

## Motivation

Trust infrastructure evolves as contexts, event types, and regulatory requirements grow. Without normative versioning, independent implementations fragment and federation breaks silently.

## Background

PTI uses layered version identifiers: specification release (e.g., v1.0), API path version (`/v1/`), schema `package_version`, and derivation rule versions (`@v3`). This RFC harmonizes change management across layers.

## Terminology

| Term | Definition |
|------|------------|
| **Major change** | Breaking incompatible change requiring consumer action |
| **Minor change** | Backward-compatible additive change |
| **Patch change** | Clarification or non-functional fix |
| **Deprecation** | Announced removal with sunset date |
| **Sunset** | Date after which support ends |

## Specification

### 1. Semantic versioning

Published PTI specifications **MUST** use `MAJOR.MINOR.PATCH`:

- **MAJOR** — incompatible API or schema changes
- **MINOR** — additive fields, new event types, new optional endpoints
- **PATCH** — documentation fixes, non-normative clarifications

### 2. API versioning

- Public URLs **MUST** include major version prefix (`/v1/`).
- Minor API additions **MUST** remain within same major path.
- Breaking changes **MUST** increment major version and maintain prior major for deprecation window.

### 3. Schema evolution rules

| Change type | Allowed in minor? | Client requirement |
|-------------|-------------------|-------------------|
| New optional JSON field | Yes | **MUST** ignore unknown fields |
| New required JSON field | No (major) | **MUST** update client |
| Relaxed validation | Yes | — |
| Tightened validation | No (major unless flagged) | **MUST** update producer |
| Removed field | No (major) | **MUST** migrate |

Clients **MUST** follow Postel's law: be conservative in sending, liberal in receiving.

### 4. Event catalogue versioning

- Event catalogue **MUST** expose `catalogue_version`.
- New `event_type` additions are minor changes.
- Payload schema breaking changes **MUST** use new `event_type` suffix or major catalogue bump.

### 5. Report and evidence versions

- `trust_intelligence` reports **MUST** include `schema_version` (e.g., `trust_intelligence.v1`).
- Evidence manifests **MUST** include `package_version` per RFC-006 and RFC-012.
- Verification **MUST** reject unknown major schema versions.

### 6. Deprecation policy

| Asset | Minimum deprecation notice |
|-------|---------------------------|
| API major version | 12 months |
| Event type | 6 months with migration guide |
| Report schema field | 6 months (minor) or parallel major |
| Exchange profile | 12 months |

Deprecated features **MUST** return `Deprecation` header with `sunset` date.

### 7. Federation compatibility

Operators **MUST** publish supported `package_version` and `schema_version` ranges in federation metadata.
Receiving operators **MUST** negotiate down or reject incompatible packages explicitly.

### 8. Conformance version pinning

Certification **MUST** declare PTI specification version tested.
Implementations **MAY** support multiple major API versions concurrently during deprecation window.

## Security Considerations

- Removing security fields **MUST** be treated as major change with accelerated migration guidance.
- Version downgrade attacks **MUST** be blocked; APIs **MUST NOT** serve weaker schema to newer clients without explicit opt-in.

## Privacy Considerations

- New fields collecting PII **MUST** trigger privacy review and minor version note in changelog.
- Erasure workflows **MUST** remain functional across minor upgrades.

## Examples

### Deprecation header

```
Deprecation: true
Sunset: Sat, 01 Jul 2028 00:00:00 GMT
Link: </v2/reports/generate>; rel="successor-version"
```

### Event type migration

`loan_repayment` v1 payload adds optional `channel`. v2 requires `channel` → introduced as new catalogue major with dual acceptance period.

## Implementation Notes

- Automate compatibility tests in CI against golden fixtures per version.
- Maintain public changelog with RFC references.
- Feature flags **SHOULD NOT** bypass version contract in production federation paths.

## Future Work

- Machine-readable compatibility matrix between operators
- Automated client SDK generation from versioned OpenAPI
- Long-term support (LTS) designation for government deployments

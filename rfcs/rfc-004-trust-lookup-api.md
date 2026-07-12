---
sidebar_position: 5
title: RFC-004 — Trust Lookup API
description: Normative consumer-facing trust lookup API for PTI.
---

# RFC-004: Trust Lookup API

| Field | Value |
|-------|-------|
| **RFC** | 004 |
| **Title** | Trust Lookup API |
| **Status** | Proposed |
| **Depends on** | RFC-001, RFC-002, RFC-012 |
| **Updates** | — |

## Abstract

This document specifies the Trust Lookup API through which trust consumers evaluate subjects at decision time. It defines search, report generation, tiers, entitlements, error handling, and explainability exports. The API is read-oriented and purpose-bound; it is not a bulk data export protocol.

## Motivation

Institutions require a stable contract for requesting context-scoped trust intelligence with predictable fields, latency, and audit metadata. Ad hoc reporting endpoints prevent certification and cross-vendor substitution.

## Background

A **trust lookup** evaluates a subject's portable record for a specific decision — hiring, underwriting, tenant screening — distinct from periodic credit file pulls. PTI lookup tiers scale depth and compute cost while preserving provenance requirements.

## Terminology

| Term | Definition |
|------|------------|
| **Lookup** | Single consumer operation producing a trust report |
| **Report tier** | `basic`, `detailed`, `predictive`, `screening_dossier` |
| **Subject** | Individual or organization identified by PTI-ID or search keys |
| **Purpose code** | Declared consumer use case for policy enforcement |
| **Report ID** | Immutable identifier for a generated report instance |

## Specification

### 1. API surface

Conforming implementations **MUST** expose:

| Operation | Method | Purpose |
|-----------|--------|---------|
| `subjects.search` | POST | Resolve candidate subjects |
| `reports.generate` | POST | Create context-scoped trust report |
| `reports.get` | GET | Retrieve prior report by `report_id` |
| `reports.verify` | GET | Public verification hook for evidence |

Implementations **MAY** expose `contexts.list` and `tiers.describe` discovery endpoints.

### 2. Authentication

- Consumers **MUST** authenticate with tenant-scoped credentials.
- Credentials **MUST** embed entitled `context_id` list and allowed tiers.
- Requests **MUST** be rejected if requested context or tier exceeds entitlement.

### 3. Subject search

Search requests **MUST** include at least one strong identifier (e.g., government ID hash, verified phone, email) or a known `pti_id`.

- Results **MUST** return ranked candidates with confidence scores.
- Implementations **MUST NOT** return subjects without minimum confidence threshold configured per jurisdiction.
- External subjects (not yet in graph) **MAY** return `status: external_route` per RFC-011.

### 4. Report generation

`reports.generate` **MUST** accept:

```json
{
  "subject": { "pti_id": "pti_k9m2" },
  "contexts": ["lending", "risk_compliance"],
  "tier": "detailed",
  "purpose_code": "underwriting_unsecured_loan",
  "consent_ref": "cst_4481"
}
```

Rules:

- `contexts` **MUST** be non-empty subset of entitled contexts.
- `purpose_code` **MUST** be from implementation registry.
- `consent_ref` **MUST** be present when jurisdiction or policy requires subject consent.
- Response **MUST** include `report_id`, `generated_at`, `trust_evidence` block (RFC-012).

### 5. Report tiers

| Tier | **MUST** include | **MAY** include |
|------|------------------|-----------------|
| `basic` | Context scores, summary band | Top three drivers |
| `detailed` | Scores, drivers, timeline summary | Verification hooks |
| `predictive` | Detailed fields + forward indicators | Model version metadata |
| `screening_dossier` | Compliance blocks, screening summary | External list provenance |

Higher tiers **MUST NOT** omit required fields from lower tiers.

### 6. Synchronous semantics

- `reports.generate` **MUST** complete within implementation SLA or return `202` with `status_url`.
- Partial reports **MUST NOT** be returned; fail closed on missing evidence.

### 7. Errors

| Code | Meaning |
|------|---------|
| `context_not_entitled` | Consumer lacks context |
| `consent_required` | Missing or invalid `consent_ref` |
| `subject_not_found` | No match above threshold |
| `tier_not_allowed` | Tier exceeds contract |
| `rate_limited` | Quota exceeded |

### 8. Verification

- `reports.verify` **MUST** allow third-party auditors to confirm report authenticity without consumer credentials.
- Verification **MUST** return signed manifest matching `trust_evidence`.

## Security Considerations

- Search endpoints are abuse-sensitive; rate limiting and bot detection **MUST** apply.
- Reports **MUST** be encrypted in transit; at-rest encryption **MUST** use tenant-isolated keys.
- PII in responses **MUST** match data minimization policy for tier and purpose.

## Privacy Considerations

- Purpose binding **MUST** log declared `purpose_code` in audit trail.
- Subjects **MUST** be able to discover lookup history where jurisdiction requires access rights.
- Screening dossier tier **MUST** document lawful basis for sensitive processing.

## Examples

### Generate detailed lending report

**Request:**

```json
POST /v1/reports/generate
{
  "subject": { "pti_id": "pti_k9m2" },
  "contexts": ["lending"],
  "tier": "detailed",
  "purpose_code": "mfi_renewal"
}
```

**Response:**

```json
{
  "report_id": "rpt_88ac",
  "generated_at": "2026-07-01T09:00:00Z",
  "contexts": ["lending"],
  "tier": "detailed",
  "scores": { "lending": { "band": "B+", "value": 712 } },
  "drivers": [
    { "label": "on_time_repayments_12m", "weight": 0.42, "event_refs": ["evt_7f3a"] }
  ],
  "trust_evidence": { "manifest_id": "evm_22bd", "signature": "…" }
}
```

## Implementation Notes

- Cache report manifests immutably; never mutate `report_id` contents.
- Separate search and generate rate limits.
- Publish OpenAPI with exemplar payloads per tier.

## Future Work

- Standard purpose code registry across industries
- Bulk lookup batch API for government profile (with strict governance)
- Webhook delivery of async report completion

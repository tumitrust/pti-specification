---
sidebar_position: 13
title: RFC-012 — Trust Evidence
description: Normative trust evidence and provenance for PTI.
---

# RFC-012: Trust Evidence

| Field | Value |
|-------|-------|
| **RFC** | 012 |
| **Title** | Trust Evidence |
| **Status** | Proposed |
| **Depends on** | RFC-004, RFC-005, RFC-006 |
| **Updates** | — |

## Abstract

This document defines trust evidence: the signed, auditable provenance bundle attached to lookup outcomes. Evidence links scores and drivers to events, producers, derivation rules, and verification hooks without exposing unauthorized cross-context data.

## Motivation

Explainability requires more than narrative text. Regulators and subjects need cryptographically verifiable chains showing how an outcome was produced. Trust evidence standardizes that export.

## Background

Evidence manifests summarize graph traversals (RFC-005) into consumer-safe structures. They power verification endpoints, PDF QR audits, and federated exchange packages (RFC-006).

## Terminology

| Term | Definition |
|------|------------|
| **Trust evidence** | Provenance bundle for a report |
| **Manifest** | Canonical JSON structure describing evidence |
| **manifest_id** | Unique evidence identifier |
| **Driver** | Weighted factor explaining outcome movement |
| **Verification hook** | Public URL or token confirming report authenticity |
| **Derivation rule** | Versioned scoring logic reference |

## Specification

### 1. Evidence attachment

Every `reports.generate` response at `basic` tier and above **MUST** include `trust_evidence` object.

### 2. Manifest structure

```json
{
  "manifest_id": "evm_22bd",
  "schema_version": "trust_evidence.v1",
  "report_id": "rpt_88ac",
  "pti_id": "pti_k9m2",
  "generated_at": "2026-07-01T09:00:00Z",
  "contexts": ["lending"],
  "outcomes": [
    {
      "context_id": "lending",
      "outcome_id": "out_lending_20260701",
      "band": "B+",
      "value": 712,
      "derived": false
    }
  ],
  "drivers": [
    {
      "driver_id": "drv_01",
      "label": "on_time_repayments_12m",
      "weight": 0.42,
      "direction": "positive",
      "event_refs": ["evt_7f3a"],
      "derivation_rule": "lending_repayment_velocity@v3"
    }
  ],
  "sources": [
    {
      "event_id": "evt_7f3a",
      "event_type": "loan_repayment",
      "context_id": "lending",
      "occurred_at": "2026-06-15T14:22:00Z",
      "producer_ref": "producer_redacted_3"
    }
  ],
  "signature": {
    "alg": "EdDSA",
    "kid": "op_main_2026",
    "value": "…"
  }
}
```

### 3. Required fields by tier

| Tier | Manifest requirements |
|------|----------------------|
| `basic` | `outcomes`, top drivers (≤3), signature |
| `detailed` | Full drivers, `sources` summary, verification hook |
| `predictive` | `model_version`, feature family metadata |
| `screening_dossier` | `screening_summary` with list provenance |

### 4. Signing

- Manifest **MUST** be canonicalized (JCS RFC 8785) before signing.
- Signature **MUST** cover entire manifest excluding `signature` object.
- Verification endpoint **MUST** recompute hash and validate signature.

### 5. Redaction rules

- Producer legal names **MAY** be redacted to `producer_ref` tokens.
- Payload fields not entitled to consumer **MUST NOT** appear in `sources`.
- Cross-context events **MUST NOT** appear unless context entitled.

### 6. Verification hook

- `reports.verify` **MUST** accept `report_id` or QR-encoded token.
- Response **MUST** include `verification_status`: `valid`, `revoked`, `expired`, `invalid`.
- Expired **MUST** apply when `generated_at` exceeds operator-configured max age.

### 7. Lens evidence

Lens outcomes **MUST** include:

```json
{
  "context_id": "digital_platform",
  "derived": true,
  "feeds_from": ["merchant", "employment", "partnership"]
}
```

### 8. Revocation linkage

Revoked manifests **MUST** appear on package revocation list (RFC-006).
Verification **MUST** return `revoked` with reason code.

## Security Considerations

- Signing keys **MUST** meet RFC-008 requirements.
- Manifests **MUST NOT** leak HMAC secrets or internal admin URLs.
- QR tokens **SHOULD** be single-purpose and rate-limited.

## Privacy Considerations

- Evidence **MUST** minimize subject attributes in `sources`.
- Subjects accessing own evidence **MAY** see less-redacted producer refs than consumers.

## Examples

### Verification response

```json
{
  "verification_status": "valid",
  "manifest_id": "evm_22bd",
  "report_id": "rpt_88ac",
  "signed_at": "2026-07-01T09:00:00Z",
  "signer": "op_main_2026"
}
```

### Screening provenance snippet

```json
{
  "screening_summary": {
    "sanctions": { "status": "clear", "provider": "opensanctions", "checked_at": "2026-07-01T08:59:00Z" },
    "pep": { "status": "match_review", "case_id": "scr_991" }
  }
}
```

## Implementation Notes

- Generate manifest from graph `explain_outcome` traversal; do not hand-build.
- Store manifest immutably keyed by `manifest_id`.
- Embed verification URL in PDF reports as QR for committee packs.

## Future Work

- Evidence visualization standard for institution UIs
- Zero-knowledge proofs for selective disclosure of drivers
- Linked data (JSON-LD) serialization optional profile

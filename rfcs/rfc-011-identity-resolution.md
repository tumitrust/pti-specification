---
sidebar_position: 12
title: RFC-011 — Identity Resolution
description: Normative identity resolution and PTI-ID assignment for PTI.
---

# RFC-011: Identity Resolution

| Field | Value |
|-------|-------|
| **RFC** | 011 |
| **Title** | Identity Resolution |
| **Status** | Proposed |
| **Depends on** | RFC-001, RFC-003, RFC-005 |
| **Updates** | — |

## Abstract

This document specifies identity resolution in PTI: assignment of portable **PTI-ID** identifiers, mapping producer `entity_id` values, deduplication, confidence scoring, external subject handling, and merge/split governance.

## Motivation

Trust portability requires a stable subject key across producers without exposing national identifiers in every API call. Normative resolution prevents duplicate subjects, erroneous merges, and ambiguous lookup matches.

## Background

Identity resolution sits between ingest and graph attachment. It consumes attributes from events and search requests, evaluates match confidence against existing subjects, and either links or creates `pti_id` records. It is distinct from authentication of API callers.

## Terminology

| Term | Definition |
|------|------------|
| **PTI-ID** | Canonical subject identifier (`pti_{alphanumeric}`) |
| **Entity reference** | (`tenant_id`, `entity_id`) pair |
| **Strong identifier** | High-uniqueness attribute (e.g., government ID hash, verified phone) |
| **Weak identifier** | Low-uniqueness attribute (e.g., name + DOB) |
| **Confidence score** | 0.0–1.0 match probability |
| **External subject** | Candidate not yet materialized in trust graph |

## Specification

### 1. PTI-ID format

- PTI-ID **MUST** match `^pti_[a-z0-9]{8,32}$`.
- PTI-ID **MUST** be assigned only by identity resolution service.
- PTI-ID **MUST NOT** embed raw national ID or sequential guessable counters without entropy.

### 2. Entity reference binding

- On first event for (`tenant_id`, `entity_id`), resolution **MUST** assign or link `pti_id`.
- Subsequent events with same pair **MUST** resolve to same `pti_id` without re-evaluation unless split invoked.
- Bindings **MUST** be stored as graph edges (`resolves_to`) with `confidence: 1.0` for direct producer mappings.

### 3. Match confidence

Search and ingest resolution **MUST** compute confidence from configured rules:

| Signal combination | Minimum confidence to auto-link |
|--------------------|---------------------------------|
| Strong identifier exact match | 0.95 |
| Strong + weak corroboration | 0.90 |
| Weak identifiers only | **MUST NOT** auto-link above 0.85 |

Thresholds **MAY** be tightened per jurisdiction via policy.

### 4. Auto-merge rules

- Auto-merge **MUST NOT** occur when confidence < configured threshold.
- Auto-merge **MUST** create audit record with inputs and rule version.
- Conflicting strong identifiers **MUST** block merge and route to manual review queue.

### 5. Manual review

Implementations **SHOULD** provide operator workflow for:

- `merge` — combine two `pti_id` records with governance approval
- `split` — separate erroneously merged subjects
- `reject` — mark candidate as non-match

All actions **MUST** be audit-logged with actor and justification.

### 6. External subjects

When consumer search finds no graph match but strong identifiers present:

- API **MAY** return `status: external_route` (RFC-004).
- External screening **MUST** not fabricate trust scores; only entitled screening dimensions **MAY** run.
- If subject later ingests events, `pti_id` **MUST** be created and linked without reusing external temporary tokens in reports.

### 7. Pre-provision and lifecycle

- Subjects **MAY** be pre-provisioned via verified onboarding flows before first producer event.
- Deletion **MUST** mark `pti_id` `erased` and invalidate entity bindings.
- Re-registration after erasure **MUST** receive new `pti_id` unless law requires linkage (documented exception).

### 8. Federation identity

Foreign PTI-IDs **MUST** use `pti_{local}@{operator_id}` format (RFC-006).
Local resolution **MUST NOT** merge foreign and local IDs without explicit cross-operator identity agreement.

## Security Considerations

- Strong identifiers **MUST** be stored hashed with salt at rest.
- Resolution admin APIs **MUST** be privileged and monitored for bulk enumeration.
- Match algorithms **MUST** resist timing side channels on citizen ID lookup.

## Privacy Considerations

- Resolution **MUST** use minimum identifiers necessary.
- Subjects **MUST** access merge/split history where jurisdiction grants right to explanation.
- Biometric matching **MUST NOT** be default resolution path.

## Examples

### Ingest resolution

```json
{
  "tenant_id": "mfi_101",
  "entity_id": "borrower_8821",
  "pti_id": "pti_k9m2x7q1",
  "resolution": "entity_binding",
  "confidence": 1.0
}
```

### Ambiguous search

```json
{
  "candidates": [
    { "pti_id": "pti_a1", "confidence": 0.72 },
    { "pti_id": "pti_b2", "confidence": 0.68 }
  ],
  "status": "ambiguous",
  "message": "Refine search with strong identifier"
}
```

## Implementation Notes

- Separate hot path (entity binding) from cold path (fuzzy search).
- Version matching rules; replay disputes against historical rule version.
- Index hashed strong identifiers with constant-time comparison.

## Future Work

- Standard attribute dictionary for cross-border resolution
- Verifiable credential binding to PTI-ID
- Privacy-preserving matching protocols (PSI) for federation

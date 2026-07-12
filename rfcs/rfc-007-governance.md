---
sidebar_position: 8
title: RFC-007 — Governance
description: Normative governance framework for PTI implementations.
---

# RFC-007: Governance

| Field | Value |
|-------|-------|
| **RFC** | 007 |
| **Title** | Governance |
| **Status** | Proposed |
| **Depends on** | RFC-001, RFC-004, RFC-009 |
| **Updates** | — |

## Abstract

This document defines governance requirements for PTI implementations: consent management, role accountability, retention and deletion, audit trails, adverse-action support, and operator obligations. Governance is part of conformance, not optional documentation.

## Motivation

Trust infrastructure operates in a regulated-risk-adjacent space. Without normative governance, implementations become indistinguishable from opaque credit bureaus, exposing subjects and institutions to legal and reputational harm.

## Background

Governance spans legal terms, technical controls, and operational workflows. PTI encodes enforceable technical rules that mirror privacy and consumer-protection principles across jurisdictions.

## Terminology

| Term | Definition |
|------|------------|
| **Lawful basis** | Documented legal ground for processing personal data |
| **Consent record** | Immutable reference proving subject authorization |
| **Data subject** | Identified or identifiable natural person |
| **Retention schedule** | Policy mapping data classes to maximum retention |
| **Audit trail** | Tamper-evident log of security and privacy events |
| **Adverse action** | Negative decision influenced by trust lookup outcome |

## Specification

### 1. Governance principles

Implementations **MUST** enforce:

1. **Consent first** — sharing beyond platform defaults **MUST** require valid consent where applicable law demands it.
2. **Data minimization** — lookups **MUST** return only fields entitled for `purpose_code` and tier.
3. **Explainability** — outcomes **MUST** include structured drivers (RFC-012).
4. **Accountability** — roles **MUST** map to auditable actions.
5. **Rights by default** — access, correction, deletion, export **MUST** be technically supported.

### 2. Consent management

- Consent records **MUST** include: `consent_id`, `pti_id`, `scope`, `granted_at`, `expires_at`, `withdrawn_at`.
- Lookup requests **MUST** validate `consent_ref` when policy requires.
- Withdrawn consent **MUST** block new lookups in scoped contexts within 15 minutes maximum propagation SLA.

### 3. Role obligations

| Role | **MUST** | **MUST NOT** |
|------|----------|--------------|
| **Subject** | Access own lookup history where required | — |
| **Producer** | Emit only contracted events | Send prohibited data categories (RFC-003) |
| **Consumer** | Document lawful basis; use explainability for adverse action | Republish raw trust data outside contract |
| **Operator** | Operate audit and DSAR workflows | Use lookup data for undisclosed analytics |

### 4. Retention and deletion

- Retention schedules **MUST** be published per data class.
- Account deletion **MUST** initiate erasure workflow with documented grace period.
- Legal holds **MAY** retain minimal audit hashes; raw PII **MUST** be deleted when hold lifts.
- Producer event deletion requests **MUST** propagate to graph and outcomes within documented SLA.

### 5. Audit trails

Audit logs **MUST** capture:

| Event | Required fields |
|-------|-----------------|
| Lookup | `consumer_id`, `pti_id`, `contexts`, `purpose_code`, `report_id`, `timestamp` |
| Consent change | `consent_id`, `action`, `actor`, `timestamp` |
| Admin access | `actor`, `resource`, `action`, `timestamp` |

Logs **MUST** be append-only and retained per compliance schedule.

### 6. Adverse action support

Reports at `detailed` tier and above **MUST** include:

- Ordered drivers with weights
- Source event references
- Verification hook for independent confirmation

Consumers **SHOULD** map drivers to human-readable notices per local law.

### 7. Incident response

Operators **MUST** document breach notification procedures and support consumer inquiry within jurisdiction timelines.

## Security Considerations

- Audit logs **MUST** be protected from tampering; WORM storage **RECOMMENDED** for government profile.
- Admin actions on consent **MUST** require multi-factor authentication.

## Privacy Considerations

- DSAR export **MUST** include lookup history, consent records, and producer events attributable to subject.
- Governance dashboards **MUST NOT** expose one subject's data to unrelated tenant admins.

## Examples

### Consent-gated lookup failure

```json
{
  "error": "consent_required",
  "message": "purpose_code hiring_permanent requires active consent",
  "consent_scope": ["employment"]
}
```

### Deletion propagation

Subject deletion sets `pti_id` status `pending_erasure`; after grace, graph nodes anonymize and packages revoke per RFC-006.

## Implementation Notes

- Implement consent as first-class service, not lookup API afterthought.
- Automate retention jobs with dry-run reporting before purge.
- Provide institution-facing compliance export for auditors.

## Future Work

- Jurisdiction-specific policy packs (GDPR, POPIA, PDPA)
- Standard adverse-action notice templates
- Independent governance audit certification

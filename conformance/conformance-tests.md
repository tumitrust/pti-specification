---
sidebar_position: 4
title: Conformance Tests
description: PTI conformance test categories and checklists.
---

# PTI Conformance Tests

This document defines test categories, pass criteria, and checklists for PTI self-assessment and accredited certification. Tests reference normative RFC requirements.

## Test methodology

| Type | Tooling | Who runs |
|------|---------|----------|
| **Automated contract** | OpenAPI-driven HTTP tests, JSON Schema validation | CI + lab |
| **Automated negative** | Fuzzing, entitlement bypass attempts | Lab |
| **Manual procedural** | DSAR walkthrough, key rotation drill | Lab + operator |
| **Soak / performance** | Load on lookup path | Enterprise+ optional |

Pass criterion: **100% mandatory tests pass** for declared profile. Optional capabilities tested only if claimed.

## Test environment

- Sandbox tenant with seed data kit `pti-test-fixtures-v1`
- At least three subjects: `pti_test_alpha`, `pti_test_beta`, `pti_test_gamma`
- Two producers with disjoint context entitlements
- One consumer with partial context entitlement (for negative tests)

## Category A — Architecture (RFC-001)

| ID | Test | Pass criteria |
|----|------|---------------|
| A-01 | Role separation | Producer credential cannot call consumer-only endpoints |
| A-02 | Lifecycle ingest→lookup | Event accepted → outcome updates → report references event |
| A-03 | Fail-closed policy | Disabled context ingest returns `context_not_enabled` |
| A-04 | Sandbox isolation | Sandbox report IDs invalid in production verify |

**Checklist:**

- [ ] A-01 pass
- [ ] A-02 pass
- [ ] A-03 pass
- [ ] A-04 pass

## Category B — Trust Contexts (RFC-002)

| ID | Test | Pass criteria |
|----|------|---------------|
| B-01 | Primary catalogue | All claimed primaries validate `context_id` format |
| B-02 | Lens rejection on ingest | Event with lens `context_id` rejected at ingest |
| B-03 | Isolation | Lending event does not alter merchant score |
| B-04 | Consumer entitlement | Lookup with non-entitled context returns `context_not_entitled` |
| B-05 | Lens derivation flag | Lens outcomes show `derived: true` in evidence |

**Checklist:**

- [ ] B-01 through B-05 pass

## Category C — Trust Events (RFC-003)

| ID | Test | Pass criteria |
|----|------|---------------|
| C-01 | Required fields | Missing `event_type` → 400 validation error |
| C-02 | Idempotency | Duplicate `idempotency_key` returns same `event_id`, single graph edge |
| C-03 | occurred_at bounds | Event 11 years old rejected |
| C-04 | Prohibited payload | PAN in payload rejected |
| C-05 | Channel attribution | Webhook-delivered event has `ingest_channel: webhook` |
| C-06 | Compensating event | Reversal event adjusts outcome with audit trail |

**Checklist:**

- [ ] C-01 through C-06 pass
- [ ] CSV batch reports row errors without silent drop (Enterprise+)

## Category D — Trust Lookup API (RFC-004)

| ID | Test | Pass criteria |
|----|------|---------------|
| D-01 | Search strong ID | Returns `pti_test_alpha` at confidence ≥ 0.95 |
| D-02 | Ambiguous weak ID | Returns `ambiguous` without auto-select |
| D-03 | Generate detailed | Response includes scores, drivers, `trust_evidence` |
| D-04 | Consent gate | Missing `consent_ref` returns `consent_required` when policy on |
| D-05 | Tier enforcement | `screening_dossier` without entitlement fails |
| D-06 | Report immutability | Second `reports.get` returns identical manifest hash |
| D-07 | Async handling | If 202 returned, `status_url` resolves to terminal state |

**Checklist:**

- [ ] D-01 through D-07 pass

## Category E — Trust Graph (RFC-005)

| ID | Test | Pass criteria |
|----|------|---------------|
| E-01 | Orphan rejection | Internal ingest cannot create event without subject link |
| E-02 | Explain outcome | `explain_outcome` lists source events matching drivers |
| E-03 | Signal versioning | Reprocessing creates new `signal_id`, preserves old |
| E-04 | Point-in-time | Historical `report_id` reconstructs same scores |

**Checklist:**

- [ ] E-01 through E-04 pass

## Category F — Trust Exchange (RFC-006, Enterprise+ optional / Government mandatory)

| ID | Test | Pass criteria |
|----|------|---------------|
| F-01 | Package signature | Valid package verifies; tampered bytes fail |
| F-02 | Foreign PTI-ID format | `pti_x@op_test` parsed correctly |
| F-03 | Revocation | Revoked `manifest_id` returns `verification_status: revoked` |
| F-04 | Fail closed | Invalid signature excludes package from consumer fields |
| F-05 | PRL polling | Simulated revocation visible within SLA |

**Checklist:**

- [ ] F-01 through F-05 pass (if exchange claimed)

## Category G — Governance (RFC-007)

| ID | Test | Pass criteria |
|----|------|---------------|
| G-01 | Consent withdraw | Lookup blocked within 15 minutes after withdraw |
| G-02 | Audit append-only | Tamper attempt detected |
| G-03 | Deletion propagation | Erased subject's events removed from new reports |
| G-04 | Adverse action fields | Detailed report drivers ordered with weights |

**Checklist:**

- [ ] G-01 through G-04 pass
- [ ] DSAR export within published SLA (Enterprise+)

## Category H — Security (RFC-008)

| ID | Test | Pass criteria |
|----|------|---------------|
| H-01 | TLS version | TLS 1.1 rejected |
| H-02 | Scoped credential | Consumer key cannot ingest events |
| H-03 | Rate limit | Search enumeration triggers throttle |
| H-04 | Webhook replay | Old timestamp signature rejected |
| H-05 | Key rotation | Dual `kid` acceptance during overlap window |

**Checklist:**

- [ ] H-01 through H-05 pass
- [ ] Pen test report < 12 months old (Enterprise+)

## Category I — Privacy (RFC-009)

| ID | Test | Pass criteria |
|----|------|---------------|
| I-01 | Purpose logging | Audit contains `purpose_code` per lookup |
| I-02 | Minimization basic | Basic tier has no raw producer payload |
| I-03 | Cross-border block | Disallowed region transfer fails closed |
| I-04 | DSAR completeness | Export includes lookups and consents |

**Checklist:**

- [ ] I-01 through I-04 pass

## Category J — Versioning (RFC-010)

| ID | Test | Pass criteria |
|----|------|---------------|
| J-01 | Unknown field tolerance | Client ignores new optional field without error |
| J-02 | Deprecation header | Sunset endpoint returns `Deprecation` header |
| J-03 | Schema version | Report includes `schema_version` |

**Checklist:**

- [ ] J-01 through J-03 pass

## Category K — Identity Resolution (RFC-011)

| ID | Test | Pass criteria |
|----|------|---------------|
| K-01 | Entity binding stability | Same `entity_id` always maps same `pti_id` |
| K-02 | Confidence threshold | 0.70 confidence does not auto-link |
| K-03 | Conflict block | Conflicting strong IDs route to review, no merge |
| K-04 | External route | Unknown subject returns `external_route` when entitled |

**Checklist:**

- [ ] K-01 through K-04 pass

## Category L — Trust Evidence (RFC-012)

| ID | Test | Pass criteria |
|----|------|---------------|
| L-01 | Manifest signature | Valid EdDSA/RSA verify on canonical JSON |
| L-02 | Verification endpoint | QR token resolves `valid` |
| L-03 | Redaction | Non-entitled context events absent from `sources` |
| L-04 | Lens metadata | Derived contexts include `feeds_from` |

**Checklist:**

- [ ] L-01 through L-04 pass

## Category M — Edge profile (Edge only)

| ID | Test | Pass criteria |
|----|------|---------------|
| M-01 | Offline queue | Events ingested offline sync without duplication |
| M-02 | Conflict resolution | Documented conflict yields deterministic outcome |
| M-03 | Hub verify | Edge `report_id` verifiable via hub |
| M-04 | Deferred erasure | Erasure propagates within 72h of sync |

**Checklist:**

- [ ] M-01 through M-04 pass

## Test reporting template

```markdown
## PTI Conformance Test Report

- Operator: 
- Profile: 
- Spec version: 
- Date: 

| Category | Pass | Fail | N/A |
|----------|------|------|-----|
| A Architecture | | | |
| … | | | |

Mandatory failures: 
Remediation plan: 
Signed by: 
```

## Continuous conformance

Implementers **SHOULD** run categories A, C, D, L on every release candidate in CI. Full suite **SHOULD** run quarterly.

## Related

- [Certification guide](./certification-guide)
- [Conformance profiles](./profiles)

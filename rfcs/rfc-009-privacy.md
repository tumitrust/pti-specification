---
sidebar_position: 10
title: RFC-009 — Privacy
description: Normative privacy requirements for PTI implementations.
---

# RFC-009: Privacy

| Field | Value |
|-------|-------|
| **RFC** | 009 |
| **Title** | Privacy |
| **Status** | Proposed |
| **Depends on** | RFC-001, RFC-004, RFC-007 |
| **Updates** | — |

## Abstract

This document specifies privacy requirements for PTI: data minimization, purpose limitation, subject rights, pseudonymization, cross-border controls, and privacy-by-design checkpoints across ingest, processing, and lookup.

## Motivation

Portable trust multiplies data flows across producers and consumers. Without normative privacy rules, portability becomes surveillance. PTI privacy requirements make explainability and minimization technically enforceable.

## Background

PTI draws on GDPR principles (lawfulness, fairness, transparency, purpose limitation, data minimization, accuracy, storage limitation, integrity, confidentiality, accountability) without mandating a single jurisdiction. Implementations **MUST** map controls to local law via policy configuration.

## Terminology

| Term | Definition |
|------|------------|
| **PII** | Personally identifiable information |
| **Pseudonymization** | Processing that replaces identifiers with reversible tokens under separate controls |
| **Purpose binding** | Restriction that data use matches declared `purpose_code` |
| **DSAR** | Data subject access request |
| **DPIA** | Data protection impact assessment |

## Specification

### 1. Privacy principles

1. **Purpose limitation** — data collected for context C **MUST NOT** be used for unrelated purposes without new lawful basis.
2. **Minimization** — ingest and lookup **MUST** exclude fields not needed for stated function.
3. **Transparency** — subjects **MUST** receive understandable notices about trust processing.
4. **Storage limitation** — retention **MUST** follow published schedules (RFC-007).
5. **Integrity and confidentiality** — security controls from RFC-008 **MUST** apply.

### 2. Lawful basis registry

Operators **MUST** maintain registry mapping:

| Processing activity | Typical lawful bases |
|---------------------|----------------------|
| Producer ingest | Contract, legitimate interest with balancing test |
| Consumer lookup | Consent, contract, legal obligation |
| Identity resolution | Consent or legal obligation per jurisdiction |
| Fraud prevention (restricted) | Legitimate interest with safeguards |

Consumers **MUST** declare `purpose_code` linked to registry entry.

### 3. Data minimization at lookup

| Tier | Minimization rule |
|------|-------------------|
| `basic` | Scores and bands only; no raw identifiers beyond subject ref |
| `detailed` | Drivers with redacted producer names if contract requires |
| `predictive` | Model features **MUST NOT** include prohibited categories |
| `screening_dossier` | Only entitled screening dimensions; fail closed on unavailable |

### 4. Subject rights

Implementations **MUST** support:

| Right | Technical capability |
|-------|---------------------|
| **Access** | Export trust data, lookups, consents |
| **Rectification** | Correct inaccurate profile attributes |
| **Erasure** | Delete account and propagate erasure |
| **Portability** | Machine-readable export |
| **Restriction** | Pause processing pending dispute |
| **Objection** | Stop processing based on legitimate interest where applicable |

SLA for DSAR **SHOULD** be ≤ 30 calendar days unless law requires shorter.

### 5. Pseudonymization and identifiers

- `pti_id` **SHOULD** be opaque and unlinkable to national ID without separate controlled lookup.
- Producer `entity_id` **MUST NOT** appear in consumer reports unless entitled.
- Logs **SHOULD** use hashed identifiers where full ID unnecessary.

### 6. Cross-border transfer

- Transfers **MUST** be blocked unless destination passes policy check (adequacy, SCCs, or local equivalent).
- Federated exchange (RFC-006) **MUST** record `origin_region` on packages.

### 7. Automated decision-making

When predictive tier influences decisions with legal effect:

- Meaningful information about logic **MUST** be available (`model_version`, driver families).
- Human review pathway **MUST** be documentable by consumer.
- Solely automated rejection **MUST NOT** occur without required safeguards under applicable law.

### 8. Privacy by design checkpoints

| Stage | Checkpoint |
|-------|------------|
| Schema design | Field necessity review |
| Ingest | Prohibited category scanner |
| Lookup | Purpose + consent validator |
| Export | Entitlement filter |
| Deletion | Graph propagation verifier |

## Security Considerations

Privacy depends on RFC-008 encryption and access control. Privacy officer **SHOULD** review role permissions quarterly.

## Privacy Considerations

Children's data and special-category data **MUST** be blocked at ingest unless explicit lawful pathway configured with DPIA on file.

## Examples

### Minimized detailed driver

```json
{
  "label": "on_time_repayments_12m",
  "weight": 0.42,
  "event_refs": ["evt_7f3a"],
  "producer_ref": "producer_redacted_3"
}
```

### DSAR export structure

```json
{
  "pti_id": "pti_k9m2",
  "profile": { },
  "consents": [ ],
  "lookups": [ ],
  "producer_events_summary": [ ]
}
```

## Implementation Notes

- Centralize purpose binding in policy engine shared by lookup and analytics paths.
- Run periodic data inventory scans against retention schedules.
- Provide privacy dashboard for subjects with lookup history.

## Future Work

- Standard DPIA template for PTI deployments
- Differential privacy for aggregate trust statistics
- Uniform consent UX specification across mobile and web

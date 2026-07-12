---
sidebar_position: 5
title: Privacy Requirements
description: Privacy implementation requirements for PTI builders.
---

# Privacy Requirements

Implementation checklist derived from [RFC-009](/pti/rfcs/rfc-009-privacy) and [RFC-007](/pti/rfcs/rfc-007-governance). Privacy controls are conformance requirements, not legal advice — map to local law with counsel.

## Purpose limitation and minimization

| ID | Requirement | Core | Enterprise | Government |
|----|-------------|------|------------|------------|
| PRV-01 | `purpose_code` required on lookup requests | ✓ | ✓ | ✓ |
| PRV-02 | Purpose registry maintained and published | ✓ | ✓ | ✓ |
| PRV-03 | Tier-based field minimization enforced | ✓ | ✓ | ✓ |
| PRV-04 | Producer `entity_id` hidden from consumers unless entitled | ✓ | ✓ | ✓ |
| PRV-05 | Prohibited categories blocked at ingest | ✓ | ✓ | ✓ |
| PRV-06 | Lens outputs respect feed entitlement redaction | Optional | ✓ | ✓ |

### Tier minimization implementation

```python
# Pseudocode: lookup response builder
def build_response(tier, entitled_fields, raw_graph):
    if tier == "basic":
        return {"scores": raw_graph.scores_only()}
    if tier == "detailed":
        return {"scores": ..., "drivers": redact_producers(raw_graph.drivers)}
    # screening_dossier: only entitled screening dimensions
```

## Consent and lawful basis

| ID | Requirement | Core | Enterprise | Government |
|----|-------------|------|------------|------------|
| PRV-10 | Consent records with grant/withdraw timestamps | ✓ | ✓ | ✓ |
| PRV-11 | Lookup blocked when consent invalid | ✓ | ✓ | ✓ |
| PRV-12 | Withdraw propagates ≤ 15 minutes | ✓ | ✓ | ✓ |
| PRV-13 | Lawful basis registry linked to purpose codes | Optional | ✓ | ✓ |
| PRV-14 | Subject consent history exportable | ✓ | ✓ | ✓ |

## Data subject rights (DSAR)

| ID | Requirement | Core | Enterprise | Government |
|----|-------------|------|------------|------------|
| PRV-20 | Access — subject can view trust data | ✓ | ✓ | ✓ |
| PRV-21 | Export — machine-readable package | Manual OK | Automated | Automated |
| PRV-22 | Erasure — deletion propagates to graph | ✓ | ✓ | ✓ |
| PRV-23 | Rectification — profile correction path | ✓ | ✓ | ✓ |
| PRV-24 | Published DSAR SLA | ✓ | ✓ | ✓ |
| PRV-25 | Lookup history visible to subject | Optional | ✓ | ✓ |

### Erasure propagation checklist

- [ ] Mark `pti_id` status `erased`
- [ ] Remove/anonymize event nodes
- [ ] Invalidate outcome cache entries
- [ ] Revoke evidence manifests (PRL)
- [ ] Retain audit hash only if legal hold applies

## Retention

| ID | Requirement |
|----|-------------|
| PRV-30 | Published retention schedule per data class |
| PRV-31 | Automated purge jobs with audit log |
| PRV-32 | Legal hold mechanism overriding purge |
| PRV-33 | Partner event retention aligned with contract |

Suggested defaults (adjust per jurisdiction):

| Data class | Retention |
|------------|-----------|
| Raw events | 7 years or contract minimum |
| Audit logs | 7 years |
| Report manifests | 3 years |
| Consent records | Life of processing + 3 years |

## Cross-border controls

| ID | Requirement | Enterprise | Government |
|----|-------------|------------|------------|
| PRV-40 | Destination region policy configuration | ✓ | ✓ |
| PRV-41 | Block transfer to non-approved regions | ✓ | ✓ |
| PRV-42 | `origin_region` on federated packages | ✓ | ✓ |

## Automated decision-making

| ID | Requirement |
|----|-------------|
| PRV-50 | Predictive tier includes `model_version` and driver families |
| PRV-51 | Document human review pathway for consumers |
| PRV-52 | No sole automated legal-effect decision without required safeguards |

## Transparency

| ID | Requirement |
|----|-------------|
| PRV-60 | Public privacy notice describes trust processing |
| PRV-61 | Producer contracts reference context scope |
| PRV-62 | Consumer contracts reference lookup purpose codes |

## Privacy by design checkpoints

Integrate reviews at:

1. **Event schema approval** — field necessity sign-off
2. **New report tier** — minimization review
3. **Federation onboarding** — cross-border DPIA
4. **Model deployment** — bias and feature prohibition review

## DPIA triggers

Conduct Data Protection Impact Assessment when:

- Adding biometric or special-category processing
- Enabling cross-border federation
- Launching predictive tier at scale
- New screening provider integration

## Documentation deliverables

1. **Record of processing activities** — ingest, lookup, resolution, federation
2. **Retention schedule** — technical enforcement mapping
3. **DSAR runbook** — automated or manual steps
4. **Consent UX specification** — scopes and withdrawal
5. **Cross-border policy** — region allowlist

## Related

- [RFC-009 Privacy](/pti/rfcs/rfc-009-privacy)
- [RFC-007 Governance](/pti/rfcs/rfc-007-governance)
- [Conformance tests — Category I](/pti/conformance/conformance-tests)

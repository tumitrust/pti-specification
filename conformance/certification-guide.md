---
sidebar_position: 3
title: Certification Guide
description: How to certify a PTI implementation.
---

# PTI Certification Guide

This guide describes how independent implementers obtain PTI conformance certification for a declared profile. Certification confirms RFC compliance through structured testing and documentation review.

## Certification goals

1. Give institutions confidence in operator interoperability
2. Reduce duplicate security and privacy questionnaires
3. Enable accredited federation (Government profile)
4. Create public registry of certified implementations

## Prerequisites

Before applying:

| Requirement | Detail |
|-------------|--------|
| **Specification version** | Target PTI v1.0 or later stable release |
| **Profile declaration** | Core, Enterprise, Government, or Edge per [profiles](./profiles) |
| **Self-assessment** | Complete [conformance-tests](./conformance-tests) checklists |
| **OpenAPI** | Published producer and consumer API descriptions |
| **Conformance statement** | JSON document per profiles schema |
| **Security documentation** | RFC-008 control mapping |
| **Privacy documentation** | RFC-009 DSAR and retention procedures |

## Certification bodies

Certification **SHOULD** be performed by accredited testing laboratories independent of the implementer's development team. Government profile **MUST** use nationally recognized accreditation where applicable.

Implementers **MAY** begin with **self-certification** for pilot deployments; self-certification **MUST NOT** be marketed as accredited certification.

## Application package

Submit:

1. **Implementation overview** — architecture diagram aligned with RFC-001
2. **Profile declaration** — capabilities and known limitations
3. **Test evidence** — automated test reports + manual test sign-off
4. **Policy documents** — governance, privacy, incident response
5. **Sample artifacts** — redacted event, report, evidence manifest, verification response
6. **Versioning statement** — supported API versions and deprecation policy per RFC-010
7. **Contact** — security and privacy officer details

## Certification phases

### Phase 1 — Document review (2–4 weeks)

Reviewers verify:

- RFC mapping completeness for declared profile
- Conformance statement accuracy
- Security and privacy policy alignment
- No undisclosed prohibited data processing

**Outcome:** Document acceptance or remediation list.

### Phase 2 — Laboratory testing (4–8 weeks)

Testers execute [conformance-tests](./conformance-tests):

- Automated API contract tests against implementer sandbox
- Negative tests (fail-closed consent, context isolation, invalid signatures)
- Identity resolution threshold tests
- Evidence verification round-trip
- Profile-specific tests (federation, edge sync, DSAR)

**Outcome:** Test report with pass/fail per category.

### Phase 3 — Operational review (2–4 weeks, Enterprise+)

Reviewers assess:

- Production change management
- Key rotation procedures
- Incident response tabletop exercise
- Audit log integrity sampling

Government profile adds governance audit and HSM validation.

### Phase 4 — Certification decision

| Result | Certificate contents |
|--------|---------------------|
| **Pass** | Profile, spec version, certificate ID, expiry (12 months), public registry entry |
| **Conditional pass** | Minor gaps with 90-day remediation plan |
| **Fail** | Remediation required before re-application |

## Sandbox requirements for testing

Certification sandbox **MUST**:

- Mirror production RFC behavior
- Use separate credentials and data
- Support deterministic test fixtures (seed subjects and events)
- Allow tester IP allowlisting
- Disable external paid third-party calls or provide stubs

## Ongoing compliance

| Obligation | Frequency |
|------------|-----------|
| **Surveillance testing** | Annual for Enterprise; semi-annual for Government |
| **Major version re-cert** | Within 6 months of new API major |
| **Incident disclosure** | Material breaches affecting evidence integrity within 72 hours to accreditation body |
| **Conformance statement update** | Within 30 days of capability change |

## Certificate revocation

Certificates **MAY** be revoked for:

- Material RFC violation discovered post-certification
- Failure to remediate conditional pass items
- Evidence forgery or audit log tampering
- Expiry without renewal

Revoked operators **MUST** be removed from federation registries within 24 hours.

## Cost and timeline estimates

| Profile | Typical duration | Notes |
|---------|----------------|-------|
| Core | 8–12 weeks | Document + lab |
| Enterprise | 12–16 weeks | Adds operational review |
| Government | 20–28 weeks | Governance + HSM + federation |
| Edge | 10–14 weeks | Includes sync conflict suite |

Estimates assume remediation cycles; first-time applicants should budget one additional iteration.

## Public registry fields

Certified implementations **SHOULD** publish:

```json
{
  "certificate_id": "pti-cert-2026-0042",
  "operator_id": "op_example",
  "profile": "enterprise",
  "pti_spec_version": "1.0",
  "issued_at": "2026-07-01",
  "expires_at": "2027-07-01",
  "contexts": ["lending", "merchant", "employment"],
  "exchange_capable": true,
  "registry_url": "https://example.trust/.well-known/pti-federation"
}
```

## Appeals and disputes

Implementers **MAY** appeal failed test categories with reproducible counter-evidence within 30 days. Federation disputes between operators follow RFC-006 bilateral escalation before accreditation body ruling.

## Related

- [Conformance profiles](./profiles)
- [Conformance tests](./conformance-tests)
- [Build your PTI](/pti/build-your-pti/)

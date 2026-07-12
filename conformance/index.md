---
sidebar_position: 1
title: PTI Conformance
description: What it means to be PTI-compatible.
---

# PTI Conformance

**Portable Trust Infrastructure (PTI) compatibility** means an implementation satisfies the normative requirements of the PTI RFC series for a declared [conformance profile](./profiles). Compatibility is a technical claim testable through documented checklists — not marketing language.

## What PTI-compatible means

A **PTI-compatible implementation**:

1. **Implements required RFCs** for its profile — architecture (RFC-001), contexts (RFC-002), events (RFC-003), lookups (RFC-004), evidence (RFC-012), and profile-specific additions.
2. **Uses RFC 2119 semantics** — **MUST** requirements are enforced, not documented-only.
3. **Passes conformance tests** — automated and manual tests in [conformance-tests](./conformance-tests).
4. **Declares a profile** — Core, Enterprise, Government, or Edge (see [profiles](./profiles)).
5. **Publishes a conformance statement** — version, profile, supported contexts, known limitations.

A PTI-compatible implementation **is not required** to:

- Operate a specific cloud or vendor stack
- Support all twenty trust contexts on day one
- Provide consumer-facing mobile applications
- Implement proprietary scoring formulas (derivation rules must be versioned and evidenced)

## What PTI-compatible does not mean

| Claim | Reality |
|-------|---------|
| "PTI-inspired" | Non-normative; not certifiable |
| "Partial PTI" | Must declare which profile capabilities are omitted |
| "PTI API wrapper" over non-PTI backend | Fails if evidence, context isolation, or governance are missing |
| "Credit bureau compatible" | PTI is trust intelligence infrastructure, not tradeline file exchange |

## Conformance dimensions

| Dimension | Primary RFCs | Test focus |
|-----------|--------------|------------|
| **Architecture** | RFC-001 | Role separation, lifecycle |
| **Contexts** | RFC-002 | Isolation, catalogue, enablement |
| **Events** | RFC-003 | Schema, idempotency, channels |
| **Lookups** | RFC-004 | Tiers, entitlements, errors |
| **Graph** | RFC-005 | Provenance traversal |
| **Exchange** | RFC-006 | Signing, federation (Enterprise+) |
| **Governance** | RFC-007 | Consent, audit, deletion |
| **Security** | RFC-008 | AuthN/Z, crypto |
| **Privacy** | RFC-009 | Minimization, DSAR |
| **Versioning** | RFC-010 | Deprecation, compatibility |
| **Identity** | RFC-011 | PTI-ID, confidence thresholds |
| **Evidence** | RFC-012 | Manifests, verification |

## Self-assessment vs certification

| Level | Who performs | Output |
|-------|--------------|--------|
| **Self-assessment** | Implementer | Internal checklist completion |
| **Accredited certification** | Independent lab | Conformance certificate with profile and version |

Self-assessment is sufficient for development and pilot. Production federation and government accreditation require [certification](./certification-guide).

## Vendor neutrality

Conformance evaluates **behavior against RFCs**, not brand affiliation. Any organization may implement PTI and certify without platform membership.

## Related documents

- [Conformance profiles](./profiles)
- [Certification guide](./certification-guide)
- [Conformance tests](./conformance-tests)
- [Build your PTI](/pti/build-your-pti/)

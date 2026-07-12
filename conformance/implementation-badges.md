---
title: PTI Implementation Badges
description: Certification badges for PTI-compatible implementations — eligibility, testing, validation, renewal.
sidebar_position: 5
---

# PTI Implementation Badges

PTI conformance badges communicate verified compatibility to enterprises, regulators, and integrators. Badges are **claims about tested behavior**, not marketing endorsements.

## Badge catalogue

| Badge | Profile | Meaning |
|-------|---------|---------|
| **PTI Compatible** | Self-declared Core minimum | Implementation publishes a conformance statement and passes public self-test checklist |
| **PTI Core Certified** | Core | Independent validation of mandatory Core capabilities |
| **PTI Enterprise Certified** | Enterprise | Core + enterprise mandatory capabilities (DSAR, webhooks, predictive tier, pen test) |
| **PTI Government Certified** | Government | Enterprise + sovereign federation, HSM, expanded context catalogue |
| **PTI Extended Certified** | Extended | Government + marketplace, multi-region federation, or advanced ecosystem APIs |

## Eligibility

An implementation **MUST**:

1. Declare `pti_spec_version` and target profile in a public conformance statement
2. Publish OpenAPI or equivalent abstract API documentation
3. Operate a sandbox environment for certification testing
4. Maintain a security contact for disclosure coordination

An implementation **MUST NOT** display a certified badge without completing the certification process for that profile.

## Testing

| Stage | Activity |
|-------|----------|
| **Self-test** | Run [conformance test checklist](/pti/conformance/conformance-tests) against sandbox |
| **Submission** | Provide test logs, API endpoints, and conformance statement JSON |
| **Independent validation** | Conformance Program runs automated + manual spot checks |
| **Remediation** | Failures block certification until resolved or documented as limitations |

## Validation

Certified implementations receive:

- Badge artwork and usage guidelines per [trademark policy](/pti/governance/trademark-branding)
- Public listing in the conformance registry (when operational)
- Certificate identifier: `PTI-CERT-{profile}-{year}-{id}`

## Renewal

| Requirement | Interval |
|-------------|----------|
| Re-run conformance tests for declared profile | Annual |
| Security disclosure contact current | Continuous |
| Spec version compatibility declared | Within 90 days of new Stable spec release |
| Penetration test evidence (Enterprise+) | Annual |

Failure to renew **MUST** result in badge revocation and registry delisting.

## Version compatibility

Badges are tied to a **spec minor version** (e.g. PTI v1.0). Implementations **MAY** support multiple spec versions during transition periods documented in [version management](/pti/governance/version-management).

Display format:

```
PTI Enterprise Certified · Spec v1.0 · Certificate PT-CERT-enterprise-2026-0042
```

## Related

- [Conformance profiles](/pti/conformance/profiles)
- [Certification guide](/pti/conformance/certification-guide)
- [Certification process (governance)](/pti/governance/certification-process)
- [Conformance program](/pti/governance/conformance-program)

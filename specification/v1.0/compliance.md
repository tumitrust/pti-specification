---
sidebar_position: 9
title: Risk & Compliance Guide
description: Screening, compliance intelligence, explainability, and institution workflows in PTI v1.0.
---

# Risk & Compliance Guide

This guide describes how PTI v1.0 supports **risk and compliance lenses** — screening dimensions, compliance intelligence payloads, and explainability for regulated workflows.

## Personas & lookup profiles

| Persona | Profile | Contexts |
|---------|---------|----------|
| Bank / digital lender | `lending` | lending, risk_compliance |
| Insurer | `risk_assessment` | insurance, risk_compliance |
| Fintech / payments | `merchant_onboarding` | merchant, digital_platform |
| Government / civic | `risk_assessment` | civic, employment |

## Screening dimensions

When a risk-assessment workflow is selected, implementations **MAY** integrate:

- **Sanctions** lists (e.g. OpenSanctions-backed where configured)
- **PEP** — potential match → manual review
- **Identity** — document and registry checks
- **Business registry** — where contract enables
- **UNSCR** packs when contracted

Statuses: `clear` · `match_review` · `not_run` · `unavailable` (fail closed)

## External subjects (not yet in registry)

When a subject is not found in the trust registry but strong identifiers are supplied, implementations **MAY** route to **external screening** providers. Screening **SHOULD** be metered separately from standard lookup tiers where billing applies.

## Compliance intelligence JSON

Additive `compliance_intelligence` block:

- `confidence`, `risk_indicators`, `coverage_gaps`
- `screening_summary` with provenance
- `trust_timeline`, `compliance_lens`

## Explainability & AML

Institutions use **drivers** and **screening_summary** for committee packs — not black-box clearance. Adverse media may return `not_run` until a dedicated provider is enabled.

## Typical institution workflow

1. Select risk-assessment profile and entitled contexts
2. Resolve subject (`pti_id` or identity hints)
3. Generate trust lookup with screening dimensions
4. Review explainability and screening summary
5. Export verifiable report artifact for audit (implementation-specific format)

## Reference implementation

[TumiTrust](/pti/reference-implementation/) documents production screening integration and institution hub workflows. See [Risk & compliance intelligence](/tumitrust/platform/risk-compliance-intelligence) and [Trust platform API — external screening](/tumitrust/developer-guides/trust-platform-api#external-screening-api).

## Governance cross-links

- [Trust governance](/pti/specification/v1.0/governance)
- [Explainability guide](/pti/specification/v1.0/explainability)
- [Security architecture](/pti/specification/v1.0/security)

## Related

- [RFC-007 Governance](/pti/rfcs/rfc-007-governance)
- [Trust Intelligence Reports](/tumitrust/platform/trust-intelligence-reports)

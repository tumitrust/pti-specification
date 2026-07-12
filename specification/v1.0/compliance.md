---
sidebar_position: 9
title: Risk & Compliance Guide
description: Screening, OpenSanctions, PEP, explainability, institution workflows, AML posture.
---

import PdfDownloadBar from '@site/src/components/PdfDownloadBar';

# Risk & Compliance Guide

<div class="tt-resource-hero">
  <p class="tt-glossary-kicker">Risk & compliance</p>
  <p class="tt-glossary-lead">Expanded public guide for insurers, banks, fintechs, and compliance officers.</p>
</div>

<PdfDownloadBar title="Risk & Compliance Guide" />

:::info Technical integration
For API fields and hub workflows, see [Risk & compliance intelligence](/guides/integration/risk-compliance-intelligence).
:::

## Personas & lookup profiles

| Persona | Profile | Contexts |
|---------|---------|----------|
| Bank / digital lender | `lending` | lending, risk_compliance |
| Insurer | `risk_assessment` | insurance, risk_compliance |
| Fintech / payments | `merchant_onboarding` | merchant, digital_platform |
| Government / civic | `risk_assessment` | civic, employment |

## Screening dimensions

When risk assessment workflow is selected:

- **Sanctions** (e.g. OpenSanctions-backed where configured)
- **PEP** — potential match → manual review
- **Identity** — document and registry checks
- **Business registry** — where contract enables
- **UNSCR** packs (`unscr_1267`, `unscr_1373`, `unscr_1988`) when contracted

Statuses: `clear` · `match_review` · `not_run` · `unavailable` (fail closed)

## External subjects (not yet on TumiTrust)

Directory miss + strong identifiers → **automatic external screening** route. Bills screening credits separately from standard lookup tiers.

## Compliance intelligence JSON

Additive `compliance_intelligence` block:

- `confidence`, `risk_indicators`, `coverage_gaps`
- `screening_summary` with provenance
- `trust_timeline`, `compliance_lens`

## Explainability & AML

Institutions use **drivers** and **screening_summary** for committee packs — not black-box clearance. Adverse media may return `not_run` until dedicated provider enabled.

## Governance cross-links

- [Trust governance](/pti/specification/v1.0/governance)
- [Explainability guide](/pti/specification/v1.0/explainability)
- [Security architecture](/pti/specification/v1.0/security)

## Institution workflow (hosted)

1. Compliance Center spotlight → Risk assessment lookup
2. Lookup Studio → subject search → generate
3. Insights Studio → screening grid + explain panel
4. PDF verify QR for auditors

## Related

- [Trust platform API — external screening](/tumitrust/developer-guides/trust-platform-api#external-screening-api)
- [Trust Intelligence Reports](/guides/integration/trust-intelligence-reports)

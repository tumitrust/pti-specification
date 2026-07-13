---
sidebar_position: 6
title: PTI Whitepaper
description: Reference paper — trust fragmentation, PTI philosophy, signals, metering, partner ecosystem, future.
displayed_sidebar: ptiSidebar
---

import PdfDownloadBar from '@site/src/components/PdfDownloadBar';

# Portable Trust Infrastructure Whitepaper

<div class="tt-resource-hero">
  <p class="tt-glossary-kicker">Reference paper</p>
  <p class="tt-glossary-lead">The canonical long-form reference for academics, regulators, and enterprise strategy teams.</p>
</div>

<PdfDownloadBar title="PTI Whitepaper" />

## Abstract

Portable Trust Infrastructure (PTI) addresses **trust fragmentation** in emerging markets by separating trust **generation** (partner-ingested signals) from trust **consumption** (institutional lookups). TumiTrust implements PTI as a **trust platform** — not a credit bureau — with **20+ documented trust contexts** (10 primaries + 10 lenses).

## 1. Introduction

Formal credit files capture a narrow slice of economic life. Community trade, digital lending, rental reliability, and mobile-money behavior produce **credible signals** that rarely travel across institutions. PTI makes those signals **portable**, **context-scoped**, and **explainable**.

## 2. Problem: trust fragmentation

- Signals locked in silos (one lender, one landlord, one wallet)
- Under-documented ≠ untrustworthy
- Institutions lack programmable infrastructure for multi-context decisions

## 3. PTI philosophy

**Ubuntu applied to infrastructure:** "I am because we are" — community validation complements verified activity.

PTI is:

- **Portable** — Trust profile travels with the subject
- **Programmable** — APIs and webhooks for institutions
- **Context-scoped** — 10 primary + 10 lens contexts
- **Explainable** — drivers and provenance, not black boxes

## 4. Portable identities

- **PTI-ID** for partner-provisioned entities
- Directory resolution for native members
- Claim flows for screened external subjects who later enroll in a PTI registry

## 5. Trust signals

```mermaid
flowchart LR
  Partner[Partner] --> Events[Events]
  Events --> Signals[Trust signals]
  Signals --> Context[Context scores]
  Context --> Lookup[Trust lookup]
```

Signal types: partner events, endorsements, badges, verification documents.

## 6. Trust intelligence

Lookup tiers: **Basic · Detailed · Predictive · Screening dossier** — all **trust intelligence** exports with explainability contracts per the specification.

Implementations **MAY** expose operator consoles for search, screening review, and verifiable report export; product naming is deployment-specific.

## 7. Metering (informative)

Implementations that bill producers **MAY** meter processing as **Trust Compute Units (TCU)** per context per billing period ([RFC-001](/pti/rfcs/rfc-001-architecture)). TCU is not normative for conformance.

**Reference implementation:** [TumiTrust partner billing](/tumitrust/billing/partner-billing).

## 8. Partner ecosystem

Producers enable contexts, configure connectors (webhook, API, CSV), and emit catalogued events. Consumers request `contexts[]` on reports.

## 9. Context tiers & expansion

**Primaries:** life-area contexts such as `lending`, `rental`, and `employment`.

**Lenses:** cross-cutting views such as `risk_compliance`, `digital_platform`, and `civic` — enabled per contract on the same map.

## 10. Governance & future

- [Trust governance framework](/pti/specification/v1.0/governance)
- [Explainability guide](/pti/specification/v1.0/explainability)
- Sovereign / residency options: [Government & sovereign](/tumitrust/platform/government-sovereign)

## Citation

TumiTrust Ltd. *Portable Trust Infrastructure Whitepaper.* Available at https://tumitrust.com/docs/pti/bibliography/whitepaper

## Related

- [Why PTI](/pti/why-pti/)
- [Architecture guide](/tumitrust/product-overview/architecture)
- [Company portfolio](/tumitrust/product-overview/company-portfolio)

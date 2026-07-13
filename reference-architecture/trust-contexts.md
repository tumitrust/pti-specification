---
sidebar_position: 8
title: Trust Context Catalogue
description: All 20+ documented trust contexts — purpose, signals, institutions, APIs.
---

import PdfDownloadBar from '@site/src/components/PdfDownloadBar';

# Trust Context Catalogue

<div class="tt-resource-hero">
  <p class="tt-glossary-kicker">20+ documented contexts</p>
  <p class="tt-glossary-lead"><strong>10 primary</strong> life areas + <strong>10 lens</strong> cross-cutting views. Enable the contexts your contract needs.</p>
</div>

<PdfDownloadBar title="Trust Context Catalogue" />

## Context tiers

| Scope | Contexts |
|-------|----------|
| **Primaries** | Life-area contexts — lending, rental, employment, insurance, and more |
| **Lenses** | Cross-cutting views — risk & compliance, digital platform, community, and more |

## Primary contexts

| ID | Label | Native users | Primary need | Example institutions |
|----|-------|--------------|--------------|----------------------|
| `lending` | Lending & Borrowing | Borrowers, MFIs | Repayment trust | Digital lenders, MFIs |
| `rental` | Rental & Housing | Tenants, landlords | Lease reliability | Property managers |
| `employment` | Employment & Work | Job seekers, HR | Hire verification | Employers, HR tech |
| `insurance` | Insurance & Risk | Policyholders | Risk assessment | Insurers, brokers |
| `merchant` | Merchant & Business | Sellers, marketplaces | Onboarding | Commerce platforms |
| `partnership` | Business Partnerships | JV partners | Counterparty trust | B2B networks |
| `supplier` | Supply Chain | Vendors | Supply chain | Procurement |
| `informal_sector` | Informal Economy | Stokvel, cooperatives | Group trust | Community finance |
| `agricultural` | Agriculture | Farmers | Seasonal reliability | Agri-finance |
| `remittance` | Money Transfer | Senders, diaspora | Transfer trust | Remittance operators |

## Lens contexts (derived)

| ID | Label | Feeds from |
|----|-------|------------|
| `education` | Education & Skills | employment, partnership, lending |
| `health` | Health & Care | insurance, employment, partnership |
| `family` | Family & Household | rental, remittance, partnership |
| `civic` | Civic & Public Life | partnership, informal_sector, employment |
| `digital_platform` | Digital & Platform Work | merchant, employment, partnership |
| `mobility` | Mobility & Logistics | employment, supplier, merchant |
| `utilities` | Utilities & Subscriptions | rental, lending, merchant |
| `creative` | Creative & Gigs | merchant, informal_sector, employment |
| `faith_mutual_aid` | Faith & Mutual Aid | informal_sector, partnership, remittance |
| `risk_compliance` | Risk & Compliance | insurance, employment, civic, merchant |

## Per-context integration

| Concern | Detail |
|---------|--------|
| **API field** | `contexts[]` on report `generate` |
| **Producer rule** | Enable only contexts you emit |
| **Consumer rule** | Request allowed contexts per contract |
| **Reports** | Context-scoped slices in trust intelligence JSON |

Full producer configuration is implementation-specific. See the [trust context catalogue](/pti/reference-architecture/trust-contexts) for normative context IDs.

## Reference implementation

[TumiTrust trust contexts for producers](/tumitrust/platform/partner-trust-contexts#all-20-trust-contexts) · [Partner integration guide](/tumitrust/developer-guides/partner-integration) · [Financial services solutions](/tumitrust/solutions/financial-services)

## Related

- [Why PTI](/pti/why-pti/)
- [Build Your Own PTI](/pti/build-your-pti/)

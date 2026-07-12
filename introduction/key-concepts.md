---
title: Key concepts
sidebar_position: 2
description: Portable Trust Infrastructure — 20+ trust contexts, trust lookups (not credit bureaus), and governance obligations.
---

# Key concepts

This page summarizes the ideas behind [Portable Trust Infrastructure](./portable-trust-infrastructure). For definitions of every term, see the [Glossary](../pti/glossary/).

## Portable Trust Infrastructure (PTI)

TumiTrust is a **trust platform**, not a traditional credit bureau. Portable Trust Infrastructure lets trust signals travel with a person or organization across **life-area contexts** instead of being locked in one institution’s silo. Partners ingest verified events; institutions run **trust lookups** at decision time.

We are **not** selling a black-box “credit score.” We provide **context-scoped trust intelligence** — explainable signals, provenance, and verification — as infrastructure for lenders, verifiers, and producers.

## Trust contexts on one map

TumiTrust documents **20+ trust contexts**: **10 primary** life areas and **10 lens** cross-cutting views. Institutions and partners enable the contexts their contract needs — lending, rental, employment, insurance, civic, and more share the same PTI map and APIs.

| Layer | Count | Examples |
|-------|-------|----------|
| **Primary** | 10 | `lending`, `rental`, `employment`, `insurance`, `merchant`, … |
| **Lens** | 10 | `education`, `health`, `civic`, `risk_compliance`, … |

Full ID table: [Documented trust contexts](../tumitrust/platform/partner-trust-contexts#all-20-trust-contexts).

:::note Context map
TumiTrust documents **20+ trust contexts** (10 primary life areas and 10 lens views). Older materials that cite “10 contexts” describe primary life areas only — not the full documented map.
:::

## Trust CV and lookup

A **Trust CV** is the portable record of credibility built from verified events and community signals. A **trust lookup** (trust report) is an institution’s request to evaluate that record for a specific decision — not a bank credit file.

Lookup tiers (**Basic · Detailed · Predictive · Screening dossier**) differ in depth; all are **trust intelligence** exports, not consumer credit bureau reports.

## AI as infrastructure

Machine learning supports **signal fusion, identity resolution, and context-scoped scoring** — institutions receive structured outcomes with drivers, provenance, and verification hooks suitable for audit and adverse-action workflows.

Deeper governance — consent flows, retention, portability — is covered in [Trust governance](/pti/specification/v1.0/governance) and institution hub **Compliance** settings.

## Governance and data rights

Terms of Service and Privacy Policy describe obligations around **trust lookups, risk assessment, identity resolution, and analytics** — a regulated-risk-adjacent category. Borrower **consent**, **data minimization**, **retention limits**, **explainability**, and **data-subject rights** (access, correction, deletion, export) are platform requirements, not optional extras.

## Individuals, institutions, and partners

| Audience | Role |
|----------|------|
| **Individuals** | Build and share trust through the mobile app and web profile |
| **Institutions & verifiers** | Run trust lookups, verify reports, integrate via the trust platform API |
| **Partners (trust producers)** | Ingest signals, configure connectors, and bill via TCU |

## API and sandbox access

Integration documentation covers API endpoints, authentication, and integration patterns. Create **sandbox** and **live** API keys under **API & trust signals** in your workspace. Enable live keys by completing **account verification** in Settings.

## Next steps

- [Portable Trust Infrastructure overview](./portable-trust-infrastructure)
- [Trust contexts for producers](../tumitrust/platform/partner-trust-contexts)
- [Trust platform API](../tumitrust/developer-guides/trust-platform-api)
- [Get started](../tumitrust/product-overview/get-started)
- [FAQ](../tumitrust/faq)

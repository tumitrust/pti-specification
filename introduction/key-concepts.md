---
title: Key concepts
sidebar_position: 2
description: Core PTI concepts — trust contexts, lookups, signals, and governance — vendor-neutral overview.
---

# Key concepts

This page summarizes ideas behind Portable Trust Infrastructure (PTI). For definitions, see the [Glossary](/pti/glossary/). For the category thesis, see [Why PTI exists](/pti/why-pti/).

## Portable Trust Infrastructure (PTI)

PTI is an **infrastructure layer** for trust — not a credit bureau, social network, or star-rating app. It lets trust signals travel with a subject across **life-area contexts** instead of being locked in one institution’s silo.

| Role | Responsibility |
|------|----------------|
| **Trust producer** | Ingests verified activity as **trust events** |
| **Trust exchange** | Normalizes, governs, and routes signals |
| **Trust consumer** | Runs **trust lookups** at decision time |
| **Trust registry** | Maintains `pti_id`, contexts, and policies |

## Trust contexts

PTI **isolates** signals and scores by **trust context** (lending, rental, employment, insurance, merchant, civic, and cross-cutting lenses such as risk compliance). The [trust context catalogue](/pti/reference-architecture/trust-contexts) documents 20+ registered contexts in the reference architecture.

Implementations **MUST NOT** collapse unrelated life areas into a single opaque score without an explicit, published aggregation policy.

## Trust profile and lookup

A **trust profile** is the aggregate view of a subject’s signals, context scores, relationships, and assertions. A **trust lookup** is a consumer’s decision-time request to evaluate that profile for a bounded purpose — not a traditional credit bureau file.

Lookup tiers (Basic, Detailed, Predictive, Screening) differ in depth; all are **trust intelligence** exports with explainability requirements per [RFC-004](/pti/rfcs/rfc-004-trust-lookup-api).

## Signals, evidence, and explainability

**Trust events** become normalized **trust signals** with provenance. **Trust evidence** substantiates assertions and outcomes. Consumers receive **drivers**, confidence bands, and **coverage gaps** — not black-box clearance. See [Explainability guide](/pti/specification/v1.0/explainability).

Machine learning **MAY** support signal fusion and identity resolution; outcomes **MUST** remain structured and auditable.

## Governance

Consent, retention, purpose binding, subject rights, and audit obligations are specification requirements — see [RFC-007 Governance](/pti/rfcs/rfc-007-governance) and [Specification governance](/pti/specification/v1.0/governance).

## Participants

| Participant | Role in PTI |
|-------------|-------------|
| **Subjects** | Portable `pti_id`; consent and rights under governance |
| **Trust producers** | Emit verifiable events and assertions |
| **Trust consumers** | Request context-scoped lookups |
| **Implementers** | Operate exchange, registry, graph, and intelligence services |

## Reference implementation

[TumiTrust](/pti/reference-implementation/) demonstrates these concepts in production. Product-specific integration guides live under [TumiTrust documentation](/tumitrust/product-overview/).

## Next steps

- [Introduction to PTI](/pti/introduction/)
- [Reference architecture](/pti/reference-architecture/)
- [PTI Specification v1.0](/pti/specification/v1.0/)
- [Build Your Own PTI](/pti/build-your-pti/)

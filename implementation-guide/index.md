---
title: Implementation Guide
description: PTI implementation guide — integration paths, conformance checklist, and reading order.
sidebar_position: 1
---

# Implementation Guide

Practical guidance for implementing Portable Trust Infrastructure (PTI) v1.0 as a producer, consumer, or registry operator. This guide is **informative**; normative requirements appear in the [Specification v1.0](/pti/specification/v1.0/).

## Who should read this

| Audience | Start here |
|----------|------------|
| **Producer engineers** | [Best Practices](./best-practices) → [Trust Producers](/pti/reference-architecture/trust-producers) |
| **Consumer engineers** | [Best Practices](./best-practices) → [Trust Consumers](/pti/reference-architecture/trust-consumers) |
| **Platform operators** | [Specification Architecture](/pti/specification/v1.0/architecture) → [Security](/pti/specification/v1.0/security) |
| **Migration teams** | [Migration Guide](./migration-guide) → [Anti-Patterns](./anti-patterns) |

## Implementation phases

```mermaid
flowchart LR
  P1[Discover] --> P2[Design]
  P2 --> P3[Build sandbox]
  P3 --> P4[Conformance test]
  P4 --> P5[Production pilot]
  P5 --> P6[Scale]
```

### Phase 1 — Discover

- Read [Key concepts](../pti/introduction/key-concepts) and [Glossary](/pti/glossary/)
- Map your data sources to trust contexts and event types
- Identify lawful basis and consent requirements with legal stakeholders

### Phase 2 — Design

- Choose conformance profile (`pti-producer/v1`, `pti-consumer/v1`, or both)
- Document entity-to-`pti_id` resolution strategy
- Define idempotency key templates per business action

### Phase 3 — Build sandbox

- Obtain sandbox credentials separate from production
- Implement Registry resolve + Exchange ingest (producers) or Lookup search/generate (consumers)
- Wire correlation ID logging end-to-end

### Phase 4 — Conformance test

- Golden-path ingest and lookup
- Idempotent replay
- Authorization denial paths (`PTI-403x`)
- Version negotiation against `/capabilities`

### Phase 5 — Production pilot

- Limited context enablement
- Monitor validation error and match confidence distributions
- Establish dispute and correction runbooks

### Phase 6 — Scale

- Enable additional contexts and tiers per contract
- Federation or multi-region routing if required
- Operational dashboards for signal freshness and lookup latency

## Conformance checklist

| Item | Producer | Consumer |
|------|----------|----------|
| RFC 2119 error handling | ✓ | ✓ |
| `schema_version` on all payloads | ✓ | ✓ |
| `X-PTI-Version` pinned | ✓ | ✓ |
| Idempotency keys | ✓ | — |
| Purpose code on lookups | — | ✓ |
| Consent flow when required | — | ✓ |
| Explainability preserved in UI | — | ✓ |
| Correction/retraction support | ✓ | — |

## Guide catalogue

| Page | Contents |
|------|----------|
| [Best Practices](./best-practices) | Recommended integration patterns |
| [Anti-Patterns](./anti-patterns) | Common mistakes to avoid |
| [Migration Guide](./migration-guide) | Moving from siloed scores to PTI |

## Related normative documents

- [Reference API Specification](/pti/specification/v1.0/reference-api-specification)
- [Reference Event Model](/pti/specification/v1.0/reference-event-model)
- [Interoperability Specification](/pti/specification/v1.0/interoperability)

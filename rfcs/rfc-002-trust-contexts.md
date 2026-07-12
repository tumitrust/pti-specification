---
sidebar_position: 3
title: RFC-002 — Trust Contexts
description: Normative trust context model for PTI.
---

# RFC-002: Trust Contexts

| Field | Value |
|-------|-------|
| **RFC** | 002 |
| **Title** | Trust Contexts |
| **Status** | Proposed |
| **Depends on** | RFC-001 |
| **Updates** | — |

## Abstract

This document specifies the trust context model: named, isolated scopes within which trust events, signals, and scores are collected and evaluated. Contexts prevent cross-domain conflation while enabling lens views that derive read-only aggregates from primaries.

## Motivation

A repayment history in lending must not silently affect merchant onboarding scores. Without normative context boundaries, implementers merge incompatible signal types into a single number, undermining explainability and regulatory defensibility.

## Background

PTI documents twenty contexts: ten **primary** life-area contexts and ten **lens** cross-cutting views. Primaries receive direct producer events. Lenses **MAY** derive signals from configured primary feeds but **MUST NOT** accept direct producer events unless explicitly extended in a future RFC.

## Terminology

| Term | Definition |
|------|------------|
| **Primary context** | Life-area scope that accepts direct trust events |
| **Lens context** | Derived view aggregating signals from one or more primaries |
| **context_id** | Stable lowercase snake_case identifier |
| **Context policy** | Rules governing enabled contexts per producer or consumer contract |
| **Context isolation** | Guarantee that scores in context A are not computed from raw events in context B |

## Specification

### 1. Context identifier format

- `context_id` **MUST** match `^[a-z][a-z0-9_]{1,63}$`.
- `context_id` **MUST** be immutable once published in the context catalogue.
- Implementations **MUST NOT** invent private `context_id` values for interchange; private extensions **MAY** use a `x_` prefix only in non-interoperable local mode.

### 2. Primary context catalogue

The following primary contexts **MUST** be recognized by conforming implementations:

| context_id | Label |
|------------|-------|
| `lending` | Lending & Borrowing |
| `rental` | Rental & Housing |
| `employment` | Employment & Work |
| `insurance` | Insurance & Risk |
| `merchant` | Merchant & Business |
| `partnership` | Business Partnerships |
| `supplier` | Supply Chain |
| `informal_sector` | Informal Economy |
| `agricultural` | Agriculture |
| `remittance` | Money Transfer |

### 3. Lens context catalogue

Lens contexts **MUST** be recognized and **MUST** declare `feeds_from` primary contexts:

| context_id | feeds_from |
|------------|------------|
| `education` | employment, partnership, lending |
| `health` | insurance, employment, partnership |
| `family` | rental, remittance, partnership |
| `civic` | partnership, informal_sector, employment |
| `digital_platform` | merchant, employment, partnership |
| `mobility` | employment, supplier, merchant |
| `utilities` | rental, lending, merchant |
| `creative` | merchant, informal_sector, employment |
| `faith_mutual_aid` | informal_sector, partnership, remittance |
| `risk_compliance` | insurance, employment, civic, merchant |

### 4. Event and lookup binding

- Every trust event **MUST** include exactly one `context_id` referencing a primary context.
- Producers **MUST NOT** emit events for lens contexts.
- Lookup requests **MAY** include primary and/or lens `context_id` values; the platform **MUST** reject contexts not entitled in the consumer contract.
- Scores returned for a lens context **MUST** be labeled `derived: true` in trust evidence.

### 5. Isolation rules

1. Raw events in context C **MUST** influence only scores and signals scoped to C and lenses that list C in `feeds_from`.
2. Cross-context correlation for fraud or identity **MAY** occur only in the identity resolution layer (RFC-011) and **MUST NOT** surface in consumer reports without explicit `risk_compliance` entitlement and legal basis.
3. Disabling a context for a producer **MUST** cause ingest rejection for that `context_id` with a machine-readable error.

### 6. Context enablement

- Producers **MUST** enable only contexts they are contracted to emit.
- Consumers **MUST** request only contexts their contract allows.
- Implementations **SHOULD** expose enabled contexts via a discovery endpoint.

## Security Considerations

- Context entitlement **MUST** be enforced at authentication boundary, not only in application logic.
- Lens derivation pipelines **MUST** be tamper-evident; unauthorized feed wiring **MUST** fail closed.

## Privacy Considerations

- Combining multiple contexts in one lookup **MUST** require documented purpose and, where applicable, subject consent.
- Lens outputs **SHOULD** apply additional redaction when a feed primary is not entitled to the consumer.

## Examples

### Producer configuration

```json
{
  "tenant_id": "prod_abc",
  "enabled_contexts": ["merchant", "digital_platform"]
}
```

Only `merchant` accepts direct events; `digital_platform` is available for consumer lookups as a derived lens.

### Rejected event

```json
{
  "error": "context_not_enabled",
  "context_id": "lending",
  "message": "Producer is not entitled to emit lending events"
}
```

## Implementation Notes

- Store `enabled_contexts` as a set indexed for O(1) ingest validation.
- Precompute lens refresh jobs when any feed primary updates.
- Document context semantics in human-readable catalogues separate from machine validation.

## Future Work

- Community process for proposing new primary contexts
- Context-specific retention schedules
- Localization of context labels without changing `context_id`

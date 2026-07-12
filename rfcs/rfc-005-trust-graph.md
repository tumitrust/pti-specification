---
sidebar_position: 6
title: RFC-005 — Trust Graph
description: Normative trust graph model for PTI.
---

# RFC-005: Trust Graph

| Field | Value |
|-------|-------|
| **RFC** | 005 |
| **Title** | Trust Graph |
| **Status** | Proposed |
| **Depends on** | RFC-001, RFC-003, RFC-011 |
| **Updates** | — |

## Abstract

This document defines the trust graph: the internal representation linking subjects, events, signals, endorsements, and context-scoped outcomes. The graph model enables provenance traversal, lens derivation, and identity resolution without exposing raw cross-context leakage to consumers.

## Motivation

Relational tables alone obscure relationship semantics needed for explainability ("this score changed because of event X from producer Y"). A normative graph model standardizes how implementations store and query trust state.

## Background

The trust graph is an implementation construct; it is not directly exported to consumers except via RFC-012 evidence summaries. Nodes represent entities; edges represent typed, timestamped relationships with provenance metadata.

## Terminology

| Term | Definition |
|------|------------|
| **Node** | Vertex in the trust graph |
| **Edge** | Directed, typed relationship between nodes |
| **Signal** | Derived numeric or categorical artifact from one or more events |
| **Outcome** | Context-scoped score or band materialized for lookup |
| **Traversal** | Query path following edges under policy constraints |

## Specification

### 1. Node types

Implementations **MUST** support at minimum:

| Node type | Identifier | Description |
|-----------|------------|-------------|
| `subject` | `pti_id` | Person or organization |
| `event` | `event_id` | Trust event instance |
| `producer` | `tenant_id` | Trust producer |
| `signal` | `signal_id` | Derived signal |
| `outcome` | `outcome_id` | Context-scoped score snapshot |

Implementations **MAY** add `document`, `endorsement`, `badge` node types.

### 2. Edge types

| Edge | From → To | **MUST** attributes |
|------|-----------|---------------------|
| `emitted` | producer → event | `ingested_at` |
| `pertains_to` | event → subject | `context_id` |
| `derived_from` | signal → event | `derivation_rule` |
| `contributes_to` | signal → outcome | `weight` |
| `resolves_to` | external_entity → subject | `confidence` |
| `feeds` | primary_outcome → lens_outcome | `lens_id` |

Edges **MUST** be immutable once written; corrections append compensating edges.

### 3. Context scoping on graph

- Every `event` node **MUST** carry `context_id`.
- Traversals for consumer evidence **MUST** filter by requested contexts and entitlements.
- Cross-context traversals for fraud **MAY** exist in restricted admin partitions not exportable via lookup API.

### 4. Signal derivation

- Signals **MUST** reference source events via `derived_from` edges.
- Derivation rules **MUST** be versioned strings (e.g., `lending_repayment_velocity@v3`).
- Reprocessing **MUST** create new signal nodes rather than overwrite historical signals.

### 5. Outcome materialization

- Outcomes **MUST** be keyed by (`pti_id`, `context_id`, `outcome_version`).
- Lookup reports **MUST** snapshot outcome state at `generated_at` with pointer to graph evidence.
- Stale outcomes **SHOULD** trigger refresh when underlying events arrive within SLA window.

### 6. Graph integrity

Implementations **MUST**:

1. Reject orphan `event` nodes without `pertains_to` subject edge.
2. Maintain referential integrity on edge endpoints.
3. Support point-in-time reconstruction for audit given `report_id`.

### 7. Query API (internal)

Internal services **SHOULD** expose:

- `get_subject_timeline(pti_id, context_id, window)`
- `explain_outcome(outcome_id) → { signals, events, producers }`
- `lens_refresh(primary_context_id)`

These are not consumer APIs but **RECOMMENDED** for operator tooling and certification tests.

## Security Considerations

- Graph admin APIs **MUST** require elevated roles; bulk export **MUST NOT** be available to standard consumer credentials.
- Edge attributes containing PII **MUST** be redacted in non-production environments.

## Privacy Considerations

- Graph retention **MUST** honor subject deletion propagating to nodes and edges per RFC-007.
- Anonymized graph analytics **MAY** retain aggregate statistics with k-anonymity thresholds ≥ 50.

## Examples

### Explain outcome traversal

```
subject(pti_k9m2)
  ← pertains_to ← event(evt_7f3a) ← emitted ← producer(mfi_101)
  → derived_from → signal(sig_repay_vel)
  → contributes_to → outcome(out_lending_20260701)
```

### Lens feed edge

```
outcome(out_merchant_*) ──feeds──► outcome(out_digital_platform_*)
```

## Implementation Notes

- Property graph or adjacency-list relational models are both acceptable.
- Version derivation rules in config, not hard-coded strings in application code.
- Precompute outcome bands on write path for lookup latency; keep full graph for explainability.

## Future Work

- Graph interchange format for federation (RFC-006)
- Standard graph metrics for risk propagation research
- Formal semantics for endorsement edge types

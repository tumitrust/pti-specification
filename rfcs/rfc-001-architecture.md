---
sidebar_position: 2
title: RFC-001 — PTI Architecture
description: Normative architecture for Portable Trust Infrastructure.
---

# RFC-001: PTI Architecture

| Field | Value |
|-------|-------|
| **RFC** | 001 |
| **Title** | Portable Trust Infrastructure Architecture |
| **Status** | Proposed |
| **Depends on** | — |
| **Updates** | — |

## Abstract

This document defines the reference architecture for Portable Trust Infrastructure (PTI): a vendor-neutral framework that separates **trust generation** (producers) from **trust consumption** (consumers) while maintaining a portable subject identity, context-scoped outcomes, and auditable provenance. PTI is not a credit bureau protocol; it specifies infrastructure for context-bound trust intelligence at decision time.

## Motivation

Digital trust data is fragmented across institution silos, opaque scoring models, and incompatible APIs. PTI addresses three structural failures:

1. **Portability** — subjects cannot carry credible proof across life areas without re-proving identity and history.
2. **Context conflation** — lending, employment, and merchant signals are merged into single opaque scores.
3. **Auditability** — decision-makers lack machine-readable provenance linking outcomes to source events.

A normative architecture enables independent implementations to interoperate without prescribing a single vendor stack.

## Background

PTI emerged from operational patterns in multi-sided trust platforms: partners emit verified activity; a processing layer normalizes signals into context-scoped scores; institutions request lookups with explainable outputs. Prior art includes credit reporting networks (FCRA-style), verifiable credential frameworks (W3C VC), and identity federation (OpenID, SAML). PTI differs by mandating **context isolation**, **event-level provenance**, and **lookup-time minimization** rather than batch file exchange of raw tradelines.

## Terminology

| Term | Definition |
|------|------------|
| **PTI-ID** | Portable subject identifier assigned by an identity resolution service |
| **Trust producer** | Entity that ingests trust events on behalf of subjects |
| **Trust consumer** | Entity that requests trust lookups for decision workflows |
| **Trust context** | Named life-area scope for signals and scores (see RFC-002) |
| **Trust event** | Atomic attestable activity record (see RFC-003) |
| **Trust lookup** | Consumer request evaluating a subject for a bounded purpose |
| **Trust evidence** | Provenance bundle attached to lookup outcomes (see RFC-012) |
| **Control plane** | Policy, consent, governance, and audit services |
| **Data plane** | Event ingest, graph storage, scoring, and lookup delivery |

## Specification

### 1. Architectural principles

A PTI-compatible implementation **MUST** uphold:

1. **Separation of generation and consumption** — producers and consumers are distinct roles with separate API surfaces.
2. **Context scoping** — no score or signal **MUST** leak across trust context boundaries without explicit lens derivation rules (RFC-002).
3. **Provenance by default** — every published outcome **MUST** reference trust evidence (RFC-012).
4. **Lookup-time binding** — consumers **MUST** declare purpose and entitled contexts per lookup request (RFC-004).
5. **Fail-closed policy** — ambiguous consent, entitlement, or security state **MUST** deny the operation.

### 2. Logical layers

```
┌─────────────────────────────────────────────────────────┐
│  Consumer applications (lookup clients)                 │
├─────────────────────────────────────────────────────────┤
│  Lookup API · Report tiers · Explainability export      │
├─────────────────────────────────────────────────────────┤
│  Processing · Scoring · Graph · Identity resolution     │
├─────────────────────────────────────────────────────────┤
│  Event ingest · Validation · Normalization              │
├─────────────────────────────────────────────────────────┤
│  Producer connectors (API · webhook · batch)            │
├─────────────────────────────────────────────────────────┤
│  Control plane: consent · governance · audit · metering │
└─────────────────────────────────────────────────────────┘
```

Implementations **MAY** collapse layers physically but **MUST NOT** bypass control-plane checks.

### 3. Roles and responsibilities

| Role | **MUST** | **MUST NOT** |
|------|----------|--------------|
| **Producer** | Emit catalogued events with `context_id`; honor subject consent | Emit events outside contracted contexts |
| **Consumer** | Request only entitled contexts; retain explainability | Republish raw trust data outside lawful basis |
| **Subject** | Control sharing where jurisdiction requires | — |
| **Platform operator** | Enforce policy, metering, audit | Use lookup data for undisclosed secondary purposes |

### 4. Trust lifecycle

1. **Provision** — subject receives or resolves to a PTI-ID (RFC-011).
2. **Ingest** — producer submits trust events (RFC-003).
3. **Normalize** — platform validates, deduplicates, attaches to graph (RFC-005).
4. **Score** — context-scoped outcomes refresh asynchronously.
5. **Lookup** — consumer requests bounded report (RFC-004).
6. **Evidence** — outcome ships with provenance chain (RFC-012).
7. **Govern** — retention, deletion, and portability enforced (RFC-007, RFC-009).

Steps 3–4 **MAY** be near-real-time or batch; steps 5–6 **MUST** be synchronous from the consumer perspective.

### 5. Metering and compute

Implementations that bill producers **SHOULD** meter processing as **Trust Compute Units (TCU)** per context per billing period. TCU is not normative for conformance but **RECOMMENDED** for cross-vendor cost transparency.

### 6. Deployment topologies

| Topology | Description | Conformance profile |
|----------|-------------|---------------------|
| **Centralized** | Single operator runs all layers | Core, Enterprise |
| **Federated** | Multiple operators exchange via RFC-006 | Enterprise, Government |
| **Edge** | Local ingest and lookup with periodic sync | Edge |

## Security Considerations

- All inter-service and public APIs **MUST** use mutually authenticated TLS 1.2 or higher.
- Producer and consumer credentials **MUST** be scoped to entitled contexts.
- Audit logs **MUST** record lookup requests without storing excessive subject PII.
- See RFC-008 for the complete threat model.

## Privacy Considerations

- Architecture **MUST** support data minimization at lookup time (RFC-009).
- Processing layers **MUST NOT** retain raw events beyond documented retention (RFC-007).
- Cross-border data flows **MUST** be policy-gated per jurisdiction configuration.

## Examples

### Minimal lending workflow

1. MFI (producer) emits `loan_repayment` events tagged `context_id: lending`.
2. Platform normalizes events and refreshes lending context score.
3. Digital lender (consumer) calls lookup API with `contexts: [lending]` and `tier: detailed`.
4. Response includes score drivers referencing event IDs and timestamps.

### Hybrid tenant

A marketplace that both sells (producer) and underwrites sellers (consumer) **MUST** use separate API credentials and audit namespaces for each role even if operated by one legal entity.

## Implementation Notes

- Start with RFC-002 context catalogue before building ingest validators.
- Graph storage (RFC-005) can begin as relational edges; scale to graph DB when edge cardinality exceeds operational thresholds.
- Separate **sandbox** and **production** control-plane namespaces early to avoid consent bleed.

## Future Work

- Reference deployment manifests for Kubernetes-style operators
- Hardware security module requirements for government profile
- Standard observability metrics (OpenTelemetry semantic conventions for PTI)

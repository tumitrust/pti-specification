---
title: PTI Glossary
description: Canonical definitions for Portable Trust Infrastructure terms — 35+ entries with cross-references.
sidebar_position: 1
---

# PTI Glossary

Canonical definitions for terms used across the Portable Trust Infrastructure (PTI) documentation. Where a term appears in the normative specification, the definition here is **informative** unless explicitly incorporated by reference.

## Core infrastructure

### Portable Trust Infrastructure (PTI)

An open architecture for generating, exchanging, resolving, and consuming **trust intelligence** across institutional boundaries. PTI separates trust production from trust consumption and binds outcomes to explicit **trust contexts**.

### Trust Infrastructure

The collective services, APIs, schemas, and governance frameworks that implement portable trust. Trust Infrastructure encompasses registries, exchanges, intelligence engines, and consumer integrations.

### Trust Fabric

The operational layer connecting **trust producers**, the **trust graph**, and **trust consumers**. The fabric enforces policy, routes events, and derives context-scoped outcomes.

## Identity and subjects

### Trust Subject

An individual or organization whose trust signals, contexts, and lookups are governed under PTI. Identified by `pti_id` in the trust registry. Also referred to as **subject** in API and governance text.

### Trust Identity

A registry-managed identity record for a subject, keyed by `pti_id`, with lifecycle state, verification level, and optional partner references.

### Trust Credential

A cryptographically verifiable claim, signed assertion, or verifiable credential presented as trust evidence — distinct from a derived **context score**.

### pti_id

The portable subject identifier allocated by the Trust Registry. Format: `pti_` prefix plus opaque suffix. **MUST NOT** embed raw personally identifiable information.

### Trust Profile

The aggregate view of a subject's signals, context scores, relationships, and assertions across enabled contexts. Distinct from a point-in-time **trust report**.

### Trust Passport

A subject-controlled bundle of portable proofs — typically signed **trust assertions** and recent context summaries — intended for sharing with institutions.

## Context and scoring

### Trust Context

A registered life-area or lens scope (e.g., `lending`, `risk_compliance`) that isolates signals, scores, and lookup entitlements.

### Context Score

A derived 0–100 outcome for a single `context_id`, accompanied by band, confidence, drivers, and coverage gaps.

### Trust Score

An informal umbrella term for credibility outcomes. PTI implementations **SHOULD** prefer **context scores** over a single cross-context number unless an explicit aggregation policy is published.

### Trust Confidence

A 0.0–1.0 measure of certainty in a score, match, or assertion — distinct from the score magnitude itself.

## Graph and relationships

### Trust Graph

The persistent graph of subjects, producers, contexts, signals, evidence, and relationships used for traversal, scoring, and provenance.

### Trust Relationship

A durable edge between subjects or between a subject and an institution (employment, tenancy, partnership, endorsement) that may unlock relationship-scoped signals.

### Trust Chain

An ordered sequence of attestations or endorsements linking a subject to trusted anchors (verifiers, institutions, community nodes).

## Signals and evidence

### Trust Signal

A normalized, context-bound representation of verified activity derived from a **trust event**, with polarity, weight, and provenance.

### Trust Event

The canonical ingest unit — an immutable record of activity submitted by a **trust producer** with `event_type`, `context_id`, and `pti_id`.

### Trust Evidence

Supporting artifacts (documents, registry matches, endorsements, badges) that substantiate signals and assertions.

### Trust Assertion

A signed, context-scoped claim about a subject, verifiable independently of the intelligence engine's derived scores.

### Trust Provenance

Metadata describing who attested data, when, through which channel, and under which verification level.

## Roles and exchange

### Trust Producer

An organization that ingests verifiable activity and emits **trust events** for subjects it serves.

### Trust Consumer

An institution that requests **trust lookups** and applies context-scoped intelligence at decision time.

### Trust Exchange

The routing and policy layer accepting events and assertions, enforcing entitlements, and triggering materialization.

### Trust Registry

The authoritative directory for `pti_id` values, context and event catalogs, and tenant entitlements.

### Trust Resolution

The process of mapping partner entity identifiers and identity hints to a `pti_id` with stated match confidence.

### Trust Lookup

A decision-time request to retrieve context-scoped trust intelligence for a subject at an entitled tier.

### Trust Verification

Confirmation that a report, assertion, or identity claim is authentic, unexpired, and issued under valid entitlements.

### Trust Exchange (protocol)

*See Trust Exchange above.* In API nomenclature, also refers to the ingest and assertion publish service surface.

## Intelligence and APIs

### Trust Intelligence

Structured, context-scoped outcomes — scores, drivers, provenance, coverage gaps — produced for consumers. Not equivalent to a traditional credit bureau file.

### Trust Intelligence Engine

The component that refreshes **context scores** and explainability from the **trust graph** according to policy tables.

### Trust API

The collective Registry, Exchange, and Lookup HTTP (or equivalent) service surfaces defined by the PTI specification.

### Trust Engine

Informal synonym for **Trust Intelligence Engine** in operator documentation.

## Derived artifacts and mechanics

### Trust Token

A short-lived, scoped credential authorizing a specific lookup or consent-gated operation.

### Trust Credits

An implementation-specific metering unit for compute or lookup consumption. Billing semantics are deployment-specific and not normative in PTI v1.0.

### Trust Index

An ordered registry of contexts, producers, or signals used for discovery and analytics — not a single consumer-facing score.

### Trust Lens

A cross-cutting **trust context** derived from primary contexts (e.g., `risk_compliance` lens aggregating signals from multiple primaries).

### Trust Router

A policy-aware component directing events, assertions, or lookup requests to the correct partition, region, or federation peer.

### Trust Policy

Machine-readable rules governing entitlements, retention, consent, signal decay, and field visibility.

## Governance and analytics

### Trust Governance

The framework of consent, accountability, retention, dispute resolution, and audit obligations across producers, consumers, and registry operators.

### Trust Analytics

Aggregated, privacy-preserving metrics on signal volume, lookup patterns, score distributions, and producer quality — distinct from individual **trust reports**.

## Related resources

- [Reference Architecture](/pti/reference-architecture/)
- [Specification v1.0](/pti/specification/v1.0/)
- [Implementation Guide](/pti/implementation-guide/)

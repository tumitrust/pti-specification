---
title: Reference Implementations
description: PTI reference implementations — production deployments that demonstrate the specification in practice.
sidebar_position: 22
---

# PTI Reference Implementations

A **reference implementation** demonstrates that the PTI specification is implementable, operable, and useful at production scale. Reference implementations contribute operational learnings back into RFCs and specification revisions.

## Policy summary

Reference implementation status is governed by the [Reference Implementation Policy](/pti/governance/reference-implementation-policy). Key rules:

- Reference implementations **MUST** publish conformance statements
- Reference implementations **MUST NOT** claim to be the only valid implementation
- Specification changes **SHOULD** cite reference implementation experience as implementation notes, not as exclusive requirements

## Current reference implementations

| Implementation | Operator | Profile | Status |
|----------------|----------|---------|--------|
| **TumiTrust Platform** | TumiTrust Ltd. | Enterprise (Government capabilities in sovereign deployments) | Production — flagship reference |

### TumiTrust Platform

TumiTrust provides the first production-scale demonstration of PTI:

- Trust producers ingest events via webhook, API, and CSV
- Institutions consume context-scoped trust lookups with explainability
- Portable `pti_id` identity across partners and contexts
- Operational runbooks feeding back into specification design

**Product documentation:** [TumiTrust Documentation](/tumitrust/product-overview/)

**Positioning:** TumiTrust is a reference implementation, not the specification owner. See [public governance statement](/pti/governance/public-governance-statement).

## Becoming a reference implementation

Implementers **MAY** apply for reference implementation listing when they:

1. Achieve **PTI Core Certified** or higher
2. Operate production traffic for ≥12 months
3. Publish anonymized operational metrics (availability, lookup latency bands)
4. Commit to RFC participation for breaking changes they depend on

Apply via the [contribution process](/pti/governance/contribution-process).

## Future implementations

PTI is designed for **multiple compatible implementations**. Independent operators in government, banking, and fintech are expected as the ecosystem matures. The specification does not require shared infrastructure — only interoperable contracts.

## Related

- [Build Your Own PTI](/pti/build-your-pti/)
- [Conformance profiles](/pti/conformance/profiles)
- [Ecosystem roadmap](/pti/governance/ecosystem-roadmap)

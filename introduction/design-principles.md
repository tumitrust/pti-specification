---
title: Core Design Principles
description: Foundational PTI principles with rationale and examples.
sidebar_position: 5
---

import NormativeLegend from '@site/src/components/NormativeLegend';

<NormativeLegend />

# Core Design Principles

These principles are constitutional to PTI. Implementations MUST NOT trade them for speed or convenience.

| Principle | Rationale | Example |
|-----------|-----------|---------|
| **Portable by Design** | Trust must follow the subject across institutions | A `pti_id` persists across partners and contexts |
| **Context Native** | Signals belong to life domains, not global scores | Lending and rental scores are isolated |
| **Evidence Driven** | Scores trace to attributable signals | Every score change links to a trust event |
| **Consent First** | Subjects control signal sharing | Consent gates before cross-context resolution |
| **Privacy Preserving** | Minimum necessary disclosure | Consumers receive envelopes, not raw partner data |
| **Explainable** | Decisions carry machine-readable provenance | Explain APIs return signal attribution |
| **Composable** | PTI orchestrates existing trust technologies | KYC output becomes a trust signal input |
| **API First** | Trust is programmatic infrastructure | Standard lookup, exchange, and registry APIs |
| **Vendor Neutral** | Specification is implementation-independent | Any organization can build a compatible platform |
| **Interoperable** | Cross-implementation trust exchange | Federated registries with conformance profiles |
| **Real-Time** | Trust resolves at decision time | Sub-second lookup with cached intelligence |
| **Auditable** | Every action is observable and replayable | Trust events are idempotent and logged |
| **Extensible** | New contexts and signal types via extension framework | Custom trust contexts with schema registration |
| **Resilient** | Degraded operation without data loss | Queue-based ingestion with at-least-once delivery |
| **Human-Centric** | Infrastructure serves people, not only institutions | Subjects own portable trust profiles |

## Related

- [PTI Specification v1.0](/pti/specification/v1.0/)
- [RFC-007 Governance](/pti/rfcs/rfc-007-governance)

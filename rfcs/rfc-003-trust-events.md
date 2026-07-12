---
sidebar_position: 4
title: RFC-003 — Trust Events
description: Normative trust event schema and ingest rules for PTI.
---

# RFC-003: Trust Events

| Field | Value |
|-------|-------|
| **RFC** | 003 |
| **Title** | Trust Events |
| **Status** | Proposed |
| **Depends on** | RFC-001, RFC-002 |
| **Updates** | — |

## Abstract

This document defines the trust event: the atomic unit of trust generation in PTI. It specifies required fields, ingest channels, validation rules, idempotency, and error semantics for producer-submitted activity records.

## Motivation

Without a canonical event schema, producers emit incompatible payloads, scoring pipelines cannot attribute outcomes, and consumers cannot audit decisions. Normative events enable interchangeable connectors and test suites.

## Background

Trust events represent attestable activity — repayments, lease completions, employment verifications, merchant transactions — normalized from partner systems. Events differ from lookup requests: they are **writes** on the producer plane, asynchronous to consumer reads.

## Terminology

| Term | Definition |
|------|------------|
| **event_id** | Producer-supplied or platform-assigned unique identifier |
| **event_type** | Catalogued string describing activity semantics |
| **ingest_channel** | Delivery mechanism: `api`, `webhook`, `csv`, `connector` |
| **entity_id** | Producer-local subject identifier |
| **occurred_at** | Timestamp when activity happened in the real world |
| **ingested_at** | Timestamp when platform accepted the event |
| **payload** | Context-specific structured attributes |

## Specification

### 1. Event envelope

Every accepted event **MUST** contain:

| Field | Type | Required | Rules |
|-------|------|----------|-------|
| `event_id` | string | **MUST** | Unique per producer tenant; max 128 chars |
| `event_type` | string | **MUST** | From implementation event catalogue |
| `context_id` | string | **MUST** | Primary context per RFC-002 |
| `entity_id` | string | **MUST** | Producer subject key |
| `occurred_at` | ISO 8601 | **MUST** | MUST NOT be more than 10 years in past or 24h in future |
| `payload` | object | **MUST** | Schema validated per `event_type` |
| `ingest_channel` | enum | **MUST** | Set by platform from delivery path |

Optional fields **MAY** include: `idempotency_key`, `source_system`, `correlation_id`, `locale`, `currency`.

### 2. Idempotency

- Producers **SHOULD** supply `idempotency_key` for at-least-once delivery channels.
- Platforms **MUST** deduplicate on (`tenant_id`, `idempotency_key`) for 72 hours minimum.
- Duplicate submissions **MUST** return `200` with original `event_id` without double-processing.

### 3. Ingest channels

| Channel | **MUST** support | Notes |
|---------|------------------|-------|
| `api` | Synchronous ack | Primary integration path |
| `webhook` | Signature verification | HMAC-SHA256 **RECOMMENDED** |
| `csv` | Batch validation report | Row-level errors without failing entire file |
| `connector` | Adapter metadata | Channel for certified connector packs |

### 4. Validation pipeline

1. Authenticate producer credential.
2. Verify `context_id` enabled for tenant.
3. Validate `event_type` registered and `payload` schema.
4. Resolve `entity_id` to PTI-ID (RFC-011).
5. Persist event; enqueue processing.
6. Return acceptance receipt.

Validation failures **MUST** return machine-readable errors without partial graph mutation.

### 5. Prohibited payload categories

Events **MUST NOT** include:

- Full payment card numbers (PAN)
- Raw biometric templates without explicit contract and legal basis
- Special-category data under GDPR Article 9 unless lawfully permitted and documented
- Passwords or authentication secrets

### 6. Event catalogue

Implementations **MUST** publish a versioned event catalogue mapping `event_type` → JSON Schema. New types **MUST** follow RFC-010 versioning rules.

### 7. Processing semantics

- Accepted events **MUST** be immutable; corrections **MUST** use compensating `event_type` values (e.g., `loan_repayment_reversal`).
- Deletion requests **MUST** propagate from governance layer (RFC-007), not silent producer overwrite.

## Security Considerations

- Webhook endpoints **MUST** verify signatures and reject replayed timestamps outside skew window (±300s **RECOMMENDED**).
- CSV uploads **MUST** be virus-scanned and size-limited.
- Event payloads **MUST** be encrypted at rest.

## Privacy Considerations

- `payload` **SHOULD** use minimum fields necessary for scoring.
- Pseudonymous `entity_id` **SHOULD** be preferred over national IDs in transit where resolution happens server-side.

## Examples

### Loan repayment event

```json
{
  "event_id": "evt_7f3a",
  "event_type": "loan_repayment",
  "context_id": "lending",
  "entity_id": "borrower_8821",
  "occurred_at": "2026-06-15T14:22:00Z",
  "payload": {
    "loan_id": "ln_991",
    "amount_minor": 150000,
    "currency": "ZAR",
    "status": "on_time"
  },
  "idempotency_key": "mfi_batch_20260615_8821"
}
```

### Acceptance receipt

```json
{
  "status": "accepted",
  "event_id": "evt_7f3a",
  "pti_id": "pti_k9m2",
  "ingested_at": "2026-06-15T14:22:04Z"
}
```

## Implementation Notes

- Index (`tenant_id`, `entity_id`, `occurred_at`) for timeline queries.
- Emit internal `trust_signal` derivations asynchronously; do not block ingest on scoring.
- Maintain dead-letter queue for schema failures with producer notification.

## Future Work

- Standard event catalogue registry per industry vertical
- Binary attachment references for document-backed events
- Streaming ingest (Kafka/gRPC) profile for Enterprise

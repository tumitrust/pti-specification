---
sidebar_position: 6
title: Interoperability Rules
description: Interoperability rules for PTI implementations.
---

# Interoperability Rules

Rules ensuring independent PTI implementations exchange data and serve consumers predictably. Primary normative references: [RFC-004](/pti/rfcs/rfc-004-trust-lookup-api), [RFC-006](/pti/rfcs/rfc-006-trust-exchange), [RFC-010](/pti/rfcs/rfc-010-versioning).

## API interoperability

### URL and versioning

- Public REST APIs **MUST** use `/v{major}/` prefix (e.g., `/v1/reports/generate`).
- OpenAPI 3.1 document **MUST** be published at stable URL.
- Error responses **MUST** use consistent JSON envelope:

```json
{
  "error": "context_not_entitled",
  "message": "Human-readable description",
  "details": { }
}
```

### Required operations

Any implementation claiming PTI compatibility **MUST** implement:

| Operation | Interop critical fields |
|-----------|-------------------------|
| `subjects.search` | `candidates[].pti_id`, `confidence` |
| `reports.generate` | `report_id`, `trust_evidence`, `schema_version` |
| `reports.get` | Immutable content hash match on repeat |

### Optional but interoperable when enabled

| Operation | Rule |
|-----------|------|
| `reports.verify` | Must validate RFC-012 signatures cross-vendor |
| `contexts.list` | Must return RFC-002 catalogue subset |
| Federation `/.well-known/pti-federation` | Must match RFC-006 schema |

## Schema interoperability

### Postel's law

- **Senders** MUST conform to published schema for their catalogue version.
- **Receivers** MUST ignore unknown optional fields.
- **Receivers** MUST reject unknown required fields in their major version.

### Identifiers

| Identifier | Format | Interop rule |
|------------|--------|--------------|
| `pti_id` | `pti_[a-z0-9]{8,32}` | Never embed national ID |
| `context_id` | RFC-002 catalogue | No private interchange IDs |
| `event_type` | Catalogue string | Document in OpenAPI + catalogue |
| `report_id` | Opaque string | Globally unique per operator |
| `manifest_id` | Opaque string | Referenced in verify + PRL |

## Context interoperability

- Producers on different implementations **MUST** use same `context_id` for same life area.
- Lens contexts **MUST NOT** accept direct events at any implementation.
- Disabled context **MUST** fail with `context_not_enabled` (not generic 500).

## Evidence interoperability

Cross-vendor verification **MUST** succeed when:

1. Manifest `schema_version` is supported by verifier
2. `kid` exists in issuer JWKS
3. Signature validates on JCS-canonicalized JSON
4. `manifest_id` not on PRL

Verifier **MUST NOT** require proprietary SDK.

## Federation interoperability

### Package exchange

| Rule | Detail |
|------|--------|
| Profile negotiation | Bilateral agreement declares `manifest_only` or richer |
| Signature algorithm | Ed25519 or RSA-3072+ only for interop |
| Foreign PTI-ID | `pti_local@operator_id` format mandatory |
| Attribution | Combined lookup labels `origin_operator_id` per slice |
| Failure | Invalid package omitted from scores (fail closed) |

### JWKS

```json
{
  "keys": [
    {
      "kid": "op_example_2026",
      "kty": "OKP",
      "crv": "Ed25519",
      "x": "..."
    }
  ]
}
```

Operators **MUST** support HTTPS JWKS fetch with cache ≤ 24h.

## Event catalogue interoperability

Shared vertical catalogues **SHOULD** use common `event_type` strings:

| event_type | context_id | Interop payload fields |
|------------|------------|------------------------|
| `loan_repayment` | lending | `amount_minor`, `currency`, `status` |
| `lease_completed` | rental | `lease_id`, `status` |
| `employment_verified` | employment | `employer_ref`, `role`, `verified_at` |

Custom events **MAY** exist locally but **MUST NOT** be assumed by foreign consumers.

## Edge sync interoperability

Edge nodes **MUST** upload `pti.exchange.v1` packages with:

- `origin_operator_id` of hub
- Edge-generated `report_id` registered on hub after sync
- Conflict metadata for duplicate `idempotency_key`

Hub **MUST** acknowledge with `sync_receipt_id`.

## Version negotiation

| Scenario | Behavior |
|----------|----------|
| Client sends `Accept-Version: 1` | Server responds v1 schema |
| Unknown major in package | `verification_status: invalid` |
| Deprecated endpoint | `Deprecation` + `Sunset` headers |

## Conformance testing for interop

Before federation go-live:

1. Run Category F tests ([conformance-tests](/pti/conformance/conformance-tests))
2. Cross-verify sample manifests with partner JWKS
3. Execute dual ingest → dual lookup → evidence verify round-trip

## Anti-patterns breaking interop

| Pattern | Impact |
|---------|--------|
| Custom context IDs in federation | Partner cannot map scores |
| Unsigned reports | Verification fails cross-vendor |
| Mutating `report_id` content | Audit hash mismatch |
| Proprietary error codes only | Client cannot handle uniformly |
| Skipping `schema_version` | Verifier cannot parse |

## Related

- [RFC-006 Trust Exchange](/pti/rfcs/rfc-006-trust-exchange)
- [Conformance profiles](/pti/conformance/profiles)
- [Implementation examples](./implementation-examples)

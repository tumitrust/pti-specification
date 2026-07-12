---
sidebar_position: 7
title: Implementation Examples
description: Reference implementation patterns for PTI builders.
---

# Implementation Examples

Vendor-neutral reference patterns illustrating how to satisfy PTI RFCs. Adapt to your stack; behavior **MUST** match normative requirements.

## Example 1 — Core lending operator (single region)

### Scope

- Profile: **PTI Core**
- Primary context: `lending`
- Channels: API ingest only
- Tiers: `basic`, `detailed`

### Architecture sketch

```
Partner Core Banking → API Gateway → Ingest Service → PostgreSQL (events)
                              ↓
                       Identity Service → pti_ids table
                              ↓
                       Graph Writer → edges table
                              ↓
                       Scoring Worker (Redis queue) → outcomes cache
                              ↓
Consumer LOS → Lookup API → Evidence Builder → Signing Service (KMS)
```

### Event ingest handler (pseudocode)

```python
def ingest_event(tenant, body):
    policy.assert_context_enabled(tenant, body["context_id"])
    schema.validate(body["event_type"], body["payload"])
    policy.scan_prohibited(body["payload"])

    if idempotent_duplicate(tenant, body.get("idempotency_key")):
        return existing_receipt(body["idempotency_key"])

    pti_id = identity.resolve_entity(tenant, body["entity_id"])
    store_event(body, pti_id)
    graph.append_event(body, pti_id)
    queue.publish("score", {"pti_id": pti_id, "context_id": body["context_id"]})

    return {"status": "accepted", "event_id": body["event_id"], "pti_id": pti_id}
```

### Scoring rule example

```yaml
derivation_rule: lending_repayment_velocity@v1
context_id: lending
inputs:
  - event_type: loan_repayment
    window_days: 365
logic:
  on_time_ratio: count(status=on_time) / count(all)
output_signal: repay_velocity
weight_in_outcome: 0.42
```

### Lookup response excerpt

```json
{
  "report_id": "rpt_88ac",
  "tier": "detailed",
  "scores": { "lending": { "band": "B+", "value": 712 } },
  "drivers": [
    {
      "label": "on_time_repayments_12m",
      "weight": 0.42,
      "event_refs": ["evt_7f3a"]
    }
  ],
  "trust_evidence": {
    "manifest_id": "evm_22bd",
    "schema_version": "trust_evidence.v1",
    "signature": { "alg": "EdDSA", "kid": "op_2026", "value": "…" }
  }
}
```

---

## Example 2 — Enterprise multi-tenant SaaS

### Additional capabilities

- Webhook + CSV ingest
- Lens contexts: `risk_compliance`, `digital_platform`
- Predictive tier with `model_version`
- Public `reports.verify`

### Tenant isolation pattern

```sql
-- Every query includes tenant_id from auth context
SELECT * FROM events
WHERE tenant_id = $authenticated_tenant
  AND pti_id = $pti_id;
```

Row-level security **RECOMMENDED** as defense in depth.

### Webhook verification

```python
def verify_webhook(secret, headers, body):
    ts = headers["X-PTI-Timestamp"]
    sig = headers["X-PTI-Signature"]
    if abs(now() - parse(ts)) > 300:
        raise ReplayError()
    expected = hmac_sha256(secret, f"{ts}.{body}")
    if not constant_time_equal(sig, expected):
        raise AuthError()
```

### CSV batch flow

1. Upload to object storage
2. Batch worker validates each row → `validation_report.json`
3. Accepted rows ingest via same path as API
4. Producer notified with row-level errors

---

## Example 3 — Government federated hub

### Additional capabilities

- HSM signing
- Accredited exchange with sector bureaus
- `screening_dossier` tier
- PRL polling every 4 hours

### Federation verify path

```python
def merge_federated_lookup(local_report, foreign_packages):
    slices = [local_report.scores]
    for pkg in foreign_packages:
        if not verify_signature(pkg, fetch_jwks(pkg["origin_operator_id"])):
            audit.log("federation_verify_failed", pkg["manifest_id"])
            continue  # fail closed
        if is_revoked(pkg["manifest_id"]):
            continue
        slices.append({
            "scores": pkg["report_manifest"]["scores"],
            "origin_operator_id": pkg["origin_operator_id"]
        })
    return combine_with_attribution(slices)
```

### JWKS publication

Host at `https://registry.example.gov/.well-known/pti-federation` with operator metadata and `keys_url`.

---

## Example 4 — Edge node (rural MFI)

### Deployment

- Field tablet captures repayments offline
- Local SQLite event queue
- Nightly sync uploads exchange packages to hub

### Offline ingest

```python
def edge_ingest(event):
    local_db.insert("pending_events", event)
    local_graph.append(event, resolve_local_pti(event))
    local_scoring.refresh(event["context_id"])
    return local_receipt(event)
```

### Sync on connect

```python
def sync_to_hub():
    for batch in local_db.pending_packages():
        resp = hub.post("/exchange/upload", batch)
        local_db.mark_synced(batch["id"], resp["sync_receipt_id"])
```

### Hub verification

Edge-issued `report_id` registered on hub during sync; `reports.verify` delegates to hub manifest.

---

## Example 5 — Evidence builder

```python
def build_manifest(report, graph):
    drivers = graph.explain_outcome(report.outcome_id)
    sources = redact_sources(drivers.events, report.entitlements)
    manifest = {
        "manifest_id": new_id(),
        "schema_version": "trust_evidence.v1",
        "report_id": report.id,
        "pti_id": report.pti_id,
        "generated_at": report.generated_at,
        "contexts": report.contexts,
        "outcomes": report.outcomes,
        "drivers": drivers,
        "sources": sources,
    }
    manifest["signature"] = sign(jcs_canonicalize(manifest))
    return manifest
```

---

## Example 6 — Policy engine call

```python
def authorize_lookup(consumer, request):
    if not consumer.entitled_contexts.issuperset(request.contexts):
        raise PolicyError("context_not_entitled")
    if not consumer.allowed_tiers.includes(request.tier):
        raise PolicyError("tier_not_allowed")
    if consent_required(request.purpose_code):
        if not consent.valid(request.consent_ref, request.contexts):
            raise PolicyError("consent_required")
    audit.log_lookup_request(consumer, request)
```

---

## OpenAPI snippet

```yaml
paths:
  /v1/reports/generate:
    post:
      operationId: reportsGenerate
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/GenerateReportRequest'
      responses:
        '200':
          description: Report generated
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/TrustReport'
```

Publish alongside event catalogue JSON Schema artefacts.

## Testing fixture

Seed sandbox with:

- `pti_test_alpha` — 12 on-time repayments
- `pti_test_beta` — mixed repayment history
- `pti_test_gamma` — no events (external route tests)

Use for [conformance-tests](/pti/conformance/conformance-tests) Categories A–L.

## Related

- [Required components](./required-components)
- [Migration examples](./migration-examples)
- [RFC index](/pti/rfcs/)

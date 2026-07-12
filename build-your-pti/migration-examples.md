---
sidebar_position: 8
title: Migration Examples
description: Migration patterns from legacy systems to PTI.
---

# Migration Examples

Patterns for migrating from legacy credit files, custom scoring databases, and partner silos to PTI-compatible infrastructure. Each example includes phased cutover, data mapping, and rollback strategy.

## Migration principles

1. **Parallel run before cutover** — dual-write or shadow lookup comparison
2. **Context-first mapping** — assign RFC-002 `context_id` before event transform
3. **Identity resolution early** — map legacy customer IDs to PTI-ID before bulk ingest
4. **Evidence from day one** — do not launch lookups without signed manifests
5. **Rollback path** — keep legacy read-only until PTI passes conformance self-test

## Example 1 — MFI core banking to PTI lending

### Starting state

- MySQL `loan_transactions` table
- Internal `customer_id` integer
- Custom 0–100 score in nightly batch job
- No API; CSV exports to partners

### Target state

- PTI Core with `lending` context
- API ingest from core banking
- `basic` + `detailed` lookups for partner MFIs

### Phase plan

| Phase | Duration | Activities |
|-------|----------|------------|
| **0 — Design** | 2 weeks | Map transactions → `loan_repayment` events; define derivation rules |
| **1 — Identity** | 2 weeks | Bulk resolve `customer_id` → `pti_id`; store binding table |
| **2 — Backfill** | 3 weeks | CSV historical ingest with `ingest_channel: csv` |
| **3 — Dual write** | 4 weeks | New transactions via API; compare scores to legacy batch |
| **4 — Consumer pilot** | 3 weeks | One partner on lookup API; shadow PDF comparison |
| **5 — Cutover** | 1 week | Disable legacy export; legacy DB read-only archive |

### Field mapping

| Legacy column | PTI event field |
|---------------|-----------------|
| `cust_id` | `entity_id` |
| `txn_date` | `occurred_at` |
| `amount` | `payload.amount_minor` |
| `status_code` | `payload.status` (`on_time`, `late`, `default`) |

### Score reconciliation

Accept ≤5% band deviation during dual-write if drivers document differences (e.g., legacy ignored partial payments). Investigate larger gaps before cutover.

### Rollback

Re-enable CSV export from legacy archive if lookup SLA breaches > 4 hours during pilot.

---

## Example 2 — Employment verifier to PTI employment context

### Starting state

- REST API returning boolean `verified` flag
- PDF certificates emailed manually
- No graph or provenance

### Target state

- `employment` context events: `employment_verified`, `employment_ended`
- Institutions consume `detailed` reports with verification hooks

### Migration steps

1. Wrap existing verification workflow to emit PTI events post-verification.
2. Assign `pti_id` at first verification via identity service.
3. Replace boolean API with PTI lookup for institutional clients; maintain boolean endpoint as deprecated wrapper for 6 months (RFC-010).
4. QR on PDF points to `reports.verify`.

### API compatibility layer

```python
def legacy_verified_api(employee_ref):
    report = pti.lookup(pti_id=resolve(employee_ref), contexts=["employment"], tier="basic")
    return {"verified": report.scores["employment"].band >= "B"}
```

Deprecate after clients migrate to native lookup.

---

## Example 3 — Regional credit bureau to federated PTI hub

### Starting state

- Centralized tradeline database
- Batch file exchange with banks (custom format)
- Single monolithic score

### Target state

- Government profile PTI hub
- Banks as consumers; utilities and telcos as producers
- Federated exchange with neighboring country operator

### Phase plan

| Phase | Focus |
|-------|-------|
| **1** | Split monolithic score into context-scoped outcomes (`lending`, `utilities`, `merchant`) |
| **2** | Replace batch files with lookup API; batch deprecated with 12-month sunset |
| **3** | Onboard producers with event catalogues per sector |
| **4** | Implement RFC-006 exchange with accredited neighbor; `manifest_only` profile first |
| **5** | Expand to `evidence_summary` after bilateral audit |

### Tradeline mapping challenges

| Challenge | PTI approach |
|-----------|--------------|
| Joint accounts | Split events per liable party with graph edges |
| Historical disputes | Compensating `*_reversal` events, not deletes |
| Missing context | Assign best-fit primary; document assumptions in migration audit |

### Governance migration

- Map bureau opt-out to PTI consent withdraw
- DSAR automation required for Government profile — build before public launch

---

## Example 4 — E-commerce marketplace hybrid tenant

### Starting state

- Internal seller risk score
- Same team underwrites sellers and ingests order data

### Target state

- Producer role: `merchant` events from order pipeline
- Consumer role: `merchant` + `digital_platform` lookups for underwriting

### Critical requirement

**Separate API credentials and audit namespaces** per RFC-001 even though one company operates both roles.

### Migration

1. Issue `prod_merchant_ingest` and `cons_underwriting` keys.
2. Route order webhooks to ingest plane only.
3. Underwriting UI calls lookup plane only.
4. Policy engine enforces role boundaries in CI integration tests.

---

## Example 5 — Legacy CSV partner network

### Starting state

- Monthly SFTP CSV drops from 40 partners
- Inconsistent column layouts

### Target state

- PTI CSV ingest with per-partner mapping templates
- Normalized event catalogue

### Template approach

```json
{
  "partner_id": "partner_12",
  "mapping_version": "v2",
  "columns": {
    "borrower_ref": "entity_id",
    "payment_date": "occurred_at",
    "paid": { "field": "payload.status", "transform": "on_time_if_true" }
  },
  "event_type": "loan_repayment",
  "context_id": "lending"
}
```

Validate templates in sandbox before production SFTP redirect.

---

## Example 6 — Edge field offices sync migration

### Starting state

- Paper ledgers digitized weekly at branch

### Target state

- Edge nodes at branches; hub aggregation

### Migration

1. Pilot one branch with edge kit; dual-entry paper + edge for 4 weeks.
2. Train staff on offline ingest receipts.
3. Nightly sync monitoring dashboard for conflict rate < 0.1%.
4. Roll out region-by-region; hub remains system of record for verification.

---

## Data quality gates before cutover

| Gate | Threshold |
|------|-----------|
| Identity match rate | ≥ 98% entity bindings without manual review |
| Event schema rejection rate | < 2% after template stabilization |
| Score correlation with legacy | ≥ 90% same band |
| Evidence verify round-trip | 100% pass in sandbox |
| DSAR erasure test | Complete propagation < SLA |

## Communication template

Notify partners and consumers with:

- Cutover date and RFC-010 deprecation headers on legacy endpoints
- New OpenAPI URL
- Context entitlements unchanged/ changed list
- Support window for dual-run period

## Post-migration

- Run full [conformance test](/pti/conformance/conformance-tests) suite within 30 days
- Archive legacy system with read-only access for regulatory retention
- Update conformance statement and pursue certification if required

## Related

- [Build your PTI overview](./index)
- [Implementation examples](./implementation-examples)
- [RFC-010 Versioning](/pti/rfcs/rfc-010-versioning)

---
title: Anti-Patterns
description: PTI anti-patterns — common integration mistakes and their corrective patterns.
sidebar_position: 3
---

# Anti-Patterns

Common mistakes when implementing PTI and the recommended corrective patterns.

## Identity anti-patterns

### ❌ Embedding PII in `pti_id`

**Problem:** Custom identifiers encode phone numbers or national IDs, creating leakage risk and merge failures.

**Fix:** Use registry-allocated opaque `pti_id` values only.

### ❌ Ignoring merge webhooks

**Problem:** Producer continues sending events to merged (retired) `pti_id`.

**Fix:** Subscribe to `identity.merged` and update local mapping tables to survivor ID.

### ❌ Treating low-confidence matches as certain

**Problem:** Auto-decisioning on 0.62 match confidence.

**Fix:** Apply confidence thresholds; require secondary verification for high-impact decisions.

## Ingest anti-patterns

### ❌ Random idempotency keys

**Problem:** Every retry creates duplicate signals.

**Fix:** Deterministic keys per business action (see [Best Practices](./best-practices)).

### ❌ Using ingest timestamp as `occurred_at`

**Problem:** Skews decay and misorders historical backfill.

**Fix:** Always send true activity time; use separate `ingested_at` (server-set).

### ❌ God payload

**Problem:** Entire CRM record stuffed into `payload`, violating minimization.

**Fix:** Emit schema-required fields only; map additional data internally.

### ❌ Context sprawl

**Problem:** Producer enables all contexts "just in case."

**Fix:** Enable only entitled, observed contexts; request catalog additions when needed.

## Consumer anti-patterns

### ❌ Single global score

**Problem:** UI collapses lending, rental, and merchant into one number.

**Fix:** Present **context scores** separately with per-context drivers.

### ❌ Thin data as approval

**Problem:** High band interpreted as "no risk" when `coverage_gaps` is non-empty.

**Fix:** Display gaps prominently; treat thin bands as inconclusive.

### ❌ Stale report reuse

**Problem:** 6-month-old report used for live underwriting without regeneration.

**Fix:** Check `generated_at`; regenerate or reject expired reports.

### ❌ Skipping verification

**Problem:** PDF or JSON accepted without `verify_uri` check.

**Fix:** Call verify endpoint for audit-sensitive workflows.

## Security anti-patterns

### ❌ Shared producer/consumer credentials

**Problem:** One API key used for ingest and lookup; unclear audit trail.

**Fix:** Separate credentials and scopes per role.

### ❌ Disabled TLS verification

**Problem:** MITM exposure in partner integrations.

**Fix:** Always validate certificates in production; use mTLS for high-assurance profiles.

### ❌ Logging full PII

**Problem:** Request bodies with national IDs in application logs.

**Fix:** Log `correlation_id`, `pti_id`, and hashed identifiers only.

## Architecture anti-patterns

### ❌ Bypassing exchange

**Problem:** Producer writes directly to consumer database or shared cache.

**Fix:** All signals flow through Exchange for policy, provenance, and audit.

### ❌ Client-side scoring

**Problem:** Consumer reimplements intelligence engine logic locally.

**Fix:** Consume server-derived scores; use drivers for transparency only.

### ❌ Silent schema drift

**Problem:** Client ignores unknown JSON fields and breaks on new required fields.

**Fix:** Pin `X-PTI-Version`; monitor `/capabilities` deprecations.

## Correction anti-patterns

### ❌ Deleting bad events

**Problem:** Hard delete without audit trail.

**Fix:** Issue `.corrected` or `.retracted` lifecycle events.

## Detection checklist

| Symptom | Likely anti-pattern |
|---------|---------------------|
| Duplicate signals on retry | Random idempotency keys |
| Score jumps after backfill | Wrong `occurred_at` |
| Regulatory challenge on explainability | Global score UI |
| Cross-tenant data leak | Missing tenant filter injection |

## Related pages

- [Best Practices](./best-practices)
- [Governance Specification](/pti/specification/v1.0/governance)
- [Reference Error Codes](/pti/specification/v1.0/reference-error-codes)

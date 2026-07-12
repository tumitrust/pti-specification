---
sidebar_position: 7
title: RFC-006 — Trust Exchange
description: Normative cross-implementation trust exchange for PTI.
---

# RFC-006: Trust Exchange

| Field | Value |
|-------|-------|
| **RFC** | 006 |
| **Title** | Trust Exchange |
| **Status** | Proposed |
| **Depends on** | RFC-001, RFC-004, RFC-010, RFC-012 |
| **Updates** | — |

## Abstract

This document specifies how independent PTI implementations exchange portable trust packages: signed report manifests, selective event exports, and federation trust anchors. Trust Exchange enables multi-operator ecosystems without centralizing all data in one vendor.

## Motivation

National-scale trust infrastructure may require multiple accredited operators — government registries, sectoral bureaus, regional edge nodes. A normative exchange protocol prevents proprietary lock-in and supports verifiable cross-network lookups.

## Background

Exchange differs from ordinary lookup: the **originating** operator attests outcomes; the **receiving** operator verifies signatures and maps foreign PTI-IDs via federation registry. Raw event bulk transfer is discouraged; packaged evidence transfer is the default.

## Terminology

| Term | Definition |
|------|------------|
| **Trust package** | Signed bundle of report manifest and optional evidence attachments |
| **Federation anchor** | Accredited operator public key and policy endpoint |
| **Foreign PTI-ID** | Subject identifier namespaced to originating operator |
| **Exchange profile** | Named ruleset for package contents and verification steps |
| **Bilateral agreement** | Legal and technical contract between two operators |

## Specification

### 1. Exchange principles

1. Packages **MUST** be signed by originating operator keys registered in federation registry.
2. Receiving operators **MUST** verify signature before presenting data to consumers.
3. Raw cross-operator event replication **MUST NOT** occur without explicit bilateral agreement and subject consent.
4. Exchange **MUST** preserve `context_id` semantics per RFC-002 without renaming.

### 2. Trust package format

A trust package **MUST** contain:

```json
{
  "package_version": "pti.exchange.v1",
  "origin_operator_id": "op_rsa_registry",
  "foreign_pti_id": "pti_k9m2@op_rsa_registry",
  "report_manifest": { },
  "trust_evidence": { },
  "signature": {
    "alg": "EdDSA",
    "kid": "op_rsa_registry_2026",
    "value": "…"
  }
}
```

- `report_manifest` **MUST** conform to RFC-012.
- Attachments **MAY** include redacted event summaries, not full producer payloads, unless `exchange_profile: full_evidence` is agreed.

### 3. Federation registry

Operators **MUST** publish:

| Endpoint | Content |
|----------|---------|
| `/.well-known/pti-federation` | Operator metadata, supported profiles |
| `/federation/keys` | JWKS or equivalent for signature verification |
| `/federation/policies` | Context entitlements accepted for inbound packages |

Receiving operators **MUST** cache keys with TTL ≤ 24 hours and support key rotation overlap.

### 4. Identity mapping

- Foreign PTI-IDs **MUST** use format `pti_{local}@{operator_id}`.
- Receiving operators **SHOULD** map to local surrogate IDs for internal graph without merging graphs globally.
- Identity resolution across operators **MUST** follow RFC-011 confidence thresholds; auto-merge **MUST NOT** occur below configured threshold.

### 5. Exchange profiles

| Profile | Contents | Use case |
|---------|----------|----------|
| `manifest_only` | Signed manifest + scores | Low-latency cross-check |
| `evidence_summary` | Manifest + driver/event refs | Institutional audit |
| `full_evidence` | Redacted payloads + manifests | Government accredited exchange |

Profiles **MUST** be declared in bilateral agreements.

### 6. Consumer presentation

When a lookup spans federated sources:

- Response **MUST** label each context slice with `origin_operator_id`.
- Consumers **MUST** see verification status per package (`verified`, `failed`, `stale`).
- Failed verification **MUST** exclude package data from decision fields (fail closed).

### 7. Revocation

- Originating operators **MUST** support package revocation list (PRL) with `manifest_id` and `revoked_at`.
- Receiving operators **MUST** poll PRL at least every 6 hours for government profile.

## Security Considerations

- Operator private keys **MUST** be stored in HSM for government profile.
- Package replay **MUST** be prevented via `generated_at` freshness checks (max age configurable, default 30 days).
- Man-in-the-middle **MUST** be mitigated via mTLS between operator exchange gateways.

## Privacy Considerations

- Cross-border exchange **MUST** document legal basis and adequacy mechanism.
- Subjects **MUST** be notified of federated sharing where required by applicable law.
- Minimum necessary fields per exchange profile **MUST** be enforced at package build time.

## Examples

### Cross-operator merchant verification

1. Operator A generates `manifest_only` package for `merchant` context.
2. Operator B verifies signature against A's federation keys.
3. Operator B serves combined lookup to consumer with `origin_operator_id: op_a` attribution.

### Revoked package

```json
{
  "manifest_id": "evm_22bd",
  "revoked_at": "2026-07-10T12:00:00Z",
  "reason": "subject_deletion"
}
```

## Implementation Notes

- Implement verification as stateless microservice for horizontal scale.
- Log exchange latency separately from local lookup SLA.
- Start with `manifest_only` profile before negotiating `full_evidence`.

## Future Work

- Multilateral federation governance framework
- Standard dispute resolution when signatures disagree
- Real-time package push via webhooks versus pull

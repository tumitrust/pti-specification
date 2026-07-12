---
sidebar_position: 9
title: RFC-008 — Security
description: Normative security requirements for PTI implementations.
---

# RFC-008: Security

| Field | Value |
|-------|-------|
| **RFC** | 008 |
| **Title** | Security |
| **Status** | Proposed |
| **Depends on** | RFC-001, RFC-004, RFC-006 |
| **Updates** | — |

## Abstract

This document specifies the security architecture and requirements for PTI implementations: threat model, authentication, authorization, cryptography, network security, secure development lifecycle, and incident handling.

## Motivation

Trust platforms aggregate sensitive financial, employment, and identity data. A shared security baseline enables institutions to assess operator risk consistently and allows certification without proprietary audits.

## Background

PTI security aligns with cloud-native best practices (OWASP ASVS, NIST CSF) while addressing domain-specific threats: lookup enumeration, producer credential theft, evidence forgery, and cross-tenant data leakage.

## Terminology

| Term | Definition |
|------|------------|
| **Tenant** | Isolated organizational boundary (producer or consumer) |
| **Credential** | API key, OAuth client, or mTLS certificate |
| **Entitlement** | Authorized contexts, tiers, and operations |
| **HSM** | Hardware Security Module for key storage |
| **SOC** | Security Operations Center monitoring |

## Specification

### 1. Threat model

Implementations **MUST** mitigate at minimum:

| Threat | Impact | Required control |
|--------|--------|------------------|
| Credential theft | Unauthorized ingest/lookup | Scoped keys, rotation, anomaly detection |
| Subject enumeration | Mass PII discovery | Rate limits, confidence thresholds |
| Evidence forgery | Fraudulent reports | Signed manifests (RFC-012) |
| Cross-tenant leakage | Data breach | Strict tenant isolation |
| Insider abuse | Unauthorized admin access | RBAC, MFA, audit |
| Replay attacks | Duplicate or stale packages | Timestamps, idempotency, PRL (RFC-006) |

### 2. Authentication

- All public APIs **MUST** use TLS 1.2+ with modern cipher suites.
- Producer and consumer credentials **MUST** be authenticatable via API key or OAuth 2.0 client credentials.
- Federation gateways **MUST** use mTLS between operators.
- Human admin access **MUST** use MFA.

### 3. Authorization

- Authorization **MUST** evaluate tenant, entitled contexts, tiers, and operation type on every request.
- Default stance **MUST** be deny.
- Graph admin and bulk export **MUST** require privileged roles separate from lookup credentials.

### 4. Cryptography

| Data state | Requirement |
|------------|-------------|
| In transit | TLS 1.2+ |
| At rest | AES-256 or equivalent |
| Signatures | Ed25519 or RSA-3072+ for evidence manifests |
| Passwords | Argon2id or bcrypt with per-user salt |

Government profile **MUST** store signing keys in HSM.

### 5. Key management

- API keys **MUST** support rotation without downtime (dual-active window).
- Signing keys **MUST** publish `kid` in JWKS with overlap during rotation.
- Compromised keys **MUST** be revocable within 1 hour operational target.

### 6. Network and infrastructure

- Production workloads **MUST** run in segmented networks.
- Databases **MUST NOT** be internet-routable.
- Implementations **SHOULD** deploy WAF on public lookup endpoints.

### 7. Secure development

- Dependencies **MUST** be scanned for known vulnerabilities in CI.
- Security testing **MUST** include OWASP Top 10 categories relevant to APIs.
- Penetration test **SHOULD** occur annually; government profile **MUST**.

### 8. Logging and monitoring

- Security events **MUST** feed SIEM with alerting on anomaly thresholds.
- Logs **MUST NOT** contain full credentials or prohibited payload categories.

## Security Considerations

This RFC is the primary security document; implementations **MUST** treat missing controls as non-conformant for Enterprise profile and above.

## Privacy Considerations

Security controls support privacy (encryption, access limits) but do not replace RFC-009. Joint implementation **MUST** satisfy both.

## Examples

### Scoped API key claims

```json
{
  "tenant_id": "cons_772",
  "role": "consumer",
  "entitled_contexts": ["lending", "risk_compliance"],
  "allowed_tiers": ["basic", "detailed"]
}
```

### Webhook signature verification

```
expected = HMAC_SHA256(secret, timestamp + "." + body)
```

Reject if `abs(now - timestamp) > 300` seconds.

## Implementation Notes

- Use short-lived tokens derived from long-lived client credentials where possible.
- Separate signing service from application servers.
- Document data residency per deployment region for institution questionnaires.

## Future Work

- Confidential computing profile for scoring workloads
- Post-quantum signature algorithm migration plan
- Bug bounty program guidelines for accredited operators

---
sidebar_position: 4
title: Security Requirements
description: Security implementation requirements for PTI builders.
---

# Security Requirements

Implementation checklist derived from [RFC-008](/pti/rfcs/rfc-008-security). Each item **MUST** be satisfied for the target conformance profile unless marked optional.

## Authentication and access control

| ID | Requirement | Core | Enterprise | Government |
|----|-------------|------|------------|------------|
| SEC-01 | TLS 1.2+ on all public endpoints | ✓ | ✓ | ✓ |
| SEC-02 | Producer and consumer credentials separated | ✓ | ✓ | ✓ |
| SEC-03 | Credentials scoped to entitled contexts | ✓ | ✓ | ✓ |
| SEC-04 | MFA for human admin access | ✓ | ✓ | ✓ |
| SEC-05 | OAuth 2.0 client credentials or API keys with rotation | ✓ | ✓ | ✓ |
| SEC-06 | Privileged graph/export roles separate from lookup | ✓ | ✓ | ✓ |
| SEC-07 | mTLS for federation gateways | — | Optional | ✓ |

### Implementation notes

- Store API key hashes (Argon2id/bcrypt); never store plaintext after issuance.
- Embed entitlements in signed JWT or lookup table refreshed ≤ 5 minutes.
- Default deny in policy engine; explicit allow per tenant.

## Cryptography

| ID | Requirement | Core | Enterprise | Government |
|----|-------------|------|------------|------------|
| SEC-10 | AES-256 at rest for PII databases | ✓ | ✓ | ✓ |
| SEC-11 | Ed25519 or RSA-3072+ for evidence signatures | ✓ | ✓ | ✓ |
| SEC-12 | JCS canonicalization before sign | ✓ | ✓ | ✓ |
| SEC-13 | HSM for signing keys | — | Optional | ✓ |
| SEC-14 | Hashed strong identifiers with salt | ✓ | ✓ | ✓ |

## Application security

| ID | Requirement | Core | Enterprise | Government |
|----|-------------|------|------------|------------|
| SEC-20 | Input validation on all event fields | ✓ | ✓ | ✓ |
| SEC-21 | Prohibited payload scanner (PAN, etc.) | ✓ | ✓ | ✓ |
| SEC-22 | Webhook HMAC verification + replay window | Optional | ✓ | ✓ |
| SEC-23 | Rate limiting on search endpoints | ✓ | ✓ | ✓ |
| SEC-24 | OWASP API Top 10 mitigations documented | ✓ | ✓ | ✓ |
| SEC-25 | Dependency vulnerability scanning in CI | ✓ | ✓ | ✓ |
| SEC-26 | Annual penetration test | — | ✓ | ✓ |

## Infrastructure security

| ID | Requirement | Core | Enterprise | Government |
|----|-------------|------|------------|------------|
| SEC-30 | Database not internet-routable | ✓ | ✓ | ✓ |
| SEC-31 | Network segmentation (public/internal/control) | ✓ | ✓ | ✓ |
| SEC-32 | Secrets in vault/KMS, not config files | ✓ | ✓ | ✓ |
| SEC-33 | WAF on lookup API | Optional | ✓ | ✓ |
| SEC-34 | Multi-AZ or equivalent HA | Optional | ✓ | ✓ |

## Key management

| ID | Requirement | Implementation |
|----|-------------|----------------|
| SEC-40 | API key rotation without downtime | Issue overlapping keys; deprecate old after 30 days |
| SEC-41 | Signing key rotation | Publish new `kid` in JWKS; overlap ≥ 7 days |
| SEC-42 | Compromise response | Revoke within 1 hour; incident per RFC-007 |

## Logging and monitoring

| ID | Requirement |
|----|-------------|
| SEC-50 | Security events to SIEM (Enterprise+) |
| SEC-51 | Alert on auth failure spikes |
| SEC-52 | Alert on bulk search patterns |
| SEC-53 | Logs exclude secrets and full PAN |

## Threat-specific controls

### Subject enumeration

- Per-tenant search rate limits
- Increasing delay on repeated ambiguous searches
- CAPTCHA or step-up auth for high-volume consumers (optional)

### Evidence forgery

- All reports include signed manifest
- Verification endpoint independent of consumer auth
- PRL for revoked manifests

### Cross-tenant leakage

- `tenant_id` on every row and query filter
- Integration tests that attempt cross-tenant read (must fail)

## Security documentation deliverables

For certification, prepare:

1. **Control matrix** — SEC IDs mapped to implementation
2. **Network diagram** — zones and data flows
3. **Key management SOP** — rotation and compromise
4. **Pen test summary** — findings and remediation (Enterprise+)
5. **SBOM** — software bill of materials for core services

## Related

- [RFC-008 Security](/pti/rfcs/rfc-008-security)
- [Privacy requirements](./privacy-requirements)
- [Conformance tests — Category H](/pti/conformance/conformance-tests)

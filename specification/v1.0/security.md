---
title: Security Specification
description: Normative PTI v1.0 security — threat model, controls, cryptography, audit, and incident response.
sidebar_position: 3
---

# Security Specification

This document defines security requirements for PTI v1.0 implementations.

## Normative language

The key words **MUST**, **MUST NOT**, **REQUIRED**, **SHALL**, **SHALL NOT**, **SHOULD**, **SHOULD NOT**, **RECOMMENDED**, **MAY**, and **OPTIONAL** are to be interpreted as described in [RFC 2119](https://datatracker.ietf.org/doc/html/rfc2119).

## Security objectives

PTI implementations **MUST** protect:

1. **Confidentiality** of subject data, credentials, and entitlements.
2. **Integrity** of events, signals, assertions, and lookup outcomes.
3. **Availability** of ingest and lookup services for entitled parties.
4. **Accountability** through tamper-evident audit trails.
5. **Non-repudiation** of producer attestations where contractually required.

## Threat model

### Assets

| Asset | Sensitivity |
|-------|-------------|
| `pti_id` and identity graph | High |
| Trust events and evidence | High |
| Lookup outcomes and explainability | High |
| API credentials and tokens | Critical |
| Entitlement and consent records | Critical |
| Audit logs | High |

### Threat actors

| Actor | Example threats |
|-------|-----------------|
| **External attacker** | Credential theft, injection, enumeration |
| **Malicious producer** | Poisoned signals, context spoofing, replay |
| **Malicious consumer** | Over-collection, unauthorized lookup, data exfiltration |
| **Insider** | Privilege abuse, covert data export |
| **Supply chain** | Compromised connector, weak third-party verification |

Implementations **MUST** document residual risk for each deployment profile.

## Authentication and authorization

All API endpoints **MUST** require authenticated callers except explicitly public verification endpoints (e.g., report QR validation).

- Credential requirements are defined in [Authentication Model](./authentication-model).
- Scope and tenant enforcement are defined in [Authorization Model](./authorization-model).

Service-to-service calls **MUST** use mutually authenticated channels or signed service tokens.

## Transport security

- All production API traffic **MUST** use TLS 1.2 or higher.
- TLS endpoints **SHOULD** support TLS 1.3.
- Certificate validation **MUST NOT** be disabled in production integrations.
- HTTP **MUST NOT** be used for credential-bearing requests outside isolated development sandboxes.

## Data protection

### Encryption at rest

Subject-identifying data, events, evidence, and lookup artifacts **MUST** be encrypted at rest using industry-standard algorithms (e.g., AES-256). Key management **MUST** support rotation without service disruption.

### Encryption in transit

Inter-service replication and backup streams **MUST** use encrypted channels equivalent to external API requirements.

### Field-level controls

Implementations **SHOULD** apply field-level encryption or tokenization for highly sensitive attributes (government identifiers, biometrics references, financial account tokens).

## Input validation and abuse resistance

| Control | Requirement |
|---------|-------------|
| **Schema validation** | All ingest payloads **MUST** be validated against versioned schemas before persistence. |
| **Idempotency** | Duplicate event submission **MUST NOT** double-apply signal effects. |
| **Rate limiting** | Public and partner endpoints **MUST** enforce per-tenant rate limits. |
| **Payload size limits** | Implementations **MUST** reject oversize payloads with standard error codes. |
| **Injection defenses** | Query and template layers **MUST** parameterize inputs. |

## Cryptographic signing

Where profiles require signed assertions:

- Signing keys **MUST** be stored in hardware-backed or HSM-equivalent systems for production.
- Signature algorithms **SHOULD** use Ed25519 or ECDSA P-256 minimum.
- Verifiers **MUST** validate `kid`, algorithm, and expiration before trusting assertions.

## Audit logging

Audit logs **MUST** capture:

- Authentication successes and failures
- Authorization denials
- Event ingest accept/reject decisions
- Lookup requests with `context_id`, tier, and subject reference (not necessarily full PII)
- Administrative entitlement changes
- Data-subject rights requests and outcomes

Audit records **MUST** include `correlation_id`, actor, timestamp, and source IP or service identity. Logs **MUST** be append-only from application perspective and **SHOULD** replicate to immutable storage.

## Tenant isolation

Multi-tenant deployments **MUST** enforce logical isolation at:

- Authentication scope
- Database row-level or schema-level partition
- Cache key namespace
- Background job queue segregation

Cross-tenant data access **MUST NOT** occur except through governed federation profiles.

## Incident response

Operators **MUST** maintain:

1. Documented incident classification and escalation paths.
2. Credential revocation procedures with maximum 15-minute propagation for compromised keys.
3. Breach notification workflows aligned with [Privacy Specification](./privacy).
4. Post-incident review capturing root cause and control gaps.

## Secure development lifecycle

- Dependencies **SHOULD** be scanned continuously for known vulnerabilities.
- Production secrets **MUST NOT** be committed to source control.
- Penetration testing **SHOULD** occur at least annually for internet-facing deployments.

## Compliance mappings

Implementations **MAY** map controls to ISO 27001, SOC 2, and regional financial/regulatory frameworks. Such mappings are informative and **MUST NOT** alter normative requirements in this specification.

## Related documents

- [Authentication Model](./authentication-model)
- [Authorization Model](./authorization-model)
- [Governance Specification](./governance)
- [Privacy Specification](./privacy)

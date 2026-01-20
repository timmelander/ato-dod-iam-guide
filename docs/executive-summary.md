# ATO Executive Summary

**Authentication and Identity Management Assessment**
**Oracle IAM Identity Domains — OCI DoD Realm (IL4 / IL5)**

---

**System:** Oracle Cloud Infrastructure (OCI) DoD Realm
**Service Evaluated:** Oracle IAM Identity Domains
**Impact Levels:** IL4 (CUI), IL5 (CUI / NSS)
**Assessment Date:** January 2026
**Purpose:** Determine suitability of Oracle IAM Identity Domains to satisfy DoD authentication, MFA, and federation requirements for Authority to Operate (ATO)

---

## 1. Assessment Objective

This assessment evaluates whether **Oracle IAM Identity Domains**, when deployed within an **OCI DoD Realm tenancy**, can satisfy Department of Defense (DoD) authentication, multi-factor authentication (MFA), federation, and session management requirements for web applications operating at **IL4 and IL5**, in accordance with:

<a id="applicable-standards"></a>

| Standard | Description | Reference |
|----------|-------------|-----------|
| DoDI 8520.03 | Identification and Authentication | [Authentication Standards](authentication-standards.md#governing-policy) |
| DISA Cloud Computing SRG v1r5 | Cloud security requirements by IL | [Session Timeout Reference](appendices.md#appendix-d-session-timeout-reference) |
| NIST SP 800-63-4 | Digital Identity Guidelines (AAL/FAL) | [AAL Requirements](authentication-standards.md#2-authentication-assurance-level-aal-requirements) |
| NIST SP 800-53 Rev. 5 | Security Controls (IA, AC, AU) | [Control Mapping](appendices.md#appendix-a-nist-sp-800-53-rev-5-control-mapping) |
| DoD CIO MFA Policy (2024) | MFA requirements | [MFA Compliance Matrix](appendices.md#appendix-e-mfa-factor-compliance-matrix) |

---

## 2. Authorization Boundary and Inheritance

Oracle IAM Identity Domains operates **within the OCI DoD Realm authorization boundary**, which holds:

- **FedRAMP High JAB P-ATO**
- **DISA Provisional Authorization at IL4, IL5, and IL6**

**Inherited from Oracle (CSP):**
- Infrastructure security controls
- Cryptographic protections
- Platform availability and integrity
- Native audit logging via OCI Audit Service

**Customer Responsibility:** (see [Responsibility Matrix](appendices.md#appendix-b-customer-vs-oracle-responsibility-matrix))
- Authentication policy configuration
- MFA enforcement and factor selection
- Federation trust establishment
- Session timeout enforcement
- Audit log forwarding and retention
- User lifecycle management

---

## 3. Authentication Architecture

### Primary Mechanism: Federated CAC/PIV Authentication

- Users authenticate using **DoD PKI (CAC/PIV)** at a DoD-managed IdP
- Oracle IAM Identity Domains consumes **SAML 2.0 assertions**
- Certificate validation, revocation checking (OCSP/CRL), and identity proofing are performed **outside OCI** by the authoritative DoD IdP
- CAC/PIV authentication satisfies MFA requirements through **possession (card)** and **knowledge (PIN)**

This model aligns with [DoDI 8520.03 requirements](authentication-standards.md#31-all-dod-web-applications) and [DISA SRG session controls](#session-controls).

For detailed configuration steps, see the [Implementation Guide](implementation-guide.md#1-primary-authentication-mechanism).

---

<a id="mfa-compliance"></a>

## 4. MFA and Assurance Level Compliance

| Area | Determination | Reference |
|------|---------------|-----------|
| **Minimum AAL (IL4)** | AAL2 — Met | [AAL Requirements](authentication-standards.md#2-authentication-assurance-level-aal-requirements) |
| **Minimum AAL (IL5)** | AAL2 (AAL3 for privileged/NSS) — Met | [AAL Requirements](authentication-standards.md#2-authentication-assurance-level-aal-requirements) |
| **Privileged Access** | Phishing-resistant MFA or CAC/PIV required | [MFA Configuration](implementation-guide.md#2-multi-factor-authentication-mfa) |
| **Non-PKI MFA (Exception-based)** | FIDO2 hardware authenticators only | [MFA Compliance Matrix](appendices.md#appendix-e-mfa-factor-compliance-matrix) |
| **Prohibited Factors** | SMS, email OTP, voice OTP — Disabled | [MFA Compliance Matrix](appendices.md#appendix-e-mfa-factor-compliance-matrix) |

**Password-only authentication is disabled for all IL4/IL5 access.**

---

<a id="federation-and-session"></a>

## 5. Federation and Session Security

### Federation Controls

| Control | Configuration | Reference |
|---------|---------------|-----------|
| Federation Assurance Level | FAL2 minimum (FAL3 for NSS at IL5) | [FAL Reference](appendices.md#appendix-f-federation-assurance-level-fal-reference) |
| Assertion Security | Signed and encrypted | [SAML Requirements](implementation-guide.md#31-saml-20-requirements) |
| Assertion Lifetime | ≤5 minutes | [Federation Standards](authentication-standards.md#5-federation-requirements) |
| AuthnContext Enforcement | Certificate-based authentication required | [AuthnContext Classes](appendices.md#f1-saml-authncontext-classes) |
| Replay Protection | Single-use assertions, short-lived tokens | [Token Controls](implementation-guide.md#53-token-configuration) |

<a id="session-controls"></a>

### Session Controls (DISA SRG-Compliant)

Per [DISA Cloud Computing SRG v1r5](appendices.md#appendix-d-session-timeout-reference):

| Parameter | IL4 | IL5 | Control Mapping |
|-----------|-----|-----|-----------------|
| Idle timeout | ≤15 minutes | ≤15 minutes | [AC-11](appendices.md#a2-access-control-ac-family) |
| Absolute session duration | ≤12 hours | ≤8 hours | [AC-12](appendices.md#a2-access-control-ac-family) |
| Privileged reauthentication | Required | Required | [IA-2(1)](appendices.md#a1-identification-and-authentication-ia-family) |
| Remember device / trusted devices | Disabled | Disabled | [AC-11](appendices.md#a2-access-control-ac-family) |

For configuration steps, see [Session Timeout Configuration](implementation-guide.md#51-session-timeout-configuration).

---

<a id="auditing"></a>

## 6. Auditing and Monitoring

Authentication and MFA events are automatically captured by **OCI Audit**, including:

- Authentication successes and failures
- MFA challenges and validation
- Session creation and termination
- Privilege and policy changes

See [Authentication Event Types](appendices.md#appendix-c-authentication-event-types) for complete event list.

Audit logs are forwarded to a **DoD-approved SIEM** via OCI Logging and Service Connector Hub to meet:

| Control | Description | Reference |
|---------|-------------|-----------|
| AU-2 | Audit Events | [Control Mapping](appendices.md#a3-audit-and-accountability-au-family) |
| AU-3 | Content of Audit Records | [Control Mapping](appendices.md#a3-audit-and-accountability-au-family) |
| AU-6 | Audit Review and Analysis | [Control Mapping](appendices.md#a3-audit-and-accountability-au-family) |
| AU-12 | Audit Generation | [Control Mapping](appendices.md#a3-audit-and-accountability-au-family) |

**Retention:** One-year online, long-term archival per [AU-11 requirements](appendices.md#a3-audit-and-accountability-au-family).

For SIEM integration steps, see [SIEM Integration Architecture](implementation-guide.md#62-siem-integration-architecture).

---

## 7. ATO Determination

### Finding

**Oracle IAM Identity Domains is ATO-supportable for IL4 and IL5 DoD workloads** when configured as documented and operated within an OCI DoD Realm tenancy.

<a id="conditions-for-approval"></a>

### Conditions for Approval

| # | Condition | Values/Details | Status |
|---|-----------|----------------|--------|
| 1 | Federation with DoD-approved IdP performing CAC/PIV authentication | [Federation Configuration](implementation-guide.md#12-saml-20-federation-configuration) | Required |
| 2 | Phishing-resistant MFA enforced for all privileged access | FIDO2 or CAC/PIV — see [MFA Compliance](#mfa-compliance) | Required |
| 3 | Non-compliant MFA factors disabled | SMS, email, voice OTP — see [Prohibited Methods](authentication-standards.md#43-prohibited-methods) | Required |
| 4 | Session timeouts configured per DISA SRG | Idle ≤15 min, Absolute ≤12h (IL4) / ≤8h (IL5) — see [Session Controls](#session-controls) | Required |
| 5 | Authentication audit logs forwarded to approved SIEM | [SIEM Integration](implementation-guide.md#62-siem-integration-architecture) | Required |
| 6 | Password-only authentication disabled | [Sign-On Policy Configuration](implementation-guide.md#23-sign-on-policy-configuration) | Required |

When these conditions are met, Oracle IAM Identity Domains fully satisfies DoD authentication, MFA, federation, and auditing requirements for ATO at IL4 and IL5.

---

**Prepared for inclusion in System Security Plan (SSP) and ATO Review Package**

---

## Related Documents

- [DoD Authentication Standards](authentication-standards.md) — Policy requirements and mandatory controls
- [OCI IAM Assessment](oci-iam-assessment.md) — Platform suitability evaluation
- [Implementation Guide](implementation-guide.md) — Step-by-step configuration guidance
- [Control Mappings and Appendices](appendices.md) — NIST 800-53 mappings, reference tables, glossary

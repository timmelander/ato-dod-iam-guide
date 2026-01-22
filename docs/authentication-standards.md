# DoD-Approved Authentication Standards for Web Applications

**Applicable Environment:** OCI DoD Realm (IL4/IL5/IL6) and Nonfederal CUI Systems
**Purpose:** SSP, Security Control Implementation Statements, ATO Review Package, CMMC Assessment
**Last Updated:** January 2026

---

> **CUI and CMMC Applicability:** Organizations handling Controlled Unclassified Information (CUI) under DFARS 252.204-7012 must also comply with NIST SP 800-171 Rev 3. These requirements align with but do not replace the standards below. For NIST 800-171 control mapping and CMMC Level 2 alignment, see [NIST 800-171 Gap Analysis](nist-800-171-gap-analysis.md).

---

<a id="governing-policy"></a>
<a id="1-governing-policy-and-standards-framework"></a>

## 1. Governing Policy and Standards Framework

| Document | Applicability | Reference |
|----------|---------------|-----------|
| **CNSSI 1253** | Security categorization for National Security Systems | — |
| **DISA Cloud Computing SRG v1r5** | Cloud-specific authentication requirements by Impact Level | [Session Thresholds](appendices.md#appendix-d-session-timeout-reference) |
| **DoD CIO MFA Policy (2024)** | Non-PKI MFA approval and implementation requirements | [MFA Matrix](appendices.md#appendix-e-mfa-factor-compliance-matrix) |
| **DoD ICAM Federation Framework** | Federation practices and trust relationships | [FAL Reference](appendices.md#appendix-f-federation-assurance-level-fal-reference) |
| **DoDI 8520.02** | PKI and Public Key Enabling policies | [DoD Issuances](https://www.esd.whs.mil/Directives/) |
| **DoDI 8520.03** | Primary authentication instruction for DoD information systems | [DoD Issuances](https://www.esd.whs.mil/Directives/) |
| **FIPS 201-3** | PIV credential and authentication requirements | — |
| **NIST SP 800-53 Rev 5** | Security controls baseline (IA family) | [Control Mapping](appendices.md#appendix-a-nist-sp-800-53-rev-5-control-mapping) |
| **NIST SP 800-63 Rev 4** | Digital identity guidelines (IAL, AAL, FAL) | [NIST Publications](https://pages.nist.gov/800-63-4/) |
| **NIST SP 800-171 Rev 3** | CUI protection for nonfederal systems (DFARS, CMMC) | [Gap Analysis](nist-800-171-gap-analysis.md) |

---

<a id="2-authentication-assurance-level-aal-requirements"></a>

## 2. Authentication Assurance Level (AAL) Requirements

Per NIST SP 800-63B-4 and DoDI 8520.03, DoD web applications must meet the following minimum assurance levels:

| Impact Level | Minimum AAL | Primary Method | Acceptable Alternatives |
|--------------|-------------|----------------|------------------------|
| **IL4** (CUI) | AAL2 | DoD PKI (CAC/PIV) | DoD-approved non-PKI MFA with compensating controls |
| **IL5** (CUI/NSS) | AAL2/AAL3* | DoD PKI (CAC/PIV) | DoD-approved non-PKI MFA (restricted scenarios) |
| **IL6** (Classified) | AAL3 | DoD PKI (CAC/PIV) | Hardware token MFA only |

*NSS data at IL5 typically requires AAL3. **Privileged administrative access in IL5 environments SHALL meet AAL3** through CAC/PIV or hardware-backed phishing-resistant MFA.

For approved MFA methods by Impact Level, see [MFA Factor Compliance Matrix](appendices.md#appendix-e-mfa-factor-compliance-matrix).

---

<a id="3-mandatory-requirements"></a>

## 3. Mandatory Requirements

<a id="31-all-dod-web-applications"></a>

### 3.1 All DoD Web Applications

The following NIST SP 800-53 Rev 5 controls are mandatory (see [Control Mapping](appendices.md#a1-identification-and-authentication-ia-family) for OCI IAM implementation):

| Control | Requirement |
|---------|-------------|
| **IA-2** | Uniquely identify and authenticate organizational users |
| **IA-2(1)** | Multi-factor authentication required for all privileged access |
| **IA-2(2)** | MFA required for network access to non-privileged accounts |
| **IA-2(6)** | One authentication factor provided by device separate from system gaining access |
| **IA-2(8)** | Replay-resistant authentication mechanisms |
| **IA-2(12)** | Accept and verify PIV credentials |

<a id="32-authentication-strength"></a>

### 3.2 Authentication Strength (DoDI 8520.03 Credential Strength D)

In order of preference:

1. PKI certificate-based authentication (CAC/PIV) — **Required default**
2. Hardware token + memorized secret
3. Phishing-resistant MFA (FIDO2/WebAuthn) where PKI unavailable — see [FIDO2 Configuration](implementation-guide.md#22-fido2-configuration)

**Password-only authentication is prohibited for all IL4/IL5/IL6 environments.**

<a id="33-cacpiv-authentication"></a>

### 3.3 CAC/PIV Authentication

CAC/PIV authentication satisfies MFA requirements through:
- **Possession factor:** Physical smart card
- **Knowledge factor:** PIN entry

For implementation, see [Federated CAC/PIV Authentication](implementation-guide.md#11-recommended-architecture-federated-cacpiv-authentication).

---

<a id="4-conditional-requirements"></a>

## 4. Conditional Requirements

<a id="41-when-pki-is-not-available"></a>

### 4.1 When PKI Is Not Available

Per DoD CIO MFA Policy (2024), non-PKI MFA is permitted when:

1. Cloud Service Offering (CSO) holds appropriate DISA Provisional Authorization or FedRAMP certification
2. Non-PKI MFA capability integrated with DoD-approved ICAM service provider
3. MFA solution appears on DoD-approved non-PKI MFA list
4. Documented waiver/exception per DoDI 8520.03 paragraph 3.3.b

<a id="42-acceptable-non-pki-mfa-methods"></a>

### 4.2 Acceptable Non-PKI MFA Methods (IL4/IL5 Non-Privileged Access)

| Method | IL4 | IL5 | Notes |
|--------|-----|-----|-------|
| FIDO2/WebAuthn hardware authenticators | Approved | Approved | Phishing-resistant, preferred |
| TOTP hardware tokens (OATH-compliant) | Conditional | Conditional | Requires hardware binding |
| Mobile push with cryptographic binding | Limited | Not Recommended | Restricted scenarios only |

For complete matrix, see [MFA Factor Compliance Matrix](appendices.md#appendix-e-mfa-factor-compliance-matrix).

<a id="43-prohibited-methods"></a>

### 4.3 Prohibited Methods

The following are **prohibited at IL4 and above**:

- SMS-based OTP
- Email-based OTP
- Voice call OTP
- Software-only tokens without hardware binding (IL5+)

<a id="44-nist-800-171-cui-requirements"></a>

### 4.4 NIST 800-171 CUI-Specific Requirements

For systems processing CUI under DFARS 252.204-7012, the following DoD Organization-Defined Parameters (ODPs) apply:

| Requirement | DoD ODP Value | NIST 800-171 Control | Implementation |
|-------------|---------------|---------------------|----------------|
| MFA for privileged access | Required | 03.05.03 | [MFA Configuration](implementation-guide.md#2-multi-factor-authentication-mfa) |
| MFA for network access | Required | 03.05.03 | Sign-on policy enforcement |
| Minimum password length | **16 characters** | 03.05.09 | [Password Policy](implementation-guide.md#45-password-policy-configuration) |
| Inactive account disable | **90 days** | 03.01.01 | [Deprovisioning](implementation-guide.md#43-deprovisioning) |
| Identifier reuse prevention | **10 years** | 03.05.05 | [Identifier Management](implementation-guide.md#44-identifier-management) |
| Device lock timeout | **≤15 minutes** | 03.01.10 | [Session Configuration](implementation-guide.md#51-session-timeout-configuration) |

**Note:** These values are mandatory for CMMC Level 2 certification. For complete mapping, see [DoD ODP Reference](nist-800-171-gap-analysis.md#appendix-h-nist-800-171-dod-odp-reference).

---

<a id="5-federation-requirements"></a>

## 5. Federation Requirements

Per NIST SP 800-63C and DoDI 8520.03:

| FAL | Requirement | DoD Application |
|-----|-------------|-----------------|
| **FAL1** | Bearer assertion, signed | Minimum for IL2 only |
| **FAL2** | Encrypted assertion OR back-channel presentation | **Required for IL4+** |
| **FAL3** | Holder-of-key assertion with cryptographic authenticator proof | **Required for NSS at IL5+** |

For detailed FAL definitions, see [FAL Reference](appendices.md#appendix-f-federation-assurance-level-fal-reference).

<a id="51-federation-protocol-requirements"></a>

### 5.1 Federation Protocol Requirements

- SAML 2.0 or OpenID Connect (OIDC)
- Assertions must be signed using DoD-approved PKI
- Assertions must be encrypted (FAL2 compliance)
- Authentication Context Class Reference enforcement — see [AuthnContext Classes](appendices.md#f1-saml-authncontext-classes)
- Assertion lifetime ≤5 minutes
- Single-use assertion enforcement
- Back-channel token resolution preferred

For OCI IAM configuration, see [SAML 2.0 Requirements](implementation-guide.md#31-saml-20-requirements).

---

<a id="6-identity-assurance-level-ial-requirements"></a>

## 6. Identity Assurance Level (IAL) Requirements

Per NIST SP 800-63A:

| Impact Level | Minimum IAL | Identity Proofing Method |
|--------------|-------------|-------------------------|
| **IL4** | IAL2 | Remote or in-person proofing with evidence validation |
| **IL5** | IAL2/IAL3* | In-person proofing for NSS |
| **IL6** | IAL3 | In-person proofing required |

*IAL3 conditional for IL5 systems with national security implications.

For DoD personnel, IAL3 is typically inherited through the PIV issuance process per FIPS 201-3.

For implementation guidance, see [Identity Proofing Inheritance](implementation-guide.md#41-identity-proofing-inheritance).

---

<a id="7-session-management-requirements"></a>

## 7. Session Management Requirements

Per NIST SP 800-63B-4 and [DISA SRG](appendices.md#d1-disa-srg-requirements):

| Control | IL4 Requirement | IL5 Requirement | Control Mapping |
|---------|-----------------|-----------------|-----------------|
| Session timeout (idle) | ≤15 minutes | ≤15 minutes | [AC-11](appendices.md#a2-access-control-ac-family) |
| Session timeout (absolute) | ≤12 hours | ≤8 hours | [AC-12](appendices.md#a2-access-control-ac-family) |
| Reauthentication | Every 12 hours | Every 12 hours (or per session for privileged) | [IA-2(1)](appendices.md#a1-identification-and-authentication-ia-family) |
| Token binding | Recommended | Required for privileged access | [SC-23](appendices.md#a4-system-and-communications-protection-sc-family) |
| Replay protection | Required | Required | [IA-2(8)](appendices.md#a1-identification-and-authentication-ia-family) |
| Continuous/adaptive authentication | Optional | Conditional for high-risk sessions | — |

For OCI IAM configuration values, see [Session Timeout Configuration](implementation-guide.md#51-session-timeout-configuration).

<a id="71-token-binding-clarification"></a>

### 7.1 Token Binding Clarification

Token binding requirements are satisfied through:
- Proof-of-possession authenticators (FIDO2)
- Short-lived tokens with rotation
- Cryptographic session binding

Note: TLS Token Binding (RFC 8471) is not widely implemented. Equivalent protections are achieved through the mechanisms listed above.

---

<a id="8-ato-assessor-expectations"></a>

## 8. ATO and CMMC Assessor Expectations

### 8.1 ATO Assessment Verification

Assessors will verify:

| # | Verification Item | Evidence |
|---|-------------------|----------|
| 1 | Evidence of PKI integration | Federation configuration, SAML metadata — [Implementation Guide](implementation-guide.md#12-saml-20-federation-configuration) |
| 2 | Sign-on policy configurations | Policy export showing MFA enforcement — [Sign-On Policy](implementation-guide.md#23-sign-on-policy-configuration) |
| 3 | Federation trust documentation | SAML metadata exchange records — [Trust Documentation](implementation-guide.md#32-trust-relationship-documentation) |
| 4 | Session timeout configurations | Settings screenshots meeting [DISA SRG thresholds](appendices.md#appendix-d-session-timeout-reference) |
| 5 | Audit log samples | Authentication event capture — [Event Types](appendices.md#appendix-c-authentication-event-types) |
| 6 | Authentication flow diagrams | Credential validation path |
| 7 | Inheritance statements | CSP vs customer responsibility — [Responsibility Matrix](appendices.md#appendix-b-customer-vs-oracle-responsibility-matrix) |
| 8 | Explicit prohibition | Password-only authentication disabled |
| 9 | Disabled non-compliant factors | SMS, email, voice OTP disabled — [MFA Matrix](appendices.md#appendix-e-mfa-factor-compliance-matrix) |

### 8.2 CMMC Level 2 Additional Verification

For organizations subject to CMMC, assessors will also verify:

| # | Verification Item | DoD ODP | Evidence |
|---|-------------------|---------|----------|
| 10 | Password policy compliance | 16+ characters | [Password Policy](implementation-guide.md#45-password-policy-configuration) |
| 11 | Inactive account management | 90-day disable | [Deprovisioning](implementation-guide.md#43-deprovisioning) |
| 12 | Identifier reuse prevention | 10-year retention | [Identifier Management](implementation-guide.md#44-identifier-management) |
| 13 | Account lifecycle documentation | Documented process | SCIM configuration, provisioning procedures |

For complete ATO and CMMC evidence checklist, see [Implementation Guide: ATO Evidence Checklist](implementation-guide.md#64-ato-evidence-checklist).

---

## Related Documents

- [OCI IAM Identity Domains Assessment](oci-iam-assessment.md) — Platform suitability evaluation
- [Implementation Guide](implementation-guide.md) — Step-by-step configuration guidance
- [Executive Summary](executive-summary.md) — One-page ATO determination
- [Control Mappings and Appendices](appendices.md) — NIST 800-53 mappings, reference tables
- [NIST 800-171 Gap Analysis](nist-800-171-gap-analysis.md) — CUI protection compliance assessment

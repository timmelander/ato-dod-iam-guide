# OCI IAM Identity Domains Implementation Guide

**Applicable Environment:** OCI DoD Realm (IL4/IL5) and Nonfederal CUI Systems
**Purpose:** Configuration guidance for ATO compliance, NIST 800-171, and CMMC
**Last Updated:** January 2026

---

> **NIST 800-171 / CMMC Note:** This guide addresses both DoD ATO requirements (DISA SRG) and NIST SP 800-171 Rev 3 requirements for CUI protection. Where DoD Organization-Defined Parameters (ODPs) specify values, those are indicated. DISA SRG requirements may be more restrictive than 800-171 baseline — always apply the stricter requirement. See [NIST 800-171 Gap Analysis](nist-800-171-gap-analysis.md) for detailed control mapping.

---

<a id="1-primary-authentication-mechanism"></a>

## 1. Primary Authentication Mechanism

<a id="11-recommended-architecture-federated-cacpiv-authentication"></a>

### 1.1 Recommended Architecture: Federated CAC/PIV Authentication

```
[User with CAC] → [DoD Enterprise IdP] → [SAML Assertion] → [OCI IAM Identity Domain]
                         ↓
              [Certificate Validation]
              [PKI Chain Verification]
              [CRL/OCSP Check]
```

This architecture ensures:
- Certificate validation occurs at the DoD-managed IdP
- PKI chain verification against DoD Root CA hierarchy
- Revocation checking via CRL/OCSP
- Identity proofing (IAL3) inherited from PIV issuance process — see [IAL Requirements](authentication-standards.md#6-identity-assurance-level-ial-requirements)

<a id="12-saml-20-federation-configuration"></a>

### 1.2 SAML 2.0 Federation Configuration

**Navigation:** Identity & Security → Domains → [Domain] → Security → Identity Providers

1. **Add SAML IdP:**
   - Import DoD enterprise IdP metadata
   - Configure entity ID and SSO endpoints

2. **Enable Force Authentication:**
   - Ensures fresh authentication per session
   - Prevents assertion reuse

3. **Set Authentication Context Class Reference:**
   - Require certificate-based authentication (see [AuthnContext Classes](appendices.md#f1-saml-authncontext-classes)):
     - `urn:oasis:names:tc:SAML:2.0:ac:classes:X509`
     - `urn:oasis:names:tc:SAML:2.0:ac:classes:SmartcardPKI`

4. **Enable Assertion Encryption:**
   - Required for [FAL2 compliance](appendices.md#appendix-f-federation-assurance-level-fal-reference)
   - Configure IdP to encrypt assertions using OCI IAM's public encryption key

5. **Configure Group Mapping:**
   - Map DoD IdP groups to OCI IAM groups
   - Ensure privileged administrative groups require separate authentication context

**Control Mapping:** [IA-2, IA-2(12), IA-8(1)](appendices.md#a1-identification-and-authentication-ia-family)

<a id="13-alternative-x509-direct-authentication"></a>

### 1.3 Alternative: X.509 Direct Authentication (Limited Use Cases)

For scenarios requiring direct certificate authentication:

**Navigation:** Identity & Security → Domains → [Domain] → Security → Identity Providers → Add X.509 IdP

1. Configure trusted CA certificates (DoD Root CA hierarchy)
2. Enable OCSP or CRL checking for certificate revocation validation
3. Map certificate attributes to user identity

**Limitation:** Federation through DoD IdP is preferred as it provides complete PKI validation within the DoD trust framework.

---

<a id="2-multi-factor-authentication-mfa"></a>

## 2. Multi-Factor Authentication (MFA)

<a id="21-factor-configuration"></a>

### 2.1 Factor Configuration

**Navigation:** Domains → [Domain] → Security → MFA

| Factor | IL4 | IL5 | Action |
|--------|-----|-----|--------|
| **FIDO2 Passkey Authenticator** | **Approved** | **Approved** | **Enable — Primary non-PKI option** |
| Mobile App Passcode (TOTP) | Conditional | Conditional | Enable only with hardware-bound app |
| Mobile App Notification | Conditional | Not Recommended | Disable for IL5 |
| Bypass Code | Emergency Only | Emergency Only | Enable with strict administrative controls |
| **SMS** | **Prohibited** | **Prohibited** | **DISABLE** |
| **Email** | **Prohibited** | **Prohibited** | **DISABLE** |
| **Phone Call** | **Prohibited** | **Prohibited** | **DISABLE** |

For complete compliance matrix, see [MFA Factor Compliance Matrix](appendices.md#appendix-e-mfa-factor-compliance-matrix).

**Control Mapping:** [IA-2(1), IA-2(2), IA-2(6)](appendices.md#a1-identification-and-authentication-ia-family)

<a id="22-fido2-configuration"></a>

### 2.2 FIDO2 Configuration

**Navigation:** Domains → [Domain] → Authentication → FIDO

| Setting | Value | Rationale |
|---------|-------|-----------|
| Attestation | Direct | Hardware verification |
| Authenticator Attachment | Cross-platform (privileged), Platform allowed (standard) | Hardware keys required for admin access |
| Resident Key | Required | Enables passwordless |
| User Verification | Required | Ensures local authentication |
| Timeout | 60000 ms | 60-second enrollment window |

**Control Mapping:** [IA-2(6), IA-2(8)](appendices.md#a1-identification-and-authentication-ia-family)

<a id="23-sign-on-policy-configuration"></a>

### 2.3 Sign-On Policy Configuration

**Navigation:** Domains → [Domain] → Security → Sign-on policies

Create policy: **"DoD IL4/IL5 Authentication Policy"**

#### Rule 1: DoD Privileged Access
```
Priority: 1
Conditions:
  - User is member of: [Administrators, Security Admins, etc.]
Factors:
  - Require: FIDO2 Passkey Authenticator
  - Frequency: Every sign-in
  - Enrollment: Required
```

#### Rule 2: DoD Standard Access
```
Priority: 2
Conditions:
  - All users
Factors:
  - Require: FIDO2 Passkey Authenticator OR Mobile App Passcode
  - Frequency: Once per session
  - Enrollment: Required
```

#### Rule 3: Federated Users (External IdP)
```
Priority: 3
Conditions:
  - Identity Provider: [DoD Enterprise IdP]
Actions:
  - MFA: Defer to IdP (CAC/PIV satisfies MFA requirement)
```

**Note:** CAC/PIV satisfies MFA through possession (card) + knowledge (PIN). See [CAC/PIV Authentication](authentication-standards.md#33-cacpiv-authentication).

**Control Mapping:** [IA-2, IA-5(1)](appendices.md#a1-identification-and-authentication-ia-family)

---

<a id="3-federation-and-protocols"></a>

## 3. Federation and Protocols

<a id="31-saml-20-requirements"></a>

### 3.1 SAML 2.0 Requirements

| Configuration | Requirement | OCI IAM Setting |
|---------------|-------------|-----------------|
| Assertion Signing | Required | IdP signs with SHA-256+ |
| Assertion Encryption | Required ([FAL2](appendices.md#appendix-f-federation-assurance-level-fal-reference)) | Enable "Encrypt Assertion" |
| NameID Format | Persistent or Transient | Configure per IdP requirements |
| Single Logout | Recommended | Enable SLO endpoints |
| Assertion Lifetime | ≤5 minutes | Configured at IdP; validated by OCI IAM |
| Single-Use Enforcement | Required | Default behavior |

**Control Mapping:** [IA-2(8), IA-8(1), SC-23](appendices.md#appendix-a-nist-sp-800-53-rev-5-control-mapping)

<a id="32-trust-relationship-documentation"></a>

### 3.2 Trust Relationship Documentation

Maintain the following records for ATO evidence:

- [ ] SAML metadata exchange dates and records
- [ ] Certificate expiration dates and rotation schedule
- [ ] Attribute mapping specifications
- [ ] Authentication context requirements — see [AuthnContext Classes](appendices.md#f1-saml-authncontext-classes)
- [ ] Federation agreement documentation

---

<a id="4-identity-assurance-and-lifecycle-controls"></a>

## 4. Identity Assurance and Lifecycle Controls

<a id="41-identity-proofing-inheritance"></a>

### 4.1 Identity Proofing Inheritance

When federating with DoD IdP:

- Identity proofing (IAL) is inherited from the authoritative identity source
- DoD employees/contractors: **IAL3 via PIV issuance process** per FIPS 201-3
- Document inheritance clearly in SSP control implementation statements

For IAL requirements by Impact Level, see [IAL Requirements](authentication-standards.md#6-identity-assurance-level-ial-requirements).

**Control Mapping:** [IA-12, IA-12(2)](appendices.md#a1-identification-and-authentication-ia-family)

<a id="42-account-provisioning"></a>

### 4.2 Account Provisioning

| Method | Use Case | Configuration |
|--------|----------|---------------|
| **Just-in-Time (JIT) Provisioning** | Standard users | Enable in SAML IdP settings |
| **SCIM Provisioning** | Automated lifecycle | Configure SCIM connector to authoritative source |
| **Manual Provisioning** | Administrative accounts | Create with MFA enrollment required |

**Navigation for JIT:** Identity Providers → [IdP] → JIT Settings → Enable

**Control Mapping:** [IA-4, IA-5](appendices.md#a1-identification-and-authentication-ia-family)

<a id="43-deprovisioning"></a>

### 4.3 Deprovisioning

- Configure account disablement upon IdP assertion absence (for JIT)
- Implement automated deprovisioning via SCIM for authoritative source sync
- Document privileged account review procedures (**quarterly minimum**)
- Implement immediate revocation capability for terminated personnel
- **Disable inactive accounts within 90 days** (DoD ODP for NIST 800-171 03.01.01)

**Control Mapping:** [AC-2](appendices.md#a2-access-control-ac-family), PS-4, [03.01.01](nist-800-171-gap-analysis.md#030101-system-account-management)

<a id="44-identifier-management"></a>

### 4.4 Identifier Management

Per NIST 800-171 control 03.05.05 and DoD ODP requirements:

| Requirement | DoD ODP Value | Implementation |
|-------------|---------------|----------------|
| Identifier reuse prevention | **10 years** | Maintain records of all user identifiers |
| Unique identification | Required | Use enterprise GUID from authoritative source |
| Identifier recycling | Prohibited | Never reassign deleted user identifiers |

**Customer Action Required:**
- Document identifier management policy prohibiting reuse for 10 years
- Maintain audit trail of all created/deleted user identifiers
- Consider using persistent enterprise identifiers (e.g., EDIPI) from authoritative source

**Control Mapping:** [IA-4](appendices.md#a1-identification-and-authentication-ia-family), [03.05.05](nist-800-171-gap-analysis.md#030505-identifier-management)

<a id="45-password-policy-configuration"></a>

### 4.5 Password Policy Configuration

**Navigation:** Domains → [Domain] → Settings → Password policy

Per NIST 800-171 control 03.05.09 and DoD ODP requirements:

| Parameter | DoD ODP Value | OCI IAM Setting |
|-----------|---------------|-----------------|
| Minimum length | **16 characters** | Minimum password length: 16 |
| Password history | **24 generations** (recommended) | Password history count: 24 |
| Complexity | Required | Enable complexity requirements |
| Compromised password check | **Quarterly update** | Customer-managed process |
| Maximum age | Organization-defined | Configure per policy (365 days max recommended) |

**Note:** For DoD environments, password-only authentication is prohibited. Passwords serve as one factor in MFA scenarios. Configure password policies for local administrative accounts and backup authentication.

**Control Mapping:** [IA-5(1)](appendices.md#a1-identification-and-authentication-ia-family), [03.05.09](nist-800-171-gap-analysis.md#030509-password-authentication)

---

<a id="5-session-management-and-token-controls"></a>

## 5. Session Management and Token Controls

<a id="51-session-timeout-configuration"></a>

### 5.1 Session Timeout Configuration

**Navigation:** Domains → [Domain] → Settings → Session settings

| Parameter | NIST 800-171 (DoD ODP) | DISA SRG IL4 | DISA SRG IL5 | Apply Stricter |
|-----------|------------------------|--------------|--------------|----------------|
| Idle Timeout | ≤15 minutes | ≤15 minutes | ≤15 minutes | **15 minutes** |
| Session Duration (Max) | ≤24 hours | ≤12 hours | ≤8 hours | **IL4: 12h / IL5: 8h** |
| Remember Device | — | Disabled | Disabled | **Disabled** |

**Configuration Values:**

| Parameter | IL4 Setting | IL5 Setting |
|-----------|-------------|-------------|
| Session Duration (Max) | 720 minutes (12 hours) | 480 minutes (8 hours) |
| Idle Timeout | 15 minutes | 15 minutes |
| Remember Device | **Disabled** | **Disabled** |

**Note:** NIST 800-171 DoD ODP allows up to 24 hours for session duration, but DISA SRG requires shorter timeouts for IL4/IL5. Always apply the stricter requirement.

**Control Mapping:** [AC-11, AC-12](appendices.md#a2-access-control-ac-family), [SC-10](appendices.md#a4-system-and-communications-protection-sc-family), [03.01.10](nist-800-171-gap-analysis.md#030110-device-lock), [03.13.05](nist-800-171-gap-analysis.md#031305-session-termination)

<a id="52-sign-on-policy-session-controls"></a>

### 5.2 Sign-On Policy Session Controls

Within each sign-on rule:

- Reauthentication: Required every session for privileged access
- Trusted devices: **Disabled** for DoD environments
- Session binding: Enable where available

<a id="53-token-configuration"></a>

### 5.3 Token Configuration

| Parameter | Setting | Rationale |
|-----------|---------|-----------|
| Access token lifetime | ≤1 hour | Limit exposure window |
| Refresh token lifetime | ≤12 hours | Align with session maximum |
| Token revocation on logout | Enabled | Immediate invalidation |
| Refresh token rotation | Enabled | Prevent replay |

**Token Binding Note:** Token binding is satisfied through proof-of-possession authenticators (FIDO2) and short-lived tokens, as OCI IAM does not implement TLS Token Binding (RFC 8471). See [Token Binding Clarification](authentication-standards.md#71-token-binding-clarification).

**Control Mapping:** [AC-12](appendices.md#a2-access-control-ac-family), [IA-2(8)](appendices.md#a1-identification-and-authentication-ia-family), [SC-23](appendices.md#a4-system-and-communications-protection-sc-family)

---

<a id="6-auditing-logging-and-ato-evidence"></a>

## 6. Auditing, Logging, and ATO Evidence

<a id="61-required-authentication-events"></a>

### 6.1 Required Authentication Events

OCI Audit automatically captures (see [Event Types](appendices.md#appendix-c-authentication-event-types) for complete list):

| Event Type | Event ID | Control Mapping |
|------------|----------|-----------------|
| User Login Success | `sso.authentication.success` | [AU-2(a)(1)](appendices.md#a3-audit-and-accountability-au-family) |
| User Login Failure | `sso.authentication.failure` | [AU-2(a)(2)](appendices.md#a3-audit-and-accountability-au-family) |
| MFA Challenge | `mfa.validation.*` | [AU-2(a)(5)](appendices.md#a3-audit-and-accountability-au-family) |
| Session Creation | `session.create` | [AU-2(a)(1)](appendices.md#a3-audit-and-accountability-au-family) |
| Session Termination | `session.terminate` | [AU-2(a)(3)](appendices.md#a3-audit-and-accountability-au-family) |
| Password Change | `user.password.change` | [AU-2(a)(6)](appendices.md#a3-audit-and-accountability-au-family) |
| Account Lockout | `user.account.lock` | [AU-2(a)(4)](appendices.md#a3-audit-and-accountability-au-family) |
| Privilege Escalation | `iam.policy.*` | [AU-2(a)(7)](appendices.md#a3-audit-and-accountability-au-family) |

<a id="62-siem-integration-architecture"></a>

### 6.2 SIEM Integration Architecture

```
[OCI IAM Identity Domain]
         ↓
    [OCI Audit Service]
         ↓
    [OCI Logging]
         ↓
    [Service Connector Hub]
         ↓
    [OCI Streaming (Kafka-compatible)]
         ↓
    [Log Shipper / Native Connector]
         ↓
    [DoD-Approved SIEM]
```

**Implementation Steps:**

1. OCI Audit automatically captures IAM events (no configuration needed)
2. Create Service Connector Hub:
   - **Source:** OCI Audit (_Audit log group)
   - **Filter:** Identity SignOn events
   - **Target:** OCI Streaming or Object Storage
3. Configure SIEM ingestion from Streaming (Kafka protocol) or Object Storage

**Control Mapping:** [AU-6](appendices.md#a3-audit-and-accountability-au-family), SI-4

<a id="63-log-retention-requirements"></a>

### 6.3 Log Retention Requirements

| Log Type | DISA SRG Requirement | OCI Default | Customer Action |
|----------|---------------------|-------------|-----------------|
| Audit Logs | 1 year online, 3 years archived | 365 days | Archive to Object Storage with retention lock |
| Authentication Events | 1 year | 365 days in OCI Audit | Forward to SIEM with extended retention |

**Control Mapping:** [AU-11](appendices.md#a3-audit-and-accountability-au-family)

<a id="64-ato-evidence-checklist"></a>

### 6.4 ATO and CMMC Evidence Checklist

Prepare the following artifacts for ATO assessment and CMMC Level 2 certification (see [ATO Assessor Expectations](authentication-standards.md#8-ato-assessor-expectations)):

**Authentication and MFA:**
- [ ] Sign-on policy export showing MFA enforcement — [Section 2.3](#23-sign-on-policy-configuration)
- [ ] MFA factor configuration showing prohibited factors disabled — [Section 2.1](#21-factor-configuration)
- [ ] FIDO2 configuration for privileged access — [Section 2.2](#22-fido2-configuration)

**Federation:**
- [ ] Identity provider federation configuration screenshots — [Section 1.2](#12-saml-20-federation-configuration)
- [ ] SAML metadata exchange documentation — [Section 3.2](#32-trust-relationship-documentation)
- [ ] Federation agreement and trust documentation — [Section 3.2](#32-trust-relationship-documentation)

**Session Management:**
- [ ] Session timeout configuration evidence — [Section 5.1](#51-session-timeout-configuration)
- [ ] Token lifetime configuration — [Section 5.3](#53-token-configuration)

**Account Lifecycle (NIST 800-171):**
- [ ] User provisioning/deprovisioning procedures — [Section 4.2](#42-account-provisioning), [Section 4.3](#43-deprovisioning)
- [ ] Inactive account disable process (90-day threshold) — [Section 4.3](#43-deprovisioning)
- [ ] Identifier management policy (10-year reuse prevention) — [Section 4.4](#44-identifier-management)
- [ ] Password policy configuration (16+ characters) — [Section 4.5](#45-password-policy-configuration)
- [ ] Privileged access review records (quarterly)

**Audit and Monitoring:**
- [ ] Audit log samples demonstrating event capture — [Section 6.1](#61-required-authentication-events)
- [ ] SIEM integration configuration documentation — [Section 6.2](#62-siem-integration-architecture)
- [ ] Log retention policy documentation — [Section 6.3](#63-log-retention-requirements)

For CMMC-specific mapping, see [CMMC Alignment](nist-800-171-gap-analysis.md#9-cmmc-alignment).

---

## Related Documents

- [DoD Authentication Standards](authentication-standards.md) — Policy requirements and mandatory controls
- [OCI IAM Assessment](oci-iam-assessment.md) — Platform suitability evaluation
- [Executive Summary](executive-summary.md) — One-page ATO determination
- [NIST 800-171 Gap Analysis](nist-800-171-gap-analysis.md) — CUI protection compliance assessment
- [Control Mappings and Appendices](appendices.md) — NIST 800-53 mappings, reference tables

# Appendices: Control Mappings and Reference Tables

**Applicable Environment:** OCI DoD Realm (IL4/IL5) and Nonfederal CUI Systems
**Purpose:** ATO Review Package, CMMC Assessment Supporting Documentation
**Last Updated:** January 2026

---

<a id="appendix-a-nist-sp-800-53-rev-5-control-mapping"></a>

## Appendix A: NIST SP 800-53 Rev 5 Control Mapping

<a id="a1-identification-and-authentication-ia-family"></a>

### A.1 Identification and Authentication (IA) Family

| Control | Control Name | OCI IAM Capability | Configuration Required |
|---------|--------------|-------------------|------------------------|
| IA-2 | Identification and Authentication (Organizational Users) | User authentication | Sign-on policies |
| IA-2(1) | Multi-Factor Authentication to Privileged Accounts | MFA for privileged access | Sign-on rules with MFA required for admin groups |
| IA-2(2) | Multi-Factor Authentication to Non-Privileged Accounts | MFA for all users | Sign-on rules with MFA required for all users |
| IA-2(5) | Individual Authentication with Group Authentication | Unique user identification | Individual accounts, no shared credentials |
| IA-2(6) | Access to Accounts - Separate Device | Out-of-band authenticator | FIDO2 hardware authenticator |
| IA-2(8) | Access to Accounts - Replay Resistant | Replay protection | SAML assertion single-use, short-lived tokens |
| IA-2(12) | Acceptance of PIV Credentials | CAC/PIV authentication | Federation with DoD IdP or X.509 IdP |
| IA-4 | Identifier Management | User identifier lifecycle | SCIM provisioning, JIT provisioning |
| IA-5 | Authenticator Management | Credential management | MFA enrollment, password policies |
| IA-5(1) | Password-Based Authentication | Password requirements | Password policies (when applicable) |
| IA-5(2) | Public Key-Based Authentication | Certificate authentication | X.509 IdP, SAML federation |
| IA-8 | Identification and Authentication (Non-Organizational Users) | External user authentication | Federation, SAML/OIDC |
| IA-8(1) | Acceptance of PIV Credentials from Other Agencies | Cross-agency PIV | Federation trust configuration |
| IA-12 | Identity Proofing | Identity verification | Inherited from DoD IdP |
| IA-12(2) | Identity Proofing - Supervisor Authorization | Verification approval | Inherited from PIV issuance process |

<a id="a2-access-control-ac-family"></a>

### A.2 Access Control (AC) Family

| Control | Control Name | OCI IAM Capability | Configuration Required |
|---------|--------------|-------------------|------------------------|
| AC-2 | Account Management | User lifecycle | SCIM, manual provisioning, deprovisioning |
| AC-2(1) | Account Management - Automated System Account Management | Automated provisioning | SCIM connector to authoritative source |
| AC-2(4) | Account Management - Automated Audit Actions | Account activity logging | OCI Audit (automatic) |
| AC-3 | Access Enforcement | Authorization | IAM policies, group membership |
| AC-7 | Unsuccessful Logon Attempts | Account lockout | Sign-on policy lockout settings |
| AC-8 | System Use Notification | Login banner | Custom login page configuration |
| AC-11 | Device Lock | Session lock | Idle timeout (≤15 minutes) — see [Session Timeout Reference](#appendix-d-session-timeout-reference) |
| AC-12 | Session Termination | Session management | Absolute timeout, logout capability — see [Session Timeout Reference](#appendix-d-session-timeout-reference) |
| AC-12(1) | Session Termination - User-Initiated Logouts | Logout functionality | Native logout, SLO support |

<a id="a3-audit-and-accountability-au-family"></a>

### A.3 Audit and Accountability (AU) Family

| Control | Control Name | OCI IAM Capability | Configuration Required |
|---------|--------------|-------------------|------------------------|
| AU-2 | Audit Events | Event logging | OCI Audit (automatic capture) — see [Event Types](#appendix-c-authentication-event-types) |
| AU-3 | Content of Audit Records | Audit record content | OCI Audit event schema |
| AU-3(1) | Content of Audit Records - Additional Audit Information | Extended audit data | OCI Audit includes user, source, timestamp |
| AU-6 | Audit Record Review, Analysis, and Reporting | Log analysis | SIEM integration |
| AU-6(1) | Audit Record Review - Automated Process Integration | Automated analysis | Service Connector Hub → SIEM |
| AU-9 | Protection of Audit Information | Audit log integrity | OCI Audit tamper protection (inherited) |
| AU-11 | Audit Record Retention | Log retention | Archive to Object Storage with retention lock |
| AU-12 | Audit Record Generation | Audit generation | OCI Audit service (automatic) |

<a id="a4-system-and-communications-protection-sc-family"></a>

### A.4 System and Communications Protection (SC) Family

| Control | Control Name | OCI IAM Capability | Configuration Required |
|---------|--------------|-------------------|------------------------|
| SC-10 | Network Disconnect | Session termination | Session timeout configuration |
| SC-12 | Cryptographic Key Establishment and Management | Key management | Inherited from OCI |
| SC-13 | Cryptographic Protection | Encryption | TLS, assertion encryption (inherited + configured) |
| SC-23 | Session Authenticity | Session integrity | Token signing, SAML signing |

---

<a id="appendix-b-customer-vs-oracle-responsibility-matrix"></a>

## Appendix B: Customer vs Oracle Responsibility Matrix

| Control Area | Oracle (CSP) | Customer | Notes |
|--------------|:------------:|:--------:|-------|
| **Infrastructure** | | | |
| Physical security | ✓ | | Inherited |
| Network security | ✓ | | Inherited |
| Platform availability | ✓ | | SLA-backed |
| **Cryptographic Controls** | | | |
| TLS termination | ✓ | | Inherited |
| Encryption at rest | ✓ | | Inherited |
| Key management | ✓ | | Inherited |
| **IAM Service** | | | |
| Service availability | ✓ | | Inherited |
| Feature functionality | ✓ | | Inherited |
| Default audit capture | ✓ | | Automatic |
| **IAM Configuration** | | | |
| Sign-on policy configuration | | ✓ | Customer responsibility — see [Implementation Guide](implementation-guide.md#23-sign-on-policy-configuration) |
| MFA factor enablement/disablement | | ✓ | Customer responsibility — see [MFA Factor Matrix](#appendix-e-mfa-factor-compliance-matrix) |
| Federation trust establishment | | ✓ | Customer responsibility — see [Implementation Guide](implementation-guide.md#12-saml-20-federation-configuration) |
| Session timeout configuration | | ✓ | Customer responsibility — see [Session Reference](#appendix-d-session-timeout-reference) |
| User provisioning/deprovisioning | | ✓ | Customer responsibility — see [Implementation Guide](implementation-guide.md#42-account-provisioning) |
| Privileged access review | | ✓ | Customer responsibility |
| **Logging and Monitoring** | | | |
| Audit log generation | ✓ | | Automatic |
| Audit log forwarding to SIEM | | ✓ | Customer responsibility — see [Implementation Guide](implementation-guide.md#62-siem-integration-architecture) |
| Extended log retention | | ✓ | Customer responsibility |
| SIEM integration and monitoring | | ✓ | Customer responsibility |
| **Identity Proofing** | | | |
| IAL inheritance from DoD IdP | | ✓ | Document in SSP — see [Authentication Standards](authentication-standards.md#6-identity-assurance-level-ial-requirements) |
| Certificate validation at IdP | | ✓ | Via federated IdP |

---

<a id="appendix-c-authentication-event-types"></a>

## Appendix C: Authentication Event Types

<a id="c1-oci-audit-event-categories"></a>

### C.1 OCI Audit Event Categories

| Event Category | Event ID Pattern | Description |
|----------------|------------------|-------------|
| Authentication Success | `sso.authentication.success` | Successful user login |
| Authentication Failure | `sso.authentication.failure` | Failed login attempt |
| MFA Challenge Issued | `mfa.challenge.*` | MFA prompt sent to user |
| MFA Validation Success | `mfa.validation.success` | MFA factor verified |
| MFA Validation Failure | `mfa.validation.failure` | MFA verification failed |
| Session Created | `session.create` | New session established |
| Session Terminated | `session.terminate` | Session ended (logout or timeout) |
| Password Changed | `user.password.change` | User password modification |
| Account Locked | `user.account.lock` | Account locked due to failed attempts |
| Account Unlocked | `user.account.unlock` | Account unlocked by admin |
| User Created | `user.create` | New user account created |
| User Deleted | `user.delete` | User account removed |
| Group Membership Changed | `group.member.*` | Group membership modification |
| Policy Changed | `iam.policy.*` | IAM policy modification |

<a id="c2-required-audit-events-per-au-2"></a>

### C.2 Required Audit Events per AU-2

| AU-2 Requirement | Event Type | OCI Audit Coverage |
|------------------|------------|-------------------|
| (a)(1) Successful logons | `sso.authentication.success` | ✓ |
| (a)(2) Unsuccessful logons | `sso.authentication.failure` | ✓ |
| (a)(3) Logoffs | `session.terminate` | ✓ |
| (a)(4) Account management | `user.*`, `group.*` | ✓ |
| (a)(5) MFA events | `mfa.*` | ✓ |
| (a)(6) Password changes | `user.password.change` | ✓ |
| (a)(7) Privilege escalation | `iam.policy.*`, `group.member.*` | ✓ |

---

<a id="appendix-d-session-timeout-reference"></a>

## Appendix D: Session Timeout Reference

<a id="d1-disa-srg-requirements"></a>

### D.1 DISA SRG Requirements

Source: DISA Cloud Computing Security Requirements Guide (SRG) v1r5

| Impact Level | Idle Timeout | Absolute Timeout | Reauthentication |
|--------------|--------------|------------------|------------------|
| IL2 | ≤30 minutes | ≤24 hours | Every 24 hours |
| IL4 | ≤15 minutes | ≤12 hours | Every 12 hours |
| IL5 | ≤15 minutes | ≤8 hours | Every 12 hours (per session for privileged) |
| IL6 | ≤15 minutes | ≤8 hours | Per session |

<a id="d2-oci-iam-configuration-values"></a>

### D.2 OCI IAM Configuration Values

| OCI IAM Setting | IL4 Value | IL5 Value | Configuration Path |
|-----------------|-----------|-----------|-------------------|
| Session Duration (minutes) | 720 | 480 | Domains → Settings → Session settings |
| Idle Timeout (minutes) | 15 | 15 | Domains → Settings → Session settings |
| Remember Device | Disabled | Disabled | Domains → Settings → Session settings |
| Trusted Devices | Disabled | Disabled | Sign-on policy rules |

For step-by-step configuration, see [Implementation Guide: Session Timeout Configuration](implementation-guide.md#51-session-timeout-configuration).

---

<a id="appendix-e-mfa-factor-compliance-matrix"></a>

## Appendix E: MFA Factor Compliance Matrix

| Factor Type | IL2 | IL4 | IL5 | IL6 | Notes |
|-------------|:---:|:---:|:---:|:---:|-------|
| CAC/PIV (via federation) | ✓ | ✓ | ✓ | ✓ | Preferred — see [Authentication Architecture](executive-summary.md#3-authentication-architecture) |
| FIDO2/WebAuthn Hardware | ✓ | ✓ | ✓ | ✓ | Phishing-resistant — see [FIDO2 Configuration](implementation-guide.md#22-fido2-configuration) |
| TOTP Hardware Token | ✓ | ✓ | Conditional | ✗ | OATH-compliant required |
| Mobile App TOTP | ✓ | Conditional | Conditional | ✗ | Hardware binding required |
| Mobile Push | ✓ | Conditional | ✗ | ✗ | Not recommended |
| SMS OTP | ✓ | ✗ | ✗ | ✗ | **Prohibited IL4+** |
| Email OTP | ✓ | ✗ | ✗ | ✗ | **Prohibited IL4+** |
| Voice Call | ✓ | ✗ | ✗ | ✗ | **Prohibited IL4+** |
| Password Only | ✗ | ✗ | ✗ | ✗ | **Prohibited all levels** |

**Legend:**
- ✓ = Approved
- ✗ = Prohibited
- Conditional = Requires documented exception or specific configuration

For factor configuration in OCI IAM, see [Implementation Guide: MFA Configuration](implementation-guide.md#21-factor-configuration).

---

<a id="appendix-f-federation-assurance-level-fal-reference"></a>

## Appendix F: Federation Assurance Level (FAL) Reference

Source: NIST SP 800-63C-4

| FAL | Assertion Requirements | Token Handling | DoD Application |
|-----|------------------------|----------------|-----------------|
| FAL1 | Signed | Bearer | IL2 only |
| FAL2 | Signed + Encrypted OR Back-channel | Bearer with protections | **IL4+ required** |
| FAL3 | Signed + Holder-of-key | Cryptographic proof of possession | **NSS at IL5+** |

For detailed federation requirements, see [Authentication Standards: Federation Requirements](authentication-standards.md#5-federation-requirements).

<a id="f1-saml-authncontext-classes"></a>

### F.1 SAML AuthnContext Classes

| Context Class | Authentication Type | Use Case |
|---------------|---------------------|----------|
| `urn:oasis:names:tc:SAML:2.0:ac:classes:X509` | X.509 Certificate | CAC/PIV |
| `urn:oasis:names:tc:SAML:2.0:ac:classes:SmartcardPKI` | Smart Card PKI | CAC/PIV |
| `urn:oasis:names:tc:SAML:2.0:ac:classes:TLSClient` | TLS Client Certificate | Certificate auth |
| `urn:oasis:names:tc:SAML:2.0:ac:classes:MobileTwoFactorContract` | Mobile MFA | Non-PKI MFA |

For configuration, see [Implementation Guide: SAML 2.0 Federation Configuration](implementation-guide.md#12-saml-20-federation-configuration).

---

<a id="appendix-g-nist-sp-800-171-rev-3-control-mapping"></a>

## Appendix G: NIST SP 800-171 Rev 3 Control Mapping

NIST SP 800-171 Rev 3 establishes security requirements for protecting Controlled Unclassified Information (CUI) in nonfederal systems. This mapping shows how OCI IAM Identity Domains supports these requirements.

For detailed gap analysis, see [NIST 800-171 Gap Analysis](nist-800-171-gap-analysis.md).

<a id="g1-access-control-0301"></a>

### G.1 Access Control (03.01)

| Control | Control Name | OCI IAM Capability | Status |
|---------|--------------|-------------------|--------|
| 03.01.01 | System Account Management | SCIM provisioning, user lifecycle | Supported |
| 03.01.02 | Access Enforcement | IAM policies, group membership | Supported |
| 03.01.03 | Information Flow Enforcement | Compartments, network policies | Customer Config |
| 03.01.04 | Separation of Duties | Role-based access, admin groups | Supported |
| 03.01.05 | Least Privilege | Granular IAM policies | Supported |
| 03.01.07 | Privileged Functions | Admin controls, audit logging | Supported |
| 03.01.10 | Device Lock | Session idle timeout (≤15 min) | Supported |
| 03.01.12 | Remote Access | Federation, VPN integration | Supported |
| 03.01.20 | External Systems | Federation trust policies | Supported |

<a id="g2-audit-and-accountability-0303"></a>

### G.2 Audit and Accountability (03.03)

| Control | Control Name | OCI IAM Capability | Status |
|---------|--------------|-------------------|--------|
| 03.03.01 | Event Logging | OCI Audit service | Supported |
| 03.03.02 | Audit Record Content | Comprehensive event schema | Supported |
| 03.03.03 | Audit Record Generation | Automatic capture | Supported |
| 03.03.04 | Audit Record Storage | Object Storage retention | Supported |
| 03.03.05 | Audit Review and Analysis | SIEM integration required | Customer Config |
| 03.03.06 | Audit Reduction and Reporting | SIEM tools | Customer Config |
| 03.03.08 | Protection of Audit Information | OCI Audit tamper protection | Inherited |

<a id="g3-identification-and-authentication-0305"></a>

### G.3 Identification and Authentication (03.05)

| Control | Control Name | OCI IAM Capability | Status |
|---------|--------------|-------------------|--------|
| 03.05.01 | User Identification | Unique user identifiers | Supported |
| 03.05.02 | Device Identification | API keys, instance principals | Supported |
| 03.05.03 | Multi-factor Authentication | FIDO2, TOTP, CAC/PIV federation | Supported |
| 03.05.04 | Replay-Resistant Authentication | SAML assertions, token expiration | Supported |
| 03.05.05 | Identifier Management | 10-year reuse prevention | Process Required |
| 03.05.06 | Authenticator Management | MFA enrollment, credential lifecycle | Supported |
| 03.05.07 | Authenticator Feedback | Masked password entry | Supported |
| 03.05.08 | Cryptographic Module Authentication | FIPS-validated modules | Inherited |
| 03.05.09 | Password-Based Authentication | 16+ char policy (DoD ODP) | Supported |

<a id="g4-system-and-communications-protection-0313"></a>

### G.4 System and Communications Protection (03.13)

| Control | Control Name | OCI IAM Capability | Status |
|---------|--------------|-------------------|--------|
| 03.13.03 | Information at Rest | Encryption at rest | Inherited |
| 03.13.04 | Information in Transit | TLS 1.2+ encryption | Inherited |
| 03.13.05 | Session Termination | Session timeout, logout | Supported |
| 03.13.06 | Network Disconnect | Session termination | Supported |
| 03.13.08 | Transmission Confidentiality | TLS encryption | Inherited |
| 03.13.11 | Cryptographic Protection | FIPS-validated modules | Inherited |

<a id="g5-dod-organization-defined-parameters"></a>

### G.5 DoD Organization-Defined Parameters (ODPs)

| Control | Parameter | DoD Value |
|---------|-----------|-----------|
| 03.01.01 | Inactive account disable | 90 days |
| 03.01.10 | Device lock timeout | ≤15 minutes |
| 03.01.01 | Session logout threshold | ≤24 hours |
| 03.05.05 | Identifier reuse prevention | 10 years |
| 03.05.09 | Minimum password length | 16 characters |
| 03.13.11 | Cryptographic standard | FIPS Validated |

**Reference:** [DoD ODP Values](https://dodcio.defense.gov/Portals/0/Documents/CMMC/OrgDefinedParmsNISTSP800-171.pdf)

---

<a id="appendix-h-glossary"></a>

## Appendix H: Glossary

| Term | Definition |
|------|------------|
| AAL | Authentication Assurance Level per [NIST SP 800-63B](authentication-standards.md#2-authentication-assurance-level-aal-requirements) |
| ATO | Authority to Operate |
| CAC | Common Access Card |
| CMMC | Cybersecurity Maturity Model Certification — DoD cybersecurity standard based on NIST 800-171 |
| CUI | Controlled Unclassified Information — see [NIST 800-171 Gap Analysis](nist-800-171-gap-analysis.md) |
| DFARS | Defense Federal Acquisition Regulation Supplement |
| DIB | Defense Industrial Base |
| FAL | Federation Assurance Level per [NIST SP 800-63C](#appendix-f-federation-assurance-level-fal-reference) |
| IAL | Identity Assurance Level per [NIST SP 800-63A](authentication-standards.md#6-identity-assurance-level-ial-requirements) |
| ICAM | Identity, Credential, and Access Management |
| IdP | Identity Provider |
| IL | Impact Level — see [Session Timeout Reference](#appendix-d-session-timeout-reference) for IL-specific requirements |
| ISSM | Information System Security Manager |
| ISSO | Information System Security Officer |
| JIT | Just-in-Time (provisioning) — see [Implementation Guide](implementation-guide.md#42-account-provisioning) |
| MFA | Multi-Factor Authentication — see [MFA Compliance Matrix](#appendix-e-mfa-factor-compliance-matrix) |
| NSS | National Security Systems |
| ODP | Organization-Defined Parameter — see [NIST 800-171 DoD ODPs](#g5-dod-organization-defined-parameters) |
| OIDC | OpenID Connect |
| OCSP | Online Certificate Status Protocol |
| OCI | Oracle Cloud Infrastructure |
| PIV | Personal Identity Verification |
| PKI | Public Key Infrastructure |
| POA&M | Plan of Action and Milestones |
| SAML | Security Assertion Markup Language |
| SCIM | System for Cross-domain Identity Management |
| SIEM | Security Information and Event Management |
| SLO | Single Logout |
| SP | Service Provider |
| SRG | Security Requirements Guide — see [Session Timeout Reference](#d1-disa-srg-requirements) |
| SSP | System Security Plan |

---

## Related Documents

- [DoD Authentication Standards](authentication-standards.md) — Policy requirements and mandatory controls
- [OCI IAM Assessment](oci-iam-assessment.md) — Platform suitability evaluation
- [Implementation Guide](implementation-guide.md) — Step-by-step configuration guidance
- [Executive Summary](executive-summary.md) — One-page ATO determination
- [NIST 800-171 Gap Analysis](nist-800-171-gap-analysis.md) — CUI protection compliance assessment

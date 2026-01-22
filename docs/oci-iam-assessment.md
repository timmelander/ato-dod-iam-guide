# Oracle IAM Identity Domains — DoD ATO Suitability Assessment

**Applicable Environment:** OCI DoD Realm (IL4/IL5/IL6) and Nonfederal CUI Systems
**Purpose:** SSP, Security Control Implementation Statements, ATO Review Package, CMMC Assessment
**Last Updated:** January 2026

---

## 1. Platform Authorization Status

| Attribute | Status |
|-----------|--------|
| **OCI US Government Cloud** | FedRAMP High JAB P-ATO |
| **OCI US Defense Cloud** | DISA IL4, IL5, IL6 Provisional Authorization |
| **IAM Identity Domains availability** | Available in DoD Realm regions |

Oracle IAM Identity Domains operates within OCI's authorized boundary for DoD workloads at IL4/IL5/IL6.

---

## 2. Control Inheritance Narrative

Oracle IAM Identity Domains inherits the following from the OCI DoD Realm authorization boundary:

- **Cryptographic protections** — TLS, encryption at rest
- **Infrastructure security** — Physical, network, platform controls
- **Audit log integrity** — OCI Audit service tamper protection
- **Service availability** — Oracle-managed SLAs and redundancy

**Customer responsibility includes:**

- Identity policy enforcement
- MFA configuration and factor selection
- Federation trust establishment
- Session timeout configuration
- Log forwarding and extended retention
- User lifecycle management

---

## 3. Compliance Capability Determination

### 3.1 Assessment Question

Does Oracle IAM Identity Domains provide the required technical capabilities to satisfy DoD authentication standards for an OCI DoD Realm?

### 3.2 Assessment Answer

**YES** — Oracle IAM Identity Domains can satisfy DoD authentication standards when properly configured with required external integrations.

### 3.3 NIST 800-171 / CMMC Compliance Determination

**Assessment Question:** Does Oracle IAM Identity Domains support NIST SP 800-171 Rev 3 requirements for CUI protection and CMMC Level 2 certification?

**Assessment Answer:** **YES** — Oracle IAM Identity Domains supports NIST 800-171 compliance for identity and access management controls. See [NIST 800-171 Gap Analysis](nist-800-171-gap-analysis.md) for detailed control mapping.

| Control Family | Total Controls | Supported | Customer Config | Inherited |
|----------------|---------------|-----------|-----------------|-----------|
| Access Control (03.01) | 12 | 7 | 3 | 0 |
| Audit (03.03) | 7 | 4 | 2 | 1 |
| Identification & Auth (03.05) | 9 | 6 | 2 | 1 |
| System & Comms (03.13) | 7 | 2 | 0 | 5 |

---

## 4. Native Capabilities Supporting Compliance

| Capability | DoD Requirement | NIST 800-171 Control | Implementation Notes |
|------------|-----------------|---------------------|----------------------|
| SAML 2.0 federation | FAL2/FAL3 | 03.05.04 | Full SP and IdP support |
| OIDC support | Alternative protocol | 03.05.04 | Native support |
| X.509 certificate authentication | CAC/PIV | 03.05.02 | Via X.509 IdP configuration |
| FIDO2/WebAuthn | AAL2/AAL3 | 03.05.03 | Supported in Premium domain types |
| Sign-on policies | IA-2 | 03.01.01, 03.05.03 | Granular rule configuration |
| MFA enforcement | IA-2(1), IA-2(2) | 03.05.03 | Multiple compliant factor types |
| Session management | AC-11, AC-12 | 03.01.10, 03.13.05 | Configurable timeouts |
| Audit logging | AU-2, AU-3, AU-12 | 03.03.01-03.03.04 | OCI Audit service integration |
| Adaptive security | Risk-based auth | 03.01.01 | Device, network, location evaluation |
| SCIM provisioning | Account lifecycle | 03.01.01 | Automated sync with authoritative sources |
| Password policies | IA-5(1) | 03.05.09 | Configurable length, complexity, history |
| User identifier management | IA-4 | 03.05.05 | Unique OCIDs, no automatic recycling |

---

## 5. Required External Integrations

| Integration | Purpose | Configuration Path |
|-------------|---------|-------------------|
| **DoD Enterprise IdP (ICAM)** | PKI authentication source, CAC/PIV validation | SAML 2.0 federation |
| **Active Directory Federation Services** | CAC/PIV authentication | Federation with DoD AD FS |
| **DoD PKI Infrastructure** | Certificate chain validation, CRL/OCSP | Performed at DoD IdP |
| **SIEM Platform** | Centralized security monitoring | OCI Audit → Streaming → SIEM |

---

## 6. Limitations and Compensating Controls

| Limitation | Impact | Compensating Control |
|------------|--------|---------------------|
| Native CAC/PIV not directly terminated | Cannot directly validate DoD PKI chain within OCI IAM | Federate with DoD-managed IdP that performs certificate validation |
| SMS OTP available but non-compliant | Risk of enabling prohibited factor | Disable SMS factor in domain MFA settings; enforce compliant factors only via sign-on policy |
| Email OTP available but non-compliant | Risk of enabling prohibited factor | Disable email factor; enforce compliant factors only |
| Session timeout defaults may exceed SRG | Non-compliance if unchanged | Configure sign-on policy with ≤15 min idle, ≤12 hr (IL4) / ≤8 hr (IL5) absolute |
| No RFC 8471 Token Binding | Token binding compliance question | Achieved via FIDO2 proof-of-possession and short-lived tokens |

---

## 7. ATO Supportability Statement

**Oracle IAM Identity Domains IS ATO-supportable for OCI DoD Realm deployments at IL4 and IL5**, provided the following conditions are met:

### 7.1 Mandatory Conditions

1. **Federation with DoD-approved IdP** — Authentication must be federated through a DoD-approved Identity Provider that performs CAC/PIV validation and certificate revocation checking

2. **Phishing-resistant MFA enforcement** — Sign-on policies must enforce phishing-resistant MFA (FIDO2 or CAC/PIV) for all privileged access

3. **Non-compliant factors disabled** — SMS OTP, email OTP, and voice call OTP must be disabled in domain MFA settings

4. **Session management compliance** — Session timeouts must be configured to meet DISA SRG thresholds:
   - Idle: ≤15 minutes
   - Absolute: ≤12 hours (IL4), ≤8 hours (IL5)

5. **Audit log forwarding** — Authentication events must be forwarded to a DoD-approved SIEM with required retention periods

6. **Password-only authentication disabled** — MFA must be enforced for all users; password-only access must be prohibited

### 7.2 Additional Conditions for NIST 800-171 / CMMC

For organizations subject to DFARS 252.204-7012 or pursuing CMMC Level 2:

7. **Password policy compliance** — Minimum 16-character passwords per DoD ODP

8. **Inactive account management** — Disable accounts inactive for 90+ days per DoD ODP

9. **Identifier management** — Maintain records to prevent identifier reuse for 10 years per DoD ODP

10. **Account lifecycle documentation** — Document provisioning and deprovisioning procedures

For detailed requirements, see [NIST 800-171 Gap Analysis](nist-800-171-gap-analysis.md#8-compliance-summary).

### 7.3 Domain Type Requirement

Identity Domain must be **Premium** or **Oracle Apps Premium** type to access the full MFA feature set including FIDO2/WebAuthn support.

---

## 8. IL4 vs IL5 Distinctions

| Aspect | IL4 | IL5 |
|--------|-----|-----|
| Minimum AAL | AAL2 | AAL2 (AAL3 for privileged/NSS) |
| Session absolute timeout | ≤12 hours | ≤8 hours |
| Federation assurance | FAL2 | FAL2 (FAL3 for NSS) |
| Non-PKI MFA | Permitted with exception | Restricted; hardware-only |
| Mobile push MFA | Conditional | Not Recommended |

---

## 9. Customer vs Oracle Responsibility Matrix

| Control Area | Oracle (CSP) | Customer | NIST 800-171 |
|--------------|:------------:|:--------:|:------------:|
| Infrastructure security | ✓ | | 03.13.01 |
| IAM service availability | ✓ | | — |
| Platform cryptographic controls | ✓ | | 03.13.11 |
| Native audit log capture | ✓ | | 03.03.01-03 |
| Sign-on policy configuration | | ✓ | 03.05.03 |
| MFA factor enablement/disablement | | ✓ | 03.05.03 |
| Federation trust establishment | | ✓ | 03.05.04 |
| Session timeout configuration | | ✓ | 03.01.10 |
| Audit log forwarding to SIEM | | ✓ | 03.03.05 |
| Extended log retention | | ✓ | 03.03.04 |
| User provisioning/deprovisioning | | ✓ | 03.01.01 |
| Privileged access review | | ✓ | 03.01.07 |
| Identity proofing (inherited from IdP) | | ✓ | 03.05.01 |
| Password policy configuration | | ✓ | 03.05.09 |
| Identifier management policy | | ✓ | 03.05.05 |
| Inactive account management | | ✓ | 03.01.01 |

---

## 10. Assumptions

1. Customer operates within an OCI DoD Realm tenancy with appropriate DISA authorization
2. DoD enterprise IdP is available and configured for CAC/PIV certificate authentication
3. Customer has documented exception if using non-PKI MFA for any access scenarios
4. SIEM platform is DoD-approved for log aggregation at appropriate classification level
5. Identity domain type is Premium (or Oracle Apps Premium) for full MFA feature set
6. Certificate revocation checking (OCSP/CRL) is performed by the federated DoD IdP

---

## Related Documents

- [DoD Authentication Standards](authentication-standards.md) — Policy requirements and mandatory controls
- [Implementation Guide](implementation-guide.md) — Step-by-step configuration guidance
- [Executive Summary](executive-summary.md) — One-page ATO determination
- [NIST 800-171 Gap Analysis](nist-800-171-gap-analysis.md) — CUI protection compliance assessment
- [Control Mappings and Appendices](appendices.md) — Control mappings, reference tables, glossary

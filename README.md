# DoD IAM Compliance Guide for OCI

**Oracle IAM Identity Domains — Authority to Operate (ATO) Documentation**

This repository provides comprehensive guidance for achieving DoD Authority to Operate (ATO) compliance using Oracle IAM Identity Domains within OCI DoD Realm environments.

---

## Purpose

This documentation assists security architects, ISSOs, and compliance teams in:

- Understanding DoD authentication requirements for web applications
- Evaluating Oracle IAM Identity Domains for ATO suitability
- Implementing compliant authentication configurations
- Preparing evidence artifacts for ATO assessments

---

## Applicable Standards

| Standard | Description |
|----------|-------------|
| [DISA Cloud Computing SRG v1r5](https://dl.dod.cyber.mil/wp-content/uploads/cloud/SRG/index.html) | Cloud Security Requirements by Impact Level |
| [DoD CIO MFA Policy (2024)](https://dodcio.defense.gov/Portals/0/Documents/Cyber/MFA_Strategy.pdf) | Multi-Factor Authentication Requirements |
| [DoDI 8520.03](https://www.esd.whs.mil/Directives/issuances/dodi/) | Identification and Authentication for DoD Information Systems |
| [NIST SP 800-53 Rev 5](https://csrc.nist.gov/pubs/sp/800/53/r5/upd1/final) | Security and Privacy Controls (IA, AC, AU families) |
| [NIST SP 800-63 Rev 4](https://pages.nist.gov/800-63-4/) | Digital Identity Guidelines (IAL, AAL, FAL) |
| [NIST SP 800-171 Rev 3](https://csrc.nist.gov/pubs/sp/800/171/r3/final) | CUI Protection for Nonfederal Systems (DFARS, CMMC) |

---

## Impact Levels Covered

- **IL4** — Controlled Unclassified Information (CUI)
- **IL5** — CUI and National Security Systems (NSS)
- **IL6** — Classified (referenced where applicable)

---

## Documentation Structure

| Document | Description |
|----------|-------------|
| [Executive Summary](docs/executive-summary.md) | One-page ATO determination summary for leadership and assessors |
| [Authentication Standards](docs/authentication-standards.md) | DoD-approved authentication requirements for web applications |
| [OCI IAM Assessment](docs/oci-iam-assessment.md) | Suitability evaluation of Oracle IAM Identity Domains |
| [Implementation Guide](docs/implementation-guide.md) | Step-by-step configuration guidance for compliance |
| [NIST 800-171 Gap Analysis](docs/nist-800-171-gap-analysis.md) | CUI protection compliance assessment and CMMC alignment |
| [Appendices](docs/appendices.md) | Control mappings, responsibility matrix, and reference tables |

---

## Quick Reference

### ATO Determination

**Oracle IAM Identity Domains is ATO-supportable at IL4 and IL5** when:

1. Authentication is federated through a DoD-approved IdP (CAC/PIV)
2. Phishing-resistant MFA is enforced for all privileged access
3. Non-compliant factors (SMS, email OTP) are disabled
4. Session timeouts meet DISA SRG thresholds
5. Audit logs are forwarded to an approved SIEM

### Key Configuration Requirements

| Setting | IL4 | IL5 |
|---------|-----|-----|
| Idle Timeout | ≤15 min | ≤15 min |
| Absolute Session | ≤12 hours | ≤8 hours |
| MFA | Required | Required |
| SMS/Email OTP | Disabled | Disabled |
| FIDO2/WebAuthn | Approved | Approved |

---

## Audience

- Information System Security Officers (ISSO)
- Information System Security Managers (ISSM)
- ATO Assessors
- Security Architects
- Cloud Compliance Teams

---

## Usage

This documentation is suitable for direct inclusion in:

- System Security Plans (SSP)
- Security Control Implementation Statements
- ATO Review Packages
- Compliance Evidence Packages

---

## Assumptions

1. Deployment is within an OCI DoD Realm tenancy with DISA authorization
2. DoD enterprise IdP is available for CAC/PIV federation
3. Identity Domain type is Premium or Oracle Apps Premium
4. SIEM platform is DoD-approved for the target classification level

---

## Disclaimer

This documentation provides guidance based on publicly available DoD and NIST standards. Verify all configurations against current DISA SRG releases and agency-specific implementation guidance prior to ATO assessment. This is not official DoD guidance.

---

## License

This documentation is provided for informational purposes. See [LICENSE](LICENSE) for terms.

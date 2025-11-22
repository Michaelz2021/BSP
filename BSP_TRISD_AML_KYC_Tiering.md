# AI TrustTrade AML/KYC Tiering System
## For BSP TRISD Meeting

**Prepared by:** NS Solution Corp  
**Date:** November 2025  
**Purpose:** Anti-Money Laundering & Know Your Customer Compliance Framework

---

## Executive Summary

AI TrustTrade implements a **risk-based, tiered KYC approach** aligned with:
- **BSP Circular No. 1022** (AMLA implementing regulations)
- **BSP Circular No. 706** (Customer Due Diligence)
- **Data Privacy Act of 2012** (RA 10173)
- **FATF Recommendations** (Financial Action Task Force)

The tiering system balances **financial inclusion** (enabling unbanked access) with **robust AML/CFT controls** through progressive verification levels tied to transaction limits and risk profiles.

---

## 1. KYC Tier Structure

### 1.1 Overview of 4 Tiers

```
┌─────────────────────────────────────────────────────────────┐
│                    KYC TIER PYRAMID                          │
├─────────────────────────────────────────────────────────────┤
│                                                               │
│                   TIER 3: PREMIUM                             │
│              Income Verification Required                     │
│            Transaction Limit: UNLIMITED                       │
│           ₱500,000+/month | Enhanced Due Diligence           │
│                    ▲                                          │
│              TIER 2: ENHANCED                                 │
│           Proof of Address Required                           │
│        Transaction Limit: ₱500,000/month                      │
│      Manual compliance review | Address verification         │
│                    ▲                                          │
│                TIER 1: VERIFIED                               │
│            Government ID Required                             │
│         Transaction Limit: ₱50,000/month                      │
│    AI-powered verification | Liveness detection              │
│                    ▲                                          │
│              TIER 0: BASIC                                    │
│           Mobile Number Only                                  │
│        Transaction Limit: ₱5,000/month                        │
│         SMS verification | Bot prevention                     │
│                                                               │
└─────────────────────────────────────────────────────────────┘
```

---

## 2. Detailed Tier Specifications

### 2.1 TIER 0: BASIC (Mobile Verification)

**Purpose:** Enable immediate marketplace access while preventing bot accounts

**Verification Requirements:**
- ✅ Philippine mobile number (+63)
- ✅ SMS OTP verification
- ✅ Device fingerprinting
- ✅ Basic profile information (name, email)

**Transaction Limits:**
- **Per Transaction:** ₱5,000 maximum
- **Monthly Limit:** ₱5,000 aggregate
- **Annual Limit:** ₱60,000
- **Wallet Balance Cap:** ₱10,000

**Allowed Activities:**
- Browse services
- Post small service requests (<₱5,000)
- Bid on entry-level jobs
- Receive payments (limited)

**Risk Level:** 🟢 **LOW** (Entry-level, high friction for abuse)

**Processing Time:** Immediate (< 1 minute)

**Retention Period:** 1 year post-account closure

---

### 2.2 TIER 1: VERIFIED (Government ID)

**Purpose:** Standard marketplace participation with AI-verified identity

**Verification Requirements:**
- ✅ Valid Philippine Government-Issued ID:
  - National ID (PhilSys)
  - Driver's License
  - Passport
  - UMID
  - Postal ID
  - Voter's ID
- ✅ AI-powered OCR data extraction
- ✅ Selfie with liveness detection (blink, head turn)
- ✅ Face-to-ID biometric matching (>85% confidence)
- ✅ Government database cross-check (if API available)
- ✅ Duplicate account prevention (facial recognition)

**Data Collected:**
- Full legal name
- Date of birth
- ID number
- Address (from ID)
- ID expiration date
- Biometric template (hashed)

**Transaction Limits:**
- **Per Transaction:** ₱50,000 maximum
- **Monthly Limit:** ₱50,000 aggregate
- **Annual Limit:** ₱600,000
- **Wallet Balance Cap:** ₱50,000

**Allowed Activities:**
- Full marketplace access (mid-tier services)
- Escrow transactions
- Trust Score building
- Insurance-protected services
- Micro-credit eligibility (starter tier)

**Risk Level:** 🟡 **MEDIUM** (Verified identity, standard monitoring)

**Processing Time:** 5-10 minutes (automated)

**Retention Period:** 5 years post-account closure

**Rejection Criteria:**
- ID expired or invalid format
- Face match below 85% confidence
- Blurry or tampered document
- Underage (<18 years)
- Previous fraud/suspension record
- Duplicate account detected

---

### 2.3 TIER 2: ENHANCED (Proof of Address)

**Purpose:** Higher transaction volumes with enhanced due diligence

**Verification Requirements:**
- ✅ All Tier 1 requirements
- ✅ Proof of Address (must match ID):
  - Utility bill (electric, water, internet) - last 3 months
  - Bank statement - last 3 months
  - Government document with address
  - Barangay certificate
- ✅ Manual compliance team review
- ✅ Address validation (geocoding check)
- ✅ Phone interview (random sampling, 10% of applications)

**Transaction Limits:**
- **Per Transaction:** ₱500,000 maximum
- **Monthly Limit:** ₱500,000 aggregate
- **Annual Limit:** ₱6,000,000
- **Wallet Balance Cap:** ₱100,000

**Allowed Activities:**
- High-value service contracts
- Corporate service requests
- Advanced micro-credit (₱20,000-50,000)
- Preferential escrow release terms
- Premium Trust Score features

**Risk Level:** 🟡 **MEDIUM-HIGH** (Enhanced monitoring, periodic reviews)

**Processing Time:** 24 hours (manual review)

**Retention Period:** 7 years post-account closure

**Additional Monitoring:**
- Quarterly transaction pattern review
- Address re-verification annually
- Enhanced CDD triggers (see Section 4)

---

### 2.4 TIER 3: PREMIUM (Income Verification)

**Purpose:** Unlimited transactions for high-volume providers/businesses

**Verification Requirements:**
- ✅ All Tier 2 requirements
- ✅ Income/Business Verification:
  - **For Individuals:**
    - ITR (Income Tax Return) - last 2 years
    - Payslips - last 6 months
    - Certificate of Employment
  - **For Businesses:**
    - DTI/SEC registration
    - Business permits
    - Audited financial statements
    - Mayor's permit
- ✅ Enhanced Due Diligence (EDD) questionnaire
- ✅ Source of funds declaration
- ✅ In-person interview (for amounts >₱1M/month)
- ✅ Third-party background check (optional)

**Transaction Limits:**
- **Per Transaction:** UNLIMITED
- **Monthly Limit:** UNLIMITED (with monitoring)
- **Annual Limit:** UNLIMITED (with monitoring)
- **Wallet Balance Cap:** ₱500,000

**Allowed Activities:**
- Unrestricted marketplace access
- Corporate accounts and multi-user access
- Advanced micro-lending (₱50,000-200,000)
- Bulk service contracts
- API integration (for businesses)
- Dedicated account manager

**Risk Level:** 🔴 **HIGH** (Continuous monitoring, real-time alerts)

**Processing Time:** 2-3 days (comprehensive review)

**Retention Period:** 10 years post-account closure

**Ongoing Requirements:**
- Annual income re-verification
- Monthly transaction reporting
- Quarterly compliance review
- Real-time suspicious activity monitoring

---

## 3. Risk-Based Transaction Monitoring

### 3.1 Red Flags and Trigger Events

**Automatic Account Review Triggers:**

| Trigger Event | Risk Level | Action |
|--------------|------------|--------|
| Sudden 300% increase in transaction volume | HIGH | Immediate CDD update |
| Multiple failed verification attempts | MEDIUM | Temporary suspension |
| Geographic mismatch (IP vs. address) | MEDIUM | Additional verification |
| Transactions to/from sanctioned countries | CRITICAL | Block + report to AMLC |
| Structuring (multiple just-below-limit txns) | HIGH | Enhanced monitoring |
| Rapid wallet loading/withdrawal | HIGH | Source of funds inquiry |
| Identical service requests from multiple accounts | MEDIUM | Collusion investigation |
| Cross-border transactions | MEDIUM | EDD required |

### 3.2 Monitoring Frequency by Tier

```
TIER 0: Real-time automated (rule-based)
TIER 1: Daily batch analysis (AI + rules)
TIER 2: Daily real-time + weekly manual review
TIER 3: Real-time continuous + monthly deep-dive
```

### 3.3 Suspicious Activity Reporting (SAR)

**SAR Filing Criteria:**
- Transactions ≥ ₱500,000 with unclear purpose
- Pattern matching known fraud typologies
- Customer evasiveness or false information
- Transactions inconsistent with customer profile
- Use of multiple accounts to evade limits

**Reporting Timeline:**
- **Immediate:** Report to internal compliance team
- **Within 5 days:** File STR (Suspicious Transaction Report) to AMLC
- **Within 24 hours:** Block account if warranted

---

## 4. Customer Due Diligence (CDD) Levels

### 4.1 Simplified Due Diligence (SDD)

**Applicable to:** Tier 0 accounts

**Requirements:**
- Basic identification
- Transaction monitoring only
- No enhanced checks

### 4.2 Standard Customer Due Diligence

**Applicable to:** Tier 1 accounts

**Requirements:**
- Identity verification (ID + biometrics)
- Address collection
- Purpose of account declaration
- Ongoing transaction monitoring

### 4.3 Enhanced Due Diligence (EDD)

**Applicable to:** Tier 2 & 3 accounts, or triggered by risk factors

**Requirements:**
- All CDD requirements
- Source of funds/wealth verification
- Enhanced background checks
- Ongoing monitoring with lower thresholds
- Senior management approval for high-risk

**EDD Triggers:**
- High transaction volume (>₱200,000/month)
- PEP (Politically Exposed Person) status
- High-risk occupation (e.g., money changer, casino)
- Adverse media mentions
- Cross-border transactions
- Business account with complex ownership

---

## 5. Special Categories

### 5.1 Politically Exposed Persons (PEPs)

**Definition:** Government officials, senior executives, family members

**Requirements:**
- Automatic EDD trigger
- Senior compliance approval required
- Source of wealth documentation
- Ongoing monitoring (monthly reviews)
- Transaction limits: Same as tier, but with approval
- Relationship disclosure

**PEP Categories:**
- Foreign PEPs (heads of state, ministers)
- Domestic PEPs (Philippine government officials)
- International organization PEPs (UN, World Bank)
- Family members and close associates

### 5.2 Corporate/Business Accounts

**Additional Requirements:**
- Business registration documents
- Beneficial ownership declaration (ultimate owners >25%)
- Board resolution authorizing platform use
- Tax identification number (TIN)
- Articles of incorporation
- Authorized signatory identification

**Transaction Limits:**
- Minimum Tier 2 verification required
- Tier 3 recommended for >₱500,000/month

### 5.3 Minor Accounts (Future Feature)

**Not currently supported** - All users must be 18+ years old

**Future Implementation (with parental consent):**
- Parental/guardian verification required
- Limited to Tier 0 equivalent
- Transaction cap: ₱1,000/month
- Educational services only

---

## 6. Data Privacy and Security

### 6.1 Compliance with Data Privacy Act

**Lawful Basis:**
- Consent (primary)
- Legal obligation (AML/KYC)
- Legitimate interest (fraud prevention)

**Data Minimization:**
- Collect only necessary information per tier
- Progressive disclosure as tier increases
- Automated deletion timelines

**User Rights:**
- Right to access stored data
- Right to correction
- Right to erasure (after retention period)
- Right to data portability
- Right to object to processing

### 6.2 Security Measures

**Encryption:**
- AES-256 encryption at rest
- TLS 1.3 in transit
- End-to-end encrypted document uploads

**Access Controls:**
- Role-based access (RBAC)
- Multi-factor authentication for compliance team
- Audit logs for all data access
- Watermarked copies for in-app viewing

**Storage:**
- Philippine-based servers (Data localization)
- ISO 27001 certified data centers
- Regular penetration testing
- Disaster recovery with 99.9% uptime

**Retention and Deletion:**
- Tier 0: 1 year post-closure
- Tier 1: 5 years post-closure
- Tier 2: 7 years post-closure
- Tier 3: 10 years post-closure
- Automated deletion with compliance approval

---

## 7. Upgrade and Downgrade Pathways

### 7.1 Tier Upgrades

**Self-Service Upgrades:**
- Tier 0 → Tier 1: Anytime via app (automated)
- Tier 1 → Tier 2: Anytime via app (manual review)
- Tier 2 → Tier 3: Anytime via app (comprehensive review)

**Incentives to Upgrade:**
- Higher transaction limits
- Lower platform fees (-0.5% per tier)
- Better Trust Score algorithm weighting
- Access to micro-credit
- Faster escrow release
- Premium support

**Processing SLA:**
- Tier 1: 10 minutes (90% automated)
- Tier 2: 24 hours (manual review)
- Tier 3: 72 hours (enhanced due diligence)

### 7.2 Tier Downgrades

**Automatic Downgrade Triggers:**
- Failed re-verification (ID expired)
- Consistent under-utilization of limit
- User request for lower tier
- Risk mitigation measure

**Forced Downgrade Scenarios:**
- Document expiration without renewal
- Address change without re-verification
- Suspicious activity (pending investigation)

---

## 8. Training and Governance

### 8.1 Compliance Team Structure

```
Chief Compliance Officer (CCO)
    │
    ├── AML/KYC Manager
    │    ├── KYC Analysts (3)
    │    ├── Transaction Monitoring (2)
    │    └── STR Filing Officer (1)
    │
    ├── Data Privacy Officer (DPO)
    │    └── Data Protection Specialist (1)
    │
    └── Internal Audit (1)
```

### 8.2 Training Requirements

**All Employees:**
- Annual AML/KYC awareness training
- Data Privacy Act compliance training
- Code of conduct and ethics

**Compliance Team:**
- Quarterly AMLC updates
- Advanced transaction monitoring techniques
- SAR/STR writing workshops
- Regulatory changes briefings

**Customer Support:**
- KYC document verification basics
- Red flag identification
- Escalation protocols

### 8.3 Audit and Review

**Internal Audits:**
- Monthly: Transaction monitoring effectiveness
- Quarterly: KYC documentation completeness
- Annual: Full AML/CFT program review

**External Audits:**
- Annual: Independent AML audit (Big 4 firm)
- Bi-annual: IT security audit (ISO 27001)
- As needed: Regulatory examinations (BSP, AMLC)

**Continuous Improvement:**
- KYC rejection rate analysis
- False positive reduction (monitoring)
- User friction measurement
- Regulatory feedback integration

---

## 9. Integration with Trust Score System

### 9.1 KYC Tier Impact on Trust Score

**Trust Score Calculation Weighting:**

| Factor | Tier 0 | Tier 1 | Tier 2 | Tier 3 |
|--------|--------|--------|--------|--------|
| Base Trust Score (upon verification) | 100 | 300 | 500 | 700 |
| Identity Verification Weight | 10% | 25% | 30% | 35% |
| Transaction History Weight | 40% | 30% | 25% | 20% |
| Customer Reviews Weight | 30% | 25% | 20% | 15% |
| Other Factors Weight | 20% | 20% | 25% | 30% |

**Benefits of Higher Tier for Trust Score:**
- Faster Trust Score accumulation
- Higher maximum achievable score
- Lower penalty multiplier for disputes
- Preferential algorithm treatment

### 9.2 Cross-System Integration

**Escrow System:**
- Higher tiers → Faster auto-release thresholds
- Tier 3 → Pre-approved partial releases

**Micro-Credit System:**
- Tier 1: Max ₱10,000 loan
- Tier 2: Max ₱50,000 loan
- Tier 3: Max ₱200,000 loan

**Insurance System:**
- Higher tiers eligible for lower premiums
- Tier 3 → Enterprise insurance packages

---

## 10. Regulatory Reporting

### 10.1 Regular Reports to BSP/SEC

**Monthly Reports:**
- Total accounts by KYC tier
- Tier upgrade/downgrade statistics
- KYC rejection rates and reasons
- Average verification processing times

**Quarterly Reports:**
- Suspicious transaction reports filed
- Enhanced due diligence cases
- PEP accounts and monitoring
- Cross-border transaction summary

**Annual Reports:**
- Full AML/CFT program assessment
- KYC system effectiveness metrics
- Training completion rates
- External audit findings and remediation

### 10.2 Ad-Hoc Reporting

**Immediate (Within 24 hours):**
- Material security breaches
- Data privacy incidents
- Sanctions hits (OFAC, UN lists)
- Large-scale fraud detection

**Within 5 Days:**
- Suspicious Transaction Reports (STRs)
- Covered Transaction Reports (CTRs) if ≥₱500,000

---

## 11. Sandbox-Specific Provisions

### 11.1 Pilot Phase Adjustments

**Simplified Requirements:**
- Tier 0 & 1 only during first 3 months
- PhilSys API integration deferred (manual check)
- Lower transaction monitoring thresholds

**Enhanced Reporting:**
- Weekly updates to SEC/BSP
- Real-time escalation for >₱100,000 transactions
- Monthly user feedback on KYC friction

### 11.2 Gradual Rollout

**Phase 1 (Months 1-3):** Tier 0 & 1 only
**Phase 2 (Months 4-6):** Tier 2 introduction
**Phase 3 (Months 7-9):** Tier 3 introduction
**Phase 4 (Months 10-12):** Full system + optimizations

---

## 12. Success Metrics

### 12.1 KYC Performance KPIs

**Efficiency Metrics:**
- ✅ Target: 90%+ automated Tier 1 approvals
- ✅ Target: <10 min average Tier 1 processing
- ✅ Target: <24 hr average Tier 2 processing
- ✅ Target: <5% false rejection rate

**Quality Metrics:**
- ✅ Target: <1% fraud incident rate
- ✅ Target: 100% AMLC compliance (zero late STRs)
- ✅ Target: <0.5% data breach incidents

**User Experience Metrics:**
- ✅ Target: <30% KYC abandonment rate
- ✅ Target: >80% user satisfaction with process
- ✅ Target: <5% user complaints re: KYC

### 12.2 Financial Inclusion Metrics

**Tier Distribution (Target Year 1):**
- Tier 0: 20% of users
- Tier 1: 65% of users (primary target)
- Tier 2: 12% of users
- Tier 3: 3% of users

**Previously Unbanked:**
- Target: 60%+ of Tier 1 users have no prior bank account
- Target: 40%+ transition to formal banking within 24 months

---

## 13. Appendices

### Appendix A: Accepted ID Documents

**Primary IDs (Single ID sufficient):**
1. Philippine National ID (PhilSys)
2. Passport
3. Driver's License
4. Professional Regulation Commission (PRC) ID
5. Social Security System (SSS) UMID Card
6. GSIS e-Card

**Secondary IDs (Two required):**
1. Postal ID
2. Voter's ID
3. Barangay Certification
4. School ID (with NBI/Police Clearance)
5. Company ID (with supporting docs)

### Appendix B: Sample KYC Form

[Detailed KYC form template with all required fields]

### Appendix C: Red Flag Indicators

**Identity Fraud:**
- Blurry or altered documents
- Mismatched facial features
- Stock photo detection
- Metadata inconsistencies

**Transaction Fraud:**
- Rapid high-value transactions
- Circular movements (A→B→A)
- Unusual service categories
- Geographic impossibilities

### Appendix D: Escalation Matrix

| Severity | Response Time | Escalation Path |
|----------|--------------|-----------------|
| Critical | <1 hour | CCO → CEO → BSP/AMLC |
| High | <4 hours | AML Manager → CCO |
| Medium | <24 hours | KYC Analyst → AML Manager |
| Low | <72 hours | Automated queue |

### Appendix E: Contact Information

**Compliance Team:**
- Chief Compliance Officer: [Name], [Email], [Phone]
- AML/KYC Manager: [Name], [Email], [Phone]
- Data Privacy Officer: [Name], [Email], [Phone]

**Regulatory Liaison:**
- BSP Contact: [Name], [Email], [Phone]
- SEC Contact: [Name], [Email], [Phone]
- AMLC Reporting: [Email], [Hotline]

---

## Document Control

**Version:** 1.0  
**Date:** November 2025  
**Status:** For BSP TRISD Review  
**Classification:** Confidential  
**Next Review:** Quarterly or upon regulatory feedback

**Approval:**
- ☐ Chief Compliance Officer
- ☐ Chief Executive Officer
- ☐ Board of Directors

---

**END OF DOCUMENT**

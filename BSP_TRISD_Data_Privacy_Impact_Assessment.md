# AI TrustTrade Data Privacy Impact Assessment (DPIA)
## For BSP TRISD Meeting & NPC Compliance

**Prepared by:** NS Solution Corp  
**Date:** November 2025  
**Purpose:** Data Privacy Act 2012 Compliance & Privacy Risk Assessment

---

## Executive Summary

This Data Privacy Impact Assessment (DPIA) evaluates AI TrustTrade's personal data processing activities in compliance with the **Data Privacy Act of 2012 (RA 10173)** and implementing rules issued by the National Privacy Commission (NPC). The assessment identifies privacy risks inherent in operating a marketplace-embedded financial service platform and establishes comprehensive safeguards to protect user privacy while enabling financial inclusion and trust-building in the gig economy.

**Key Findings:**
- **High-Risk Processing:** KYC verification, biometric data, financial transactions, behavioral profiling
- **Lawful Basis:** Consent, legal obligation (AML/CFT), legitimate interest (fraud prevention)
- **Data Subjects:** 10,000+ users (service providers and consumers) during sandbox phase
- **Risk Level:** MEDIUM-HIGH (due to sensitive financial and biometric data)
- **Mitigation:** Comprehensive technical and organizational measures implemented

**Recommendation:** Proceed with planned data processing activities subject to implementation of all identified safeguards and ongoing NPC consultation.

---

## 1. Introduction

### 1.1 Purpose of DPIA

This DPIA is conducted to:
1. **Comply with Data Privacy Act 2012** - Mandatory for high-risk processing (Section 35)
2. **Identify Privacy Risks** - Systematically assess risks to data subject rights and freedoms
3. **Demonstrate Accountability** - Show proactive privacy-by-design approach
4. **Inform Regulatory Decisions** - Provide transparency to BSP, SEC, and NPC
5. **Build User Trust** - Communicate commitment to privacy protection

### 1.2 Scope

**In Scope:**
- All personal data processing activities of AI TrustTrade platform
- Data collected from service providers and consumers
- Data shared with third parties (payment gateways, insurance partners, etc.)
- Data processing by AI systems (Trust Score, fraud detection)
- Cross-border data transfers (if any)

**Out of Scope:**
- Employee HR data (separate DPIA)
- Marketing and sales databases (unless integrated with platform)
- Public information (e.g., publicly available business registrations)

### 1.3 Methodology

**Assessment Process:**
1. Data mapping and inventory (what data, why, where, how long)
2. Legal basis assessment (consent, legal obligation, legitimate interest)
3. Privacy risk identification (unauthorized access, profiling, discrimination)
4. Safeguard evaluation (technical and organizational measures)
5. Residual risk assessment (after safeguards)
6. Stakeholder consultation (DPO, legal, engineering, compliance)
7. Continuous monitoring and review (quarterly)

---

## 2. Description of Processing Activities

### 2.1 Processing Overview

```
┌─────────────────────────────────────────────────────────────┐
│              AI TrustTrade Data Lifecycle                    │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  1. COLLECTION                                              │
│     ├── Registration (name, mobile, email)                  │
│     ├── KYC Verification (ID, selfie, address, income)      │
│     ├── Service Listings (skills, portfolio, certifications)│
│     ├── Transactions (bookings, payments, escrow)           │
│     └── Interactions (reviews, messages, dispute claims)    │
│                                                             │
│  2. PROCESSING                                              │
│     ├── AI Trust Score Calculation (7 factors)              │
│     ├── Fraud Detection (anomaly patterns)                  │
│     ├── Service Matching (consumer-provider)                │
│     ├── Payment Processing (gateway integration)            │
│     └── Dispute Resolution (evidence analysis)              │
│                                                             │
│  3. STORAGE                                                 │
│     ├── Production Database (AWS RDS PostgreSQL)            │
│     ├── Backup Storage (AWS S3, encrypted)                  │
│     ├── Document Storage (KYC docs, AES-256)                │
│     └── Log Files (anonymized after 90 days)                │
│                                                             │
│  4. SHARING                                                 │
│     ├── Payment Gateways (PayMongo, GCash, PayMaya)         │
│     ├── Insurance Partners (risk assessment)                │
│     ├── Regulators (BSP, SEC, AMLC - upon request)          │
│     └── Public Profile (name, rating, verified badges)      │
│                                                             │
│  5. RETENTION & DELETION                                    │
│     ├── Active Users: Indefinite (with consent)             │
│     ├── Closed Accounts: 5-10 years (AML/KYC requirement)   │
│     ├── Transaction Logs: 10 years (regulatory)             │
│     └── Automated Deletion: After retention period          │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

### 2.2 Categories of Data Subjects

| Data Subject Category | Description | Estimated Volume (Year 1) |
|----------------------|-------------|---------------------------|
| **Service Providers** | Individuals offering services (plumbers, tutors, etc.) | 6,000 users |
| **Consumers** | Individuals booking services | 4,000 users |
| **Corporate Clients** | Businesses booking services | 200 accounts |
| **Minors (Future)** | Users under 18 with parental consent | 0 (not yet supported) |

---

## 3. Categories of Personal Data Processed

### 3.1 Personal Data Inventory

#### **Category 1: Basic Identity Information**

**Data Elements:**
- Full legal name (as per ID)
- Date of birth
- Gender (optional, for demographic analytics)
- Mobile phone number (primary contact)
- Email address
- Profile photo (optional)

**Purpose:**
- Account creation and authentication
- Communication with users
- Compliance with KYC regulations
- Service matching and reviews

**Lawful Basis:** Consent + Legal Obligation (KYC)

**Retention Period:** 
- Active accounts: Indefinite
- Closed accounts: 5 years (AML), then deleted

**Sensitivity:** LOW (except when combined with financial data)

---

#### **Category 2: Government-Issued Identification**

**Data Elements:**
- ID type (e.g., Driver's License, Passport, National ID)
- ID number
- ID photo (front/back)
- Issuing authority
- Issue/expiry dates
- Embedded address (on ID)

**Purpose:**
- Identity verification (Tier 1 KYC)
- Age verification (18+ requirement)
- Fraud prevention (duplicate account detection)
- Compliance with AML/CFT regulations

**Lawful Basis:** Legal Obligation (BSP KYC requirements)

**Retention Period:** 10 years post-account closure (AML compliance)

**Sensitivity:** HIGH - Government IDs are sensitive personal information

**Special Safeguards:**
- OCR extraction only (raw images encrypted, access-controlled)
- Watermarked copies for in-app display (not full resolution)
- Compliance team access only (audit logged)
- Encrypted at rest (AES-256) and in transit (TLS 1.3)

---

#### **Category 3: Biometric Data**

**Data Elements:**
- Selfie photo (for liveness detection)
- Facial recognition template (hashed)
- Biometric match score (face-to-ID similarity)

**Purpose:**
- Liveness detection (prevent spoofing with photos)
- Face-to-ID matching (verify ID holder is user)
- Duplicate account prevention (facial recognition)

**Lawful Basis:** Consent + Legal Obligation (enhanced KYC)

**Retention Period:** 
- Selfie: Deleted after verification (or retained if user consents)
- Facial template (hashed): 5 years post-closure

**Sensitivity:** CRITICAL - Biometric data is "privileged information" under DPA

**Special Safeguards:**
- Explicit consent required (separate from general T&C)
- One-way hashing (cannot reverse-engineer face from template)
- No sharing with third parties (except law enforcement with warrant)
- Right to withdraw consent (delete biometric data anytime)
- Regular audits by DPO

---

#### **Category 4: Address and Location**

**Data Elements:**
- Residential address (from ID or proof of address)
- Service delivery addresses (for bookings)
- GPS coordinates (during service delivery)
- IP address (for fraud detection)

**Purpose:**
- Address verification (Tier 2 KYC)
- Service matching (proximity-based)
- Fraud detection (geolocation mismatch)
- Logistics and routing

**Lawful Basis:** Consent + Legitimate Interest (fraud prevention)

**Retention Period:** 
- Residential address: 5 years post-closure
- GPS logs: 90 days (then anonymized)
- IP logs: 180 days

**Sensitivity:** MEDIUM - Can reveal patterns of life

**Special Safeguards:**
- GPS tracking only during active service delivery
- Anonymization of location data after 90 days
- No real-time location sharing without consent
- Opt-out available (but may limit service quality)

---

#### **Category 5: Financial Information**

**Data Elements:**
- Bank account details (for payouts)
- E-wallet numbers (GCash, PayMaya)
- Payment card tokens (not full card numbers - tokenized by gateway)
- Transaction history (amounts, dates, parties)
- Wallet balance
- Escrow status
- Withdrawal requests

**Purpose:**
- Payment processing (top-up, escrow, payout)
- Fraud detection (unusual transaction patterns)
- Financial reporting (to BSP, SEC)
- Trust Score calculation (transaction performance)

**Lawful Basis:** Consent + Legal Obligation (AML/CFT)

**Retention Period:** 10 years (AML/CFT requirement)

**Sensitivity:** HIGH - Financial data requires stringent protection

**Special Safeguards:**
- PCI-DSS compliance (for card payments)
- Tokenization (no raw card numbers stored)
- End-to-end encryption (TLS 1.3)
- Multi-factor authentication for withdrawals
- Daily reconciliation and audit trails

---

#### **Category 6: Income and Employment Data**

**Data Elements:**
- Income Tax Return (ITR)
- Payslips (last 6 months)
- Certificate of Employment
- Business registration documents
- Declared source of income
- Self-reported monthly income

**Purpose:**
- Tier 3 KYC verification
- Enhanced Due Diligence (EDD) for high-value users
- Income verification (future micro-lending partnerships)
- AML/CFT compliance (source of funds)

**Lawful Basis:** Consent + Legal Obligation (EDD for high-risk)

**Retention Period:** 10 years post-closure (AML)

**Sensitivity:** HIGH - Income data can be used for discrimination

**Special Safeguards:**
- Only collected for Tier 3 (optional)
- Manual review by compliance team only
- Encrypted storage with strict access controls
- Not used for any purpose beyond KYC/AML

---

#### **Category 7: Service and Transaction Data**

**Data Elements:**
- Service categories provided/requested
- Booking details (date, time, location, price)
- Service completion status
- Payment amount and method
- Escrow timeline
- Dispute claims and outcomes
- Refund history

**Purpose:**
- Platform functionality (booking, escrow, settlement)
- Trust Score calculation
- Fraud detection and prevention
- Dispute resolution
- Business analytics (aggregate only)

**Lawful Basis:** Consent + Contractual Necessity

**Retention Period:** 10 years (transaction records)

**Sensitivity:** MEDIUM - Reveals patterns of behavior and financial status

**Special Safeguards:**
- Aggregation and anonymization for analytics
- No individual profiling for marketing without consent
- Access restricted to operational needs only

---

#### **Category 8: Behavioral and Interaction Data**

**Data Elements:**
- Customer reviews and ratings (text and star ratings)
- Messages between users (encrypted)
- Search queries and browse history
- App usage patterns (screen views, time spent)
- Clickstream data
- Device information (OS, model, browser)
- Session logs

**Purpose:**
- Trust Score calculation (reviews, responsiveness)
- Service quality improvement
- Fraud detection (bot activity, fake reviews)
- Platform optimization (UX/UI improvements)
- Customer support (troubleshooting)

**Lawful Basis:** Consent + Legitimate Interest (service improvement)

**Retention Period:** 
- Reviews: Indefinite (public, unless deleted by user)
- Messages: 2 years (encrypted)
- Logs: 90 days (then anonymized)

**Sensitivity:** MEDIUM - Can reveal preferences and vulnerabilities

**Special Safeguards:**
- End-to-end encryption for messages (platform cannot read)
- Anonymization of logs after 90 days
- Opt-out for behavioral analytics (with service limitations)
- No third-party sharing without consent

---

#### **Category 9: AI-Generated Data (Trust Score)**

**Data Elements:**
- Trust Score (0-1000 numeric value)
- Score components (7 factors with sub-scores)
- Score history (changes over time)
- Risk flags (fraud indicators)

**Purpose:**
- Provider reputation and consumer protection
- Fraud prevention and platform safety
- Service matching and recommendations
- Access to higher transaction limits

**Lawful Basis:** Legitimate Interest (platform safety and trust)

**Retention Period:** 5 years post-closure

**Sensitivity:** HIGH - Algorithmic decision-making affects access to services

**Special Safeguards:**
- Transparency (users can see score breakdown)
- Explainability (factors and weights disclosed)
- Right to human review (challenge automated decisions)
- Bias testing (regular audits for discrimination)
- No use of protected characteristics (race, religion, etc.)

---

### 3.2 Special Categories of Personal Data

**Privileged Information under DPA (Section 13):**
- ✅ Biometric data (facial recognition templates)
- ❌ Race, ethnic origin - NOT collected
- ❌ Religious beliefs - NOT collected
- ❌ Health data - NOT collected (future: if medical services added)
- ❌ Sexual orientation - NOT collected
- ❌ Political opinions - NOT collected
- ❌ Criminal convictions - NOT directly collected (future: NBI clearance API)

**Additional Consent Required:** Yes, for biometric data (explicit, separate consent)

---

## 4. Legal Basis for Processing

### 4.1 Lawful Basis Assessment

**Primary Legal Bases under DPA Section 12:**

| Processing Activity | Lawful Basis | Justification |
|---------------------|--------------|---------------|
| **Account Registration** | Consent | User voluntarily creates account and accepts T&C |
| **KYC Verification (Tier 1-3)** | Legal Obligation | BSP AML/CFT regulations require identity verification |
| **Biometric Data (Liveness)** | Consent | Explicit consent obtained separately for facial recognition |
| **Transaction Processing** | Contractual Necessity | Required to fulfill booking and payment services |
| **Fraud Detection** | Legitimate Interest | Platform safety and prevention of financial crime |
| **Trust Score Calculation** | Legitimate Interest | Essential for platform trust and consumer protection |
| **Regulatory Reporting** | Legal Obligation | BSP, SEC, AMLC reporting requirements |
| **Data Sharing with Gateways** | Contractual Necessity | Required for payment processing |
| **Marketing Communications** | Consent | Opt-in required, easy opt-out provided |

### 4.2 Consent Management

**Consent Mechanisms:**
1. **Registration Consent:** Checkbox (not pre-checked) for T&C and Privacy Policy
2. **Biometric Consent:** Separate modal with explanation of facial recognition use
3. **Marketing Consent:** Opt-in checkbox during registration (optional)
4. **Location Consent:** OS-level permission (iOS/Android) for GPS access
5. **Cookies Consent:** Banner on web platform (required by ePrivacy principles)

**Consent Requirements:**
- ✅ Freely given (no service denial for non-essential processing)
- ✅ Specific (granular choices for different processing purposes)
- ✅ Informed (clear language, no legal jargon)
- ✅ Unambiguous (affirmative action required, no silence or inactivity)
- ✅ Withdrawable (easy mechanism to revoke consent anytime)

**Consent Withdrawal:**
- In-app settings: "Privacy & Data" → "Manage Consent"
- Effect: Immediate cessation of processing (except legal obligations)
- Impact: May limit platform functionality (e.g., no Trust Score without behavioral data)

---

## 5. Data Subject Rights

### 5.1 Rights under Data Privacy Act

**Section 16 - General Data Privacy Principles:**
Users have the right to:
1. **Right to be Informed** - Transparent privacy notices
2. **Right to Access** - View all personal data held
3. **Right to Object** - Opt-out of processing (where consent-based)
4. **Right to Erasure/Blocking** - Delete or suspend data processing
5. **Right to Rectification** - Correct inaccurate data
6. **Right to Data Portability** - Export data in machine-readable format
7. **Right to Damages** - Compensation for privacy violations
8. **Right to File Complaint** - Lodge complaint with NPC

### 5.2 How AI TrustTrade Enables These Rights

#### **Right to be Informed**
- **Privacy Policy:** Available at signup, plain language (Tagalog + English)
- **Privacy Notice:** In-app banner for key processing activities
- **Data Collection Notice:** Before collecting new data types (e.g., GPS)
- **Third-Party Sharing Notice:** Before sharing with partners

#### **Right to Access**
- **In-App Dashboard:** "My Data" section showing all collected data
- **Download Function:** Export full data package (JSON format)
- **Processing Timeline:** View when data was collected, used, shared
- **Third-Party Access Log:** See who accessed data (within platform)

**Response Time:** Within 15 days of request (NPC standard)

#### **Right to Object**
- **Marketing Opt-Out:** One-click unsubscribe in emails, SMS, push
- **Behavioral Analytics Opt-Out:** Disable non-essential tracking
- **Impact:** May limit personalization, but core services remain accessible

#### **Right to Erasure (Right to be Forgotten)**
- **Immediate Deletion:** Non-essential data (marketing lists, device IDs)
- **Delayed Deletion:** Essential data retained per legal requirements (AML: 5-10 years)
- **Account Closure:** Full account deletion request triggers retention countdown
- **Exceptions:** Cannot delete data under legal hold (ongoing dispute, investigation)

**Process:**
1. User requests deletion via in-app form or email to dpo@aitrustrade.ph
2. DPO verifies identity and assesses retention obligations
3. Non-essential data deleted immediately
4. Essential data flagged for deletion after retention period
5. User receives confirmation email with deletion timeline

#### **Right to Rectification**
- **Self-Service:** Users can update profile, contact info, preferences
- **KYC Data:** Contact support to update ID or address (re-verification required)
- **Transaction Data:** Cannot modify (audit integrity), but can add notes/clarifications

#### **Right to Data Portability**
- **Export Formats:** JSON, CSV, PDF (user selects)
- **Data Included:** All personal data, transaction history, reviews, Trust Score breakdown
- **Delivery:** Email download link (expires in 7 days)
- **Frequency:** Unlimited requests, but rate-limited (1 per week)

**Export Contents:**
```json
{
  "personal_info": {
    "name": "Juan Dela Cruz",
    "email": "juan@example.com",
    "mobile": "+639171234567",
    "registration_date": "2025-03-15"
  },
  "kyc_data": {
    "tier": 1,
    "id_type": "Driver's License",
    "verification_date": "2025-03-16",
    "status": "Verified"
  },
  "trust_score": {
    "current_score": 785,
    "breakdown": {
      "skill_verification": 200,
      "completion_rate": 150,
      // ...
    }
  },
  "transactions": [
    {
      "id": "TXN_12345",
      "date": "2025-04-01",
      "amount": 1500,
      "service": "Aircon cleaning",
      "status": "Completed"
    },
    // ...
  ]
}
```

### 5.3 Data Subject Request Process

**Submission Channels:**
- In-app form: Settings → Privacy → Data Request
- Email: dpo@aitrustrade.ph
- Postal mail: [Address] (Attn: Data Protection Officer)

**Verification:**
- Identity verification required (prevent fraudulent requests)
- For in-app requests: Multi-factor authentication (MFA)
- For email/mail: Copy of ID + signed authorization letter

**Response Timeline:**
- Acknowledgment: Within 3 business days
- Fulfillment: Within 15 days (NPC guideline)
- Extension: Up to 30 days if complex (with explanation to user)

**Tracking:**
- Each request assigned unique ID (DSR-YYYYMMDD-####)
- Status updates sent via email and in-app notifications
- User can track progress in "My Data Requests" dashboard

---

## 6. Privacy Risks and Safeguards

### 6.1 Risk Assessment Matrix

| Risk | Likelihood | Impact | Risk Level | Safeguards |
|------|-----------|--------|-----------|-----------|
| **Unauthorized Access to KYC Data** | Medium | Critical | HIGH | Encryption, access controls, MFA, audit logs |
| **Biometric Data Breach** | Low | Critical | MEDIUM | One-way hashing, separate storage, no third-party sharing |
| **Re-identification from Anonymized Data** | Low | High | MEDIUM | K-anonymity (k≥5), differential privacy techniques |
| **Algorithmic Bias in Trust Score** | Medium | High | MEDIUM-HIGH | Bias audits, explainability, human review option |
| **Insider Threat (Employee Abuse)** | Low | High | MEDIUM | Role-based access, audit logs, background checks |
| **Third-Party Data Misuse (Gateways)** | Low | Medium | LOW | Contractual safeguards, DPAs, regular audits |
| **Cross-Border Data Transfer Violation** | Low | Medium | LOW | Data localization (Philippines only), NPC notification |
| **User Profiling for Discrimination** | Medium | High | MEDIUM-HIGH | No use of protected characteristics, opt-out available |
| **Lack of Consent (Unconscious Processing)** | Low | Medium | LOW | Consent management dashboard, granular controls |
| **Data Retention Violation (Over-Retention)** | Low | Medium | LOW | Automated deletion scripts, DPO quarterly audits |

### 6.2 Technical Safeguards

#### **6.2.1 Encryption**

**At Rest:**
- **Database:** AWS RDS PostgreSQL with AES-256 encryption
- **File Storage:** AWS S3 with server-side encryption (SSE-S3)
- **KYC Documents:** AES-256 with customer-managed keys (CMK) via AWS KMS
- **Backups:** Encrypted with same or stronger algorithm

**In Transit:**
- **API Calls:** TLS 1.3 (minimum TLS 1.2)
- **Mobile App:** Certificate pinning to prevent MitM attacks
- **Third-Party APIs:** Mutual TLS (mTLS) for payment gateways

**At Use (in application):**
- **Database Connections:** Encrypted with TLS
- **Application Logs:** Sensitive data (IDs, financial) masked before logging

#### **6.2.2 Access Controls**

**Role-Based Access Control (RBAC):**
```
┌─────────────────────────────────────────────────────────────┐
│                    Access Control Matrix                     │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  Data Category          │ CEO │ CTO │ CCO │ Dev │ Support │
│  ─────────────────────────────────────────────────────────  │
│  Basic User Info        │  ✓  │  ✓  │  ✓  │  ✗  │   ✓     │
│  KYC Documents          │  ✓  │  ✗  │  ✓  │  ✗  │   ✗     │
│  Biometric Templates    │  ✗  │  ✗  │  ✓  │  ✗  │   ✗     │
│  Financial Data         │  ✓  │  ✓  │  ✓  │  ✗  │   ✗     │
│  Transaction History    │  ✓  │  ✓  │  ✓  │  ✗  │   ✓     │
│  Messages (Encrypted)   │  ✗  │  ✗  │  ✗  │  ✗  │   ✗*    │
│  Trust Score Algorithm  │  ✓  │  ✓  │  ✗  │  ✓  │   ✗     │
│  System Logs            │  ✓  │  ✓  │  ✓  │  ✓  │   ✗     │
│                                                             │
│  * Support can view metadata only (date, parties), not body │
└─────────────────────────────────────────────────────────────┘
```

**Authentication:**
- **Employees:** Multi-factor authentication (MFA) mandatory
- **Service Accounts:** Credential rotation every 90 days
- **API Keys:** Hashed and stored in secrets manager (AWS Secrets Manager)

**Audit Logging:**
- All data access logged (who, what, when, why)
- Logs stored in tamper-proof system (AWS CloudTrail)
- DPO reviews access logs monthly for anomalies
- Alerts for unusual patterns (e.g., bulk data download)

#### **6.2.3 Anonymization and Pseudonymization**

**Techniques Used:**
1. **K-Anonymity:** Analytics datasets ensure k≥5 (no individual identifiable in group <5)
2. **Data Masking:** Show only last 4 digits of phone/ID numbers in logs
3. **Aggregation:** Reports show only aggregated statistics (no individual-level)
4. **Tokenization:** Replace sensitive IDs with random tokens in non-production environments

**Example (Analytics Query):**
```sql
-- BAD: Individual-level query (privacy risk)
SELECT name, transaction_amount, service_category 
FROM transactions 
WHERE user_id = 12345;

-- GOOD: Aggregated query (privacy-preserving)
SELECT service_category, 
       AVG(transaction_amount) as avg_amount,
       COUNT(*) as count
FROM transactions
GROUP BY service_category
HAVING COUNT(*) >= 5;  -- Ensure k-anonymity
```

#### **6.2.4 Data Minimization**

**Principles:**
- Only collect data necessary for stated purpose
- Progressive disclosure (Tier 0 → 1 → 2 → 3 as needed)
- No "nice-to-have" data collection
- Regular audits to identify unused data fields (delete if not used in 12 months)

**Examples:**
- ❌ **Don't Collect:** Social media handles (unless user volunteers for verification)
- ❌ **Don't Collect:** Passport number if Driver's License already provided
- ✅ **Collect:** Minimum data for each KYC tier

---

### 6.3 Organizational Safeguards

#### **6.3.1 Data Protection Officer (DPO)**

**Role and Responsibilities:**
- **Name:** [To be appointed]
- **Reporting:** Directly to CEO, independent from operations
- **NPC Registration:** Registered as DPO within 30 days of platform launch

**Duties:**
- Monitor compliance with Data Privacy Act
- Conduct Privacy Impact Assessments (DPIAs)
- Serve as point of contact for NPC
- Handle data subject requests (access, deletion, etc.)
- Conduct employee privacy training
- Investigate privacy incidents and breaches
- Maintain data processing register

**Contact:**
- Email: dpo@aitrustrade.ph
- Phone: [To be assigned]
- Office Hours: Monday-Friday, 9AM-5PM

#### **6.3.2 Privacy Policies and Notices**

**Documents:**
1. **Privacy Policy** (Comprehensive, legal document)
   - Covers all processing activities
   - Available at signup and in-app (always accessible)
   - Updated annually or when processing changes
   - Versions archived (users can see what they consented to)

2. **Privacy Notice** (Short, user-friendly summary)
   - One-pager highlighting key points
   - Provided at registration
   - Available in English and Filipino

3. **Cookie Policy** (For web platform)
   - Explains cookies and tracking technologies
   - Consent banner on first visit

**Plain Language Commitment:**
- No legal jargon (Flesch Reading Ease score >60)
- Tagalog translation for Filipino users
- Icons and infographics for visual clarity

#### **6.3.3 Employee Training**

**Privacy Training Program:**
- **Onboarding:** All new employees complete 2-hour privacy training
- **Annual Refresher:** 1-hour update on new regulations and incidents
- **Role-Specific:** Compliance team (8 hours), Developers (4 hours)
- **Certification:** Employees acknowledge understanding (signed)

**Topics Covered:**
- Data Privacy Act overview
- User rights and how to respond
- Data handling best practices (no screenshots, no email of IDs)
- Incident reporting procedures
- Consequences of violations (disciplinary action, legal liability)

#### **6.3.4 Vendor Management**

**Third-Party Processors:**
- Payment gateways (PayMongo, GCash, PayMaya, DragonPay)
- Cloud provider (AWS)
- SMS provider (Twilio)
- Email provider (SendGrid)
- Analytics (Firebase, Mixpanel)

**Data Processing Agreements (DPAs):**
- **Required:** All vendors sign DPA before data sharing
- **Contents:** Processing scope, security measures, sub-processors, liability, audit rights
- **Review:** Legal and DPO review before signing
- **Termination Clause:** Data return/deletion upon contract end

**Vendor Audits:**
- Annual compliance questionnaire
- SOC 2 Type II reports (for critical vendors)
- On-site audit (if high-risk processing)

#### **6.3.5 Breach Notification Plan**

**Personal Data Breach Definition (DPA Section 13):**
Unauthorized or accidental:
- Access (someone views data without permission)
- Disclosure (data sent to wrong person)
- Loss (data deleted or becomes inaccessible)
- Destruction (data permanently destroyed)
- Alteration (data modified without authorization)

**Notification Timeline:**

| Severity | Example | NPC Notification | User Notification |
|----------|---------|------------------|-------------------|
| **Critical** | Biometric data exposed | Within 72 hours | Immediately |
| **High** | KYC documents leaked | Within 72 hours | Within 5 days |
| **Medium** | Transaction data accessed by unauthorized employee | Within 72 hours | Within 7 days |
| **Low** | Anonymized analytics data exposed | No notification (unless high risk to users) | No notification |

**Breach Response Protocol:**
1. **Contain (0-1 hour):**
   - Isolate affected systems
   - Revoke compromised credentials
   - Engage cybersecurity team

2. **Assess (1-4 hours):**
   - Determine scope (how many users, what data)
   - Evaluate risk to users (identity theft, fraud, discrimination)
   - Document timeline and root cause

3. **Notify (4-72 hours):**
   - NPC notification via prescribed form
   - User notification via email/SMS/in-app
   - Public disclosure (if >1,000 users affected)
   - Regulatory reporting (BSP, SEC if relevant)

4. **Remediate (1-30 days):**
   - Implement fixes (patch vulnerabilities)
   - Offer remedies to users (credit monitoring, password reset)
   - Update policies and training
   - DPO post-mortem report

**User Notification Template:**
```
Subject: Important Security Notice - Your AI TrustTrade Account

Dear [User Name],

We are writing to inform you of a security incident involving your personal data.

WHAT HAPPENED:
On [Date], we discovered that [brief description of incident].

WHAT DATA WAS AFFECTED:
The following data may have been accessed: [specific data types].

WHAT WE'RE DOING:
We have [containment steps]. We are working with [authorities/vendors] to [remediation].

WHAT YOU SHOULD DO:
We recommend that you [specific actions, e.g., change password, monitor account].

If you have any questions, please contact our Data Protection Officer at dpo@aitrustrade.ph or +63 [phone].

We sincerely apologize for this incident and are committed to protecting your privacy.

Sincerely,
AI TrustTrade Data Protection Officer
```

---

## 7. Data Sharing and Transfers

### 7.1 Third-Party Data Sharing

**Categories of Recipients:**

| Recipient Category | Purpose | Data Shared | Safeguards |
|-------------------|---------|-------------|-----------|
| **Payment Gateways** | Process payments | Name, phone, email, payment method, transaction amount | DPA, PCI-DSS, encryption |
| **Insurance Partners** | Underwrite policies | Service type, transaction amount, Trust Score | DPA, aggregated data where possible |
| **Cloud Provider (AWS)** | Host infrastructure | All data (encrypted) | DPA, ISO 27001, data localization |
| **Analytics Providers** | Platform improvement | Anonymized behavioral data | DPA, no PII sharing, k-anonymity |
| **Regulators (BSP, SEC, AMLC)** | Compliance | KYC, transaction data (upon request) | Legal obligation, secure channels |
| **Law Enforcement** | Investigations | Relevant data (with warrant or subpoena) | Legal obligation, DPO review |

**No Data Sharing Without:**
- Data Processing Agreement (DPA) signed
- Legitimate purpose (contractual, legal, consent)
- Adequate safeguards (encryption, access controls)
- User notification (unless prohibited by law)

### 7.2 Cross-Border Data Transfers

**Current Status:**
- **Data Localization:** All production data stored in Philippines
- **Cloud Region:** AWS Asia Pacific (Singapore) - **NOT PHILIPPINES**
  - This is a **cross-border transfer** requiring NPC notification
  - Singapore has adequate data protection (PDPA 2012, equivalent to PH DPA)
  
**NPC Notification:**
- **Requirement:** Notify NPC of cross-border transfers (DPA Section 25)
- **Timing:** Before platform launch
- **Form:** NPC prescribed form for cross-border data transfers
- **Justification:** AWS does not have a data center in Philippines; Singapore is nearest with adequate protection

**Safeguards for AWS Singapore:**
- AWS GDPR-compliant (adequacy recognized by EU)
- Standard Contractual Clauses (SCC) in AWS Customer Agreement
- Data encrypted in transit and at rest
- Philippine law governs (jurisdiction clause in DPA)
- Right to audit AWS (via SOC 2 reports)

**Future Consideration:**
- Migrate to AWS Asia Pacific (Manila) when available (planned 2026)
- This would eliminate cross-border transfer concern

---

## 8. Algorithmic Decision-Making (AI Trust Score)

### 8.1 Trust Score as Automated Decision

**Nature of Processing:**
- **Algorithm:** Machine learning model + rule-based scoring
- **Impact:** Affects transaction limits, service matching, reputation
- **Human Oversight:** Users can request manual review of score

**Privacy Concerns:**
1. **Lack of Transparency:** "Black box" algorithms
2. **Discrimination:** Bias against certain groups
3. **Inaccuracy:** Errors in data or model
4. **No Recourse:** Automated rejection without explanation

### 8.2 Safeguards for Algorithmic Fairness

#### **Transparency**
- **Score Breakdown:** Users can see 7 factors and their weights
- **Explainability:** In-app "Why this score?" feature with plain language
- **Algorithm Documentation:** Published on website (high-level, no proprietary details)

**Example Explanation:**
```
Your Trust Score: 785 / 1000

Breakdown:
✓ Skill Verification: 200/250 (Good - ID and certifications verified)
✓ Completion Rate: 180/200 (Excellent - 95% of jobs completed on time)
⚠ Customer Reviews: 140/200 (Fair - Average 4.2★, improve quality)
✓ Response Speed: 150/150 (Excellent - Average 2 hours)
✓ Dispute History: 90/100 (Good - 1 resolved dispute)
✓ Transaction Volume: 50/100 (Low - Only 5 transactions so far)

How to improve:
- Complete more jobs to increase Transaction Volume
- Maintain high quality to improve Customer Reviews
```

#### **Non-Discrimination**
- **Prohibited Factors:** No use of race, religion, gender, age, disability, etc.
- **Bias Testing:** Quarterly audits to check for disparate impact
  - Example: Ensure Trust Score distribution is similar across demographic groups
- **Fairness Metrics:** Statistical parity, equal opportunity, equalized odds

**Bias Audit Process:**
```python
# Pseudocode for bias detection
def audit_trust_score_bias(scores, demographics):
    for demographic_var in ['age_group', 'gender', 'region']:
        # Check if score distribution differs significantly by demographic
        p_value = statistical_test(scores, demographics[demographic_var])
        if p_value < 0.05:  # Significant difference detected
            alert_dpo(f"Potential bias detected in {demographic_var}")
            investigate_and_remediate()
```

#### **Right to Human Review**
- **Request Process:** In-app button "Challenge Trust Score"
- **Review Team:** Compliance team reviews within 5 business days
- **Outcome:** Score adjusted if error found, explanation provided if valid
- **Appeal:** User can escalate to DPO if unsatisfied

#### **Regular Model Audits**
- **Frequency:** Quarterly (by internal data science team) + Annual (external auditor)
- **Scope:** Accuracy, fairness, explainability, data quality
- **Remediation:** Model retraining or adjustment if issues found

---

## 9. Data Retention and Deletion

### 9.1 Retention Schedule

| Data Category | Retention Period | Justification | Deletion Method |
|--------------|------------------|---------------|----------------|
| **Basic User Info** | 5 years post-closure | AML requirement | Secure deletion (3-pass overwrite) |
| **KYC Documents** | 10 years post-closure | AML/CFT, tax | Secure deletion + log retention |
| **Biometric Templates** | 5 years post-closure (or upon withdrawal of consent) | KYC verification | Cryptographic erasure (delete key) |
| **Transaction Records** | 10 years | BSP, SEC, tax | Archive then secure deletion |
| **Messages** | 2 years | Dispute evidence | Automated purge |
| **Session Logs** | 90 days | Security monitoring | Automated anonymization |
| **Analytics Data** | Indefinite (anonymized) | Business intelligence | N/A (no PII) |
| **Marketing Lists** | Until consent withdrawn | Marketing purposes | Immediate deletion upon opt-out |

### 9.2 Automated Deletion

**Deletion Scripts:**
- **Daily:** Check for expired data (e.g., 90-day logs)
- **Weekly:** Purge messages older than 2 years
- **Monthly:** Review closed accounts approaching retention deadline
- **Annual:** Audit all data for compliance with retention policy

**Deletion Verification:**
- DPO spot-checks deletion logs monthly
- External auditor verifies annually
- Users can request deletion confirmation certificate

**Exceptions (Legal Hold):**
- Ongoing dispute or litigation
- Regulatory investigation
- Criminal investigation (with warrant)

**Legal Hold Process:**
1. Legal/compliance team identifies affected data
2. Flag in database (do not delete)
3. Notify DPO and affected systems
4. Release hold when resolved (with documentation)

---

## 10. User Interface and Experience

### 10.1 Privacy-Friendly UI Design

**Principles:**
- **Privacy by Default:** Most privacy-protective settings enabled by default
- **Just-in-Time Consent:** Ask for permissions when needed (not all at once)
- **Clear Language:** No confusing toggles or double negatives
- **Easy Access:** Privacy settings accessible in ≤3 taps/clicks

**Examples:**

**Good (Privacy-Friendly):**
```
[ ] Send me promotional emails (optional)
    You'll still receive important account notifications.
```

**Bad (Dark Pattern):**
```
[X] I don't want to miss out on exclusive offers! (pre-checked)
    Opt-out of emails (you may miss important updates)
```

### 10.2 Privacy Dashboard

**Features:**
- **My Data:** View all collected data
- **Download Data:** Export in JSON/CSV/PDF
- **Delete Account:** Initiate deletion request
- **Manage Consent:** Granular controls for each processing purpose
- **Access Log:** See who accessed my data (support, compliance)
- **Data Requests:** Track status of access/deletion requests

**Screenshot (Conceptual):**
```
┌─────────────────────────────────────────────────────────────┐
│                   Privacy & Data Settings                    │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  📊 My Data                                                 │
│      View all personal data we have about you               │
│      [View My Data]                                         │
│                                                             │
│  ⬇️  Download Data                                          │
│      Export your data in machine-readable format            │
│      [Download JSON] [Download CSV]                         │
│                                                             │
│  🗑️  Delete Account                                         │
│      Permanently delete your account and data               │
│      [Delete Account] (requires verification)               │
│                                                             │
│  ✅ Manage Consent                                          │
│      [ ] Marketing emails and SMS                           │
│      [✓] Behavioral analytics (for personalization)         │
│      [✓] Location tracking (during service only)            │
│                                                             │
│  📜 Privacy Policy                                          │
│      Last updated: Nov 15, 2025                             │
│      [Read Full Policy]                                     │
│                                                             │
│  📧 Contact DPO                                             │
│      Questions? Email dpo@aitrustrade.ph                    │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

---

## 11. NPC Compliance and Reporting

### 11.1 NPC Registration

**Requirements:**
- **Personal Information Controller (PIC) Registration:** Within 30 days of platform launch
- **DPO Registration:** Concurrent with PIC registration
- **Annual Report:** Due January 31st of each year

**Documents to Submit:**
- PIC registration form
- DPO appointment letter
- Data processing register (inventory of all processing activities)
- Privacy policy and notices
- Data breach incident report (if any)

### 11.2 Data Processing Register

**Contents (per NPC requirements):**

| Field | Description | Example |
|-------|-------------|---------|
| **Processing Activity** | Name and purpose | "KYC Verification for AML Compliance" |
| **Legal Basis** | Justification | "Legal Obligation (BSP Circular 1022)" |
| **Data Categories** | Types of personal data | "Name, ID number, address, photo" |
| **Data Subjects** | Who | "Service providers (Tier 1-3)" |
| **Recipients** | Who data is shared with | "BSP, AMLC (upon request)" |
| **Retention Period** | How long stored | "10 years post-account closure" |
| **Security Measures** | Safeguards | "AES-256 encryption, access controls, MFA" |
| **Cross-Border Transfers** | Countries | "Singapore (AWS), with SCC and NPC notification" |

**Maintenance:**
- Updated quarterly by DPO
- Reviewed annually by external auditor
- Available for NPC inspection upon request

### 11.3 Incident Reporting to NPC

**Reportable Incidents:**
- Any personal data breach (as defined in Section 11.6.3.5)
- Especially if high risk to data subjects

**Report Contents:**
- Nature of breach (what happened)
- Data categories affected (what data)
- Estimated number of affected data subjects (how many people)
- Likely consequences (risk assessment)
- Measures taken to mitigate (remediation)
- Contact details (DPO name, email, phone)

**Submission:**
- **Method:** NPC online reporting portal (https://privacy.gov.ph)
- **Timing:** Within 72 hours of breach discovery
- **Follow-Up:** Additional information if initially incomplete

---

## 12. Privacy Impact Summary

### 12.1 Residual Risks (After Safeguards)

| Risk | Pre-Mitigation | Post-Mitigation | Residual Risk | Acceptable? |
|------|---------------|----------------|--------------|-------------|
| **KYC Data Breach** | HIGH | MEDIUM-LOW | Encryption + access controls reduce but don't eliminate risk | ✅ Yes |
| **Biometric Misuse** | CRITICAL | LOW | One-way hashing + consent + no third-party sharing | ✅ Yes |
| **Algorithmic Bias** | MEDIUM-HIGH | MEDIUM | Ongoing audits + human review option | ✅ Yes |
| **Re-identification** | MEDIUM | LOW | K-anonymity + aggregation | ✅ Yes |
| **Cross-Border Data Leak** | MEDIUM | LOW | AWS SCC + data localization plan | ✅ Yes |

### 12.2 Overall Privacy Impact Rating

**Assessment:** MEDIUM RISK (Acceptable with identified safeguards)

**Justification:**
- High-risk processing (biometric, financial, profiling) is present
- BUT comprehensive technical and organizational safeguards implemented
- Clear legal basis for all processing
- Strong user rights mechanisms in place
- DPO oversight and regular audits
- Transparent and accountable approach

**Recommendation:** Proceed with planned processing activities, subject to:
1. ✅ Implementation of all technical safeguards (encryption, access controls)
2. ✅ Appointment of qualified DPO before launch
3. ✅ NPC registration and cross-border transfer notification
4. ✅ Quarterly privacy audits by DPO
5. ✅ Annual external privacy audit
6. ✅ User education on privacy rights and controls

---

## 13. Continuous Monitoring and Review

### 13.1 Quarterly Privacy Reviews

**DPO Checklist:**
- [ ] Review data processing register for new activities
- [ ] Audit access logs for anomalies
- [ ] Test data subject request processes (mystery shop)
- [ ] Review and update privacy policy if needed
- [ ] Conduct employee privacy training refresher
- [ ] Verify automated deletion scripts are functioning
- [ ] Check for new NPC guidance or regulations
- [ ] Report findings to CEO and Board

### 13.2 Annual External Audit

**Scope:**
- Compliance with Data Privacy Act 2012
- Adequacy of technical and organizational safeguards
- Effectiveness of user rights mechanisms
- Privacy policy accuracy and transparency
- Vendor management and DPAs

**Auditor:**
- External firm (e.g., Big 4 accounting/consulting)
- Independence verified (no conflict of interest)
- Report submitted to NPC if required

### 13.3 Privacy by Design in New Features

**Process:**
1. Product team proposes new feature
2. DPO reviews for privacy implications (mini-DPIA)
3. Identify data minimization opportunities
4. Implement privacy-friendly defaults
5. Test user consent flows
6. Update privacy policy and notices
7. Deploy with privacy controls

**Example (Future Feature: Video Chat):**
- Privacy concern: Recording video calls
- Data minimization: Only record if user explicitly opts-in (e.g., for dispute evidence)
- Default: No recording
- Transparency: Clear indicator when recording
- Retention: Delete after 30 days (unless dispute)
- User control: Users can delete recording anytime

---

## 14. Conclusion and Next Steps

### 14.1 Key Findings

**Strengths:**
✅ Comprehensive privacy safeguards planned
✅ Clear legal basis for all processing
✅ Strong user rights mechanisms
✅ Privacy by design approach
✅ Dedicated DPO and compliance structure

**Areas for Improvement:**
⚠️ Cross-border data transfer (AWS Singapore) - mitigate with NPC notification and future migration to Manila
⚠️ Algorithmic bias in Trust Score - ongoing audits and human review
⚠️ Insider threat - enhanced employee monitoring and training

### 14.2 Recommendations

**Before Platform Launch:**
1. ✅ Appoint and register DPO with NPC
2. ✅ Complete NPC PIC registration
3. ✅ Notify NPC of cross-border data transfer (AWS Singapore)
4. ✅ Finalize privacy policy and notices (English + Filipino)
5. ✅ Implement all technical safeguards (encryption, access controls, etc.)
6. ✅ Conduct employee privacy training
7. ✅ Execute Data Processing Agreements (DPAs) with all vendors
8. ✅ Test data subject request workflows (access, deletion)
9. ✅ Set up breach notification procedures and contacts
10. ✅ Conduct final privacy review with legal counsel

**Within 3 Months of Launch:**
1. ✅ First quarterly DPO audit
2. ✅ Review user feedback on privacy controls
3. ✅ Analyze privacy-related support tickets
4. ✅ Conduct bias audit on Trust Score algorithm
5. ✅ Submit quarterly report to BSP/SEC (if required)

**Within 12 Months (Sandbox End):**
1. ✅ Annual external privacy audit
2. ✅ Comprehensive DPIA update based on actual data
3. ✅ Plan migration to AWS Manila (if available)
4. ✅ Evaluate need for additional privacy certifications (ISO 27701)

### 14.3 Sign-Off

This DPIA was prepared by:
- **Data Protection Officer:** [Name], [Date]
- **Reviewed by Legal Counsel:** [Name], [Date]
- **Approved by CEO:** [Name], [Date]

**Next Review Date:** [3 months after launch, then annually]

**Document Version:** 1.0  
**Date:** November 2025  
**Status:** For NPC and BSP TRISD Review  
**Classification:** Confidential

---

## Appendices

### Appendix A: Privacy Policy Outline

1. Introduction and Contact Information
2. Data We Collect (by category)
3. Why We Collect Data (purposes and legal basis)
4. How We Use Data (processing activities)
5. Who We Share Data With (third parties)
6. Your Privacy Rights (access, deletion, etc.)
7. Data Security (technical and organizational measures)
8. Data Retention (how long we keep data)
9. Cross-Border Transfers (AWS Singapore)
10. Children's Privacy (no users under 18)
11. Changes to Privacy Policy (notification process)
12. Contact Us (DPO email and phone)

### Appendix B: User Consent Form (Biometric Data)

```
┌─────────────────────────────────────────────────────────────┐
│           Biometric Data Consent Form                        │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  To verify your identity and prevent fraud, we use facial   │
│  recognition technology to match your selfie with your ID.  │
│                                                             │
│  What we do:                                                │
│  • Take your selfie photo (liveness detection)              │
│  • Create a biometric template (mathematical representation)│
│  • Compare template to your ID photo                        │
│  • Store hashed template (cannot recreate your face)        │
│                                                             │
│  Your rights:                                               │
│  • Withdraw consent anytime (Settings > Privacy)            │
│  • Request deletion of biometric data                       │
│  • This may limit your access to certain features           │
│                                                             │
│  [ ] I consent to the use of facial recognition for         │
│      identity verification purposes.                        │
│                                                             │
│  [Learn More] [Decline] [Accept]                            │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

### Appendix C: Data Subject Request Form

**Request Type:** (select one)
- [ ] Access my personal data
- [ ] Correct inaccurate data
- [ ] Delete my account and data
- [ ] Export my data (portability)
- [ ] Object to processing (opt-out)
- [ ] Withdraw consent

**Your Details:**
- Name: ________________
- Email: ________________
- Phone: ________________
- User ID: ________________

**Details of Request:**
[Text box for specific request]

**Verification:**
I confirm that I am the account holder and authorize this request.

**Signature:** ________________  **Date:** ________________

**Submit to:** dpo@aitrustrade.ph or mail to [Address]

### Appendix D: Glossary

- **Consent:** Freely given, specific, informed agreement to processing
- **Data Controller:** Entity that determines purposes and means of processing (AI TrustTrade)
- **Data Processor:** Entity that processes data on behalf of controller (AWS, gateways)
- **Data Subject:** Individual whose personal data is processed (users)
- **DPA:** Data Privacy Act of 2012 (RA 10173)
- **DPO:** Data Protection Officer
- **DPIA:** Data Privacy Impact Assessment (this document)
- **NPC:** National Privacy Commission
- **Personal Data:** Any information relating to identified or identifiable individual
- **Processing:** Any operation performed on data (collection, storage, use, deletion)
- **Pseudonymization:** Replacing identifying fields with artificial identifiers
- **Sensitive Personal Information:** Race, health, biometrics, etc. (requires extra care)

---

**END OF DOCUMENT**

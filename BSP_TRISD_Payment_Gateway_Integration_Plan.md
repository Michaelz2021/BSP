# AI TrustTrade Payment Gateway Integration Plan
## For BSP TRISD Meeting

**Prepared by:** NS Solution Corp  
**Date:** November 2025  
**Purpose:** Technical Review - Payment Infrastructure & Gateway Integration

---

## Executive Summary

AI TrustTrade implements a **multi-gateway payment architecture** that provides redundancy, cost optimization, and comprehensive coverage of Philippine payment methods. The platform integrates with 4 primary payment gateways and 3 backup providers to ensure 99.9% payment availability while maintaining BSP-compliant fund segregation and security standards.

**Key Features:**
- **Smart Payment Routing** - Automatic gateway selection based on cost, speed, and success rate
- **Multi-Channel Support** - E-wallets, cards, bank transfers, over-the-counter
- **Real-Time Settlement Tracking** - Complete visibility into fund movements
- **PCI-DSS Level 1 Compliance** - Industry-leading security standards
- **BSP Trust Account Integration** - Seamless fund flow to segregated accounts

---

## 1. Payment Gateway Strategy

### 1.1 Multi-Gateway Approach

**Rationale:**
- **Redundancy:** No single point of failure
- **Cost Optimization:** Route transactions through lowest-fee gateway
- **Coverage:** Support all popular Philippine payment methods
- **Negotiating Power:** Competitive rates through multi-provider relationships

**Gateway Selection Criteria:**
1. **Regulatory Compliance** - BSP-authorized payment service providers
2. **Reliability** - 99.9%+ uptime SLA
3. **Cost Structure** - Competitive transaction fees
4. **Settlement Speed** - T+0 to T+2 maximum
5. **API Quality** - Well-documented RESTful APIs with webhooks
6. **Support** - 24/7 technical and merchant support

---

## 2. Primary Payment Gateways

### 2.1 PayMongo (Credit/Debit Cards)

**Provider:** PayMongo Philippines, Inc. (BSP-registered ROPPS)

**Use Cases:**
- Credit card payments (Visa, Mastercard, JCB)
- Debit card payments
- International cards (for expat users)

**Integration Details:**

**API Type:** RESTful API  
**Authentication:** Secret API keys (sandbox + live)  
**Webhook Support:** Yes (payment.paid, payment.failed, source.chargeable)

**Key Endpoints:**
```
POST /v1/payment_intents          # Create payment intent
POST /v1/payment_methods          # Create payment method
POST /v1/sources                  # Create payment source
GET  /v1/payments/:id             # Retrieve payment status
POST /v1/refunds                  # Process refunds
GET  /v1/webhooks                 # Webhook management
```

**Transaction Flow:**
```
1. User selects card payment
   â†"
2. Platform creates PayMongo PaymentIntent
   â†"
3. User enters card details (hosted on PayMongo secure page)
   â†"
4. 3D Secure authentication (OTP from bank)
   â†"
5. PayMongo processes payment
   â†"
6. Webhook notification to platform
   â†"
7. Funds settle to AI TrustTrade merchant account (T+2)
   â†"
8. Platform transfers to Trust Account
```

**Fees:**
- **Transaction Fee:** 2.9% + ₱15 per successful transaction
- **International Cards:** 3.9% + ₱15
- **Refund Fee:** ₱0 (free)
- **Chargeback Fee:** ₱500 per dispute

**Settlement:**
- **Timeline:** T+2 business days
- **Destination:** AI TrustTrade merchant bank account (BDO)
- **Batch:** Daily automated settlement
- **Minimum:** No minimum amount

**Transaction Limits:**
- **Per Transaction:** ₱500,000 maximum
- **Daily:** ₱5,000,000 per merchant account
- **Monthly:** ₱50,000,000

**Security Features:**
- PCI-DSS Level 1 certified
- 3D Secure (3DS) mandatory for all transactions
- Tokenization (card details never stored on platform)
- TLS 1.3 encryption
- Real-time fraud detection

**Error Handling:**
- Insufficient funds â†' Notify user, suggest alternative
- Declined card â†' Retry with different card or method
- Gateway timeout â†' Automatic retry (3x), then fallback to Xendit
- Webhook failure â†' Queue for manual reconciliation

---

### 2.2 GCash for Business (E-Wallet)

**Provider:** Mynt - Globe Fintech Innovations, Inc. (BSP EMI License)

**Use Cases:**
- GCash wallet payments (largest e-wallet in PH, 80M+ users)
- QR code payments
- Instant wallet top-up

**Integration Details:**

**API Type:** RESTful API  
**Authentication:** OAuth 2.0 (merchant credentials)  
**SDK:** GCash Lite SDK for mobile apps

**Key Endpoints:**
```
POST /oauth/v2/token              # Get access token
POST /v2/payments                 # Create payment
GET  /v2/payments/:refNo          # Check payment status
POST /v2/refunds                  # Process refunds
```

**Transaction Flow:**
```
1. User selects GCash payment
   â†"
2. Platform creates GCash payment request
   â†"
3. User redirects to GCash app (or web if not installed)
   â†"
4. User enters MPIN/biometric in GCash
   â†"
5. GCash deducts from user wallet
   â†"
6. Callback notification to platform
   â†"
7. Funds settle to AI TrustTrade GCash Business account (T+1)
   â†"
8. Platform withdraws to Trust Account bank
```

**Fees:**
- **Transaction Fee:** 2.5% per successful transaction
- **Wallet Top-Up (User → GCash):** Free for users
- **Withdrawal (Merchant → Bank):** ₱15 per withdrawal
- **Refund Fee:** ₱0 (free)

**Settlement:**
- **Timeline:** T+1 business day
- **Destination:** AI TrustTrade GCash Business Wallet
- **Withdrawal to Bank:** Manual (daily batch), T+1
- **Minimum Withdrawal:** ₱500

**Transaction Limits:**
- **Per Transaction:** ₱100,000 (fully verified users)
- **Daily:** ₱100,000 per user
- **Monthly:** ₱100,000 per user (basic), ₱500,000 (fully verified)

**Security Features:**
- MPIN authentication
- Biometric login
- Device binding
- Transaction alerts (SMS + push)
- Anti-fraud monitoring

**User Experience:**
- **Mobile App:** Deep link to GCash app, seamless return
- **Web:** Redirect to GCash web login
- **QR Code:** Generate QR for manual scanning

---

### 2.3 PayMaya Checkout (E-Wallet)

**Provider:** PayMaya Philippines, Inc. (BSP EMI License)

**Use Cases:**
- PayMaya wallet payments (2nd largest e-wallet, 50M+ users)
- PayMaya card payments (virtual/physical Visa cards)
- Alternative to GCash

**Integration Details:**

**API Type:** RESTful API  
**Authentication:** API keys (public + secret)  
**SDK:** PayMaya Checkout SDK

**Key Endpoints:**
```
POST /checkout/v1/checkouts       # Create checkout session
GET  /checkout/v1/checkouts/:id   # Get checkout status
POST /payments/v1/payment-tokens  # Tokenize payment method
POST /payments/v1/payments        # Execute payment
GET  /payments/v1/payments/:id    # Get payment details
POST /payments/v1/refunds         # Process refunds
```

**Transaction Flow:**
```
1. User selects PayMaya payment
   â†"
2. Platform creates PayMaya checkout session
   â†"
3. User redirects to PayMaya checkout page
   â†"
4. User logs in and selects wallet or card
   â†"
5. User enters PIN/OTP
   â†"
6. PayMaya processes payment
   â†"
7. Webhook notification to platform
   â†"
8. Funds settle to AI TrustTrade PayMaya merchant account (T+1)
   â†"
9. Platform transfers to Trust Account bank
```

**Fees:**
- **E-Wallet Transaction:** 2.9% per successful transaction
- **Card Transaction:** 3.5% + ₱10
- **Refund Fee:** ₱0 (free, deducted from settlement)
- **Chargeback Fee:** ₱500 per dispute

**Settlement:**
- **Timeline:** T+1 business day
- **Destination:** AI TrustTrade merchant bank account (BPI)
- **Batch:** Daily automated settlement
- **Minimum:** ₱100

**Transaction Limits:**
- **Per Transaction:** ₱100,000
- **Daily:** Merchant-specific (negotiated)
- **Monthly:** ₱10,000,000

**Security Features:**
- PCI-DSS Level 1 (for card mode)
- PIN + OTP authentication
- 3D Secure for cards
- Tokenization
- Fraud scoring

---

### 2.4 DragonPay (Bank Transfers & OTC)

**Provider:** DragonPay Corporation (BSP-authorized Third Party Processor)

**Use Cases:**
- InstaPay (real-time bank transfers)
- PESONet (next-day bank transfers)
- Over-the-counter (7-Eleven, MLhuillier, Bayad Center, etc.)
- Online banking

**Integration Details:**

**API Type:** RESTful API + Legacy SOAP  
**Authentication:** Merchant ID + Password Hash  
**Webhook Support:** Yes (HTTP POST callback)

**Key Endpoints:**
```
POST /api/collect/v1              # Create payment request
GET  /api/collect/v1/:txnid       # Query transaction status
POST /api/collect/v1/cancel       # Cancel pending transaction
```

**Transaction Flow (InstaPay):**
```
1. User selects bank transfer
   â†"
2. Platform generates payment reference number
   â†"
3. User redirects to bank's online banking
   â†"
4. User authorizes InstaPay transfer
   â†"
5. Real-time transfer to DragonPay account
   â†"
6. Callback notification to platform (within 5 minutes)
   â†"
7. Funds settle to AI TrustTrade account (T+0 or T+1)
```

**Transaction Flow (OTC):**
```
1. User selects OTC payment
   â†"
2. Platform generates payment barcode + reference
   â†"
3. User goes to 7-Eleven/Bayad Center/MLhuillier
   â†"
4. Cashier scans barcode, user pays cash
   â†"
5. Notification to DragonPay (real-time)
   â†"
6. Callback to platform (within 1 hour)
   â†"
7. Funds settle to AI TrustTrade account (T+1)
```

**Fees:**
- **InstaPay:** ₱10 flat fee per transaction
- **PESONet:** ₱10 flat fee per transaction
- **OTC (7-Eleven):** ₱15 convenience fee
- **OTC (Bayad Center):** ₱20 convenience fee
- **Online Banking:** ₱10-25 (bank-dependent)

**Settlement:**
- **Timeline:** T+0 (InstaPay), T+1 (others)
- **Destination:** AI TrustTrade merchant bank account (UnionBank)
- **Batch:** Multiple daily settlements
- **Minimum:** ₱1

**Transaction Limits:**
- **InstaPay:** ₱50,000 per transaction
- **PESONet:** ₱1,000,000 per transaction
- **OTC:** Typically ₱10,000-40,000 (location-dependent)

**Security Features:**
- Bank-grade security (for bank transfers)
- SMS confirmation for OTC
- Unique reference numbers (no duplicate payments)
- Fraud monitoring

---

## 3. Backup Payment Gateways

### 3.1 Xendit (Redundancy Provider)

**Purpose:** Backup for PayMongo and primary gateways

**Coverage:**
- Credit/debit cards (Visa, Mastercard)
- E-wallets (GCash, PayMaya, GrabPay)
- Bank transfers (InstaPay, PESONet)
- Over-the-counter (Alfamart, Indomaret - future expansion)

**Trigger Conditions:**
- Primary gateway downtime (>5 minutes)
- Primary gateway high decline rate (>20%)
- Transaction amount exceeds primary gateway limits
- User preference (if saved on Xendit)

**Fees:** 2.9% + ₱15 (cards), 2.5%-3.0% (e-wallets)

---

### 3.2 Paymaya Enterprise (High-Volume Backup)

**Purpose:** Enterprise-level integration for high transaction volumes

**Use Case:** Corporate clients, bulk payments

**Benefits:**
- Customized fee structure (volume discounts)
- Dedicated account manager
- Priority support
- Higher transaction limits

**Activation:** When monthly GMV exceeds ₱50M

---

### 3.3 Coins.ph (Future - Crypto Option)

**Purpose:** Cryptocurrency payments (future feature)

**Coverage:**
- Bitcoin, Ethereum, USDT
- Peso-pegged stablecoins (PHPC)

**Use Case:** International freelancers, remittances

**Status:** Planned for Year 2

---

## 4. Smart Payment Routing System

### 4.1 Routing Algorithm

**Decision Factors (in priority order):**

```python
def select_payment_gateway(payment_method, amount, user_history, time):
    # 1. Method Compatibility
    compatible_gateways = filter_by_method(payment_method)
    
    # 2. Amount Limits
    compatible_gateways = filter_by_limits(compatible_gateways, amount)
    
    # 3. Gateway Availability (99.9% uptime required)
    available_gateways = check_uptime(compatible_gateways)
    
    # 4. Cost Optimization
    if amount > 10000:
        # For large amounts, minimize percentage fees
        available_gateways = sort_by_percentage_fee(available_gateways)
    else:
        # For small amounts, minimize fixed fees
        available_gateways = sort_by_total_cost(available_gateways)
    
    # 5. Success Rate (Historical)
    if user_history.has_failed_recently():
        available_gateways = sort_by_success_rate(available_gateways)
    
    # 6. Settlement Speed (if urgent payout)
    if time.is_urgent():
        available_gateways = sort_by_settlement_speed(available_gateways)
    
    return available_gateways[0]  # Select top-ranked gateway
```

### 4.2 Routing Examples

**Example 1: ₱500 E-Wallet Payment**
- **User selects:** GCash
- **Amount:** ₱500
- **Primary gateway:** GCash for Business (2.5% = ₱12.50)
- **Backup gateway:** Xendit GCash (2.6% = ₱13)
- **Selected:** GCash for Business (lower cost)

**Example 2: ₱50,000 Card Payment**
- **User selects:** Credit Card
- **Amount:** ₱50,000
- **Primary gateway:** PayMongo (2.9% + ₱15 = ₱1,465)
- **Backup gateway:** Xendit (2.9% + ₱15 = ₱1,465)
- **Selected:** PayMongo (higher historical success rate)

**Example 3: Gateway Downtime Scenario**
- **User selects:** PayMaya
- **PayMaya status:** Downtime (maintenance)
- **Fallback:** Inform user, offer alternative (GCash or Card)
- **If user insists:** Queue payment, process when gateway recovers

---

## 5. Fund Flow Architecture

### 5.1 Complete Payment Journey

```
┌─────────────────────────────────────────────────────────────┐
│                   INBOUND PAYMENTS                          │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  User Payment Method                                        │
│       │                                                     │
│       ├── GCash ──────────► GCash Business API             │
│       ├── PayMaya ────────► PayMaya Checkout               │
│       ├── Credit Card ────► PayMongo                       │
│       ├── Bank Transfer ──► DragonPay                      │
│       └── OTC ────────────► DragonPay                      │
│                                 │                           │
│                                 ▼                           │
│                     Gateway Merchant Account                │
│                     (Temporary holding, T+0 to T+2)         │
│                                 │                           │
│                                 ▼                           │
│                   AI TrustTrade Operating Account           │
│                          (BDO/BPI/UnionBank)                │
│                     Daily batch reconciliation              │
│                                 │                           │
│                                 ▼                           │
│  ┌──────────────────────────────────────────────────────┐  │
│  │      BSP-COMPLIANT TRUST ACCOUNT (BPI Trust)         │  │
│  ├──────────────────────────────────────────────────────┤  │
│  │                                                      │  │
│  │  ┌─────────────────┐    ┌────────────────────────┐  │  │
│  │  │ ESCROW SUB-POOL │    │  WALLET SUB-POOL       │  │  │
│  │  ├─────────────────┤    ├────────────────────────┤  │  │
│  │  │ Active Escrows  │    │ User Wallet Balances   │  │  │
│  │  │ Disputed Funds  │    │ Float Reserve (10%)    │  │  │
│  │  │ Reserve (10%)   │    │ Insurance Reserve      │  │  │
│  │  └─────────────────┘    └────────────────────────┘  │  │
│  │                                                      │  │
│  └──────────────────────────────────────────────────────┘  │
│                                 │                           │
│                 [Service Completed & Verified]              │
│                                 │                           │
│                                 ▼                           │
├─────────────────────────────────────────────────────────────┤
│                   OUTBOUND PAYMENTS                         │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│                   Settlement Account                        │
│                      (UnionBank)                            │
│                   Next-day provider payouts                 │
│                          │                                  │
│          ┌───────────────┼───────────────┐                 │
│          ▼               ▼               ▼                 │
│    Provider Wallet  GCash Payout  Bank Transfer            │
│    (Instant)        (Instant)     (T+1)                    │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

### 5.2 Reconciliation Process

**Daily Reconciliation (Automated):**

```
1. Gateway Settlement Reports (Downloaded via API)
   ├── PayMongo: CSV export of T-2 settlements
   ├── GCash: JSON API of T-1 settlements
   ├── PayMaya: CSV export of T-1 settlements
   └── DragonPay: Email report of T-0/T-1 settlements

2. Platform Transaction Log (PostgreSQL Database)
   ├── All payment_intents created
   ├── Webhook notifications received
   └── Manual reconciliation entries

3. Bank Account Statements (API or Manual)
   ├── Operating Account (BDO)
   ├── Trust Account (BPI)
   └── Settlement Account (UnionBank)

4. Automated Matching Algorithm
   ├── Match gateway reports to platform logs (by reference ID)
   ├── Match bank deposits to gateway settlements (by amount + date)
   ├── Flag discrepancies (>₱1 variance or missing transactions)
   └── Generate reconciliation report

5. Manual Review (Compliance Team)
   ├── Investigate flagged discrepancies
   ├── Contact gateway support for missing transactions
   ├── Adjust for timing differences (T+0 vs T+1)
   └── Approve daily reconciliation

6. Trust Account Transfer (Automated)
   ├── Calculate total inbound payments (verified)
   ├── Deduct platform fees (7%)
   ├── Transfer net amount to Trust Account
   └── Update internal ledger (escrow + wallet sub-pools)
```

**Weekly Reconciliation (Manual Audit):**
- Compliance Officer reviews 100% of transactions >₱50,000
- Sample 10% of transactions <₱50,000
- Verify escrow releases match service completions
- Check for unusual patterns (fraud detection)

**Monthly Reconciliation (External Audit):**
- External auditor (Big 4 firm) reviews full month
- Bank statements vs. platform ledger
- Trust Account balance verification
- Discrepancy resolution report to BSP

---

## 6. Integration Security & Compliance

### 6.1 PCI-DSS Level 1 Compliance

**Requirements:**
- Never store raw card numbers (use tokenization)
- All card data processed on gateway-hosted pages
- TLS 1.3 encryption for all API calls
- Regular penetration testing (quarterly)
- Security training for all engineers (annual)

**Implementation:**
- **Hosted Payment Pages:** Users enter card details on PayMongo/PayMaya pages (not our servers)
- **Tokenization:** Store only tokens (e.g., `tok_abc123...`) for recurring payments
- **API Keys:** Stored in environment variables, rotated every 90 days
- **Webhook Validation:** Verify signatures to prevent spoofing
- **Logging:** Mask sensitive data (card numbers, OTPs) in logs

### 6.2 BSP Circular 808 (IT Risk Management)

**Requirements:**
- IT Steering Committee
- Annual IT audit
- Disaster Recovery Plan (RPO <4 hours, RTO <24 hours)
- Incident Management (report within 24 hours)

**Implementation:**
- **IT Steering Committee:** CEO, CTO, CCO, IT Manager (quarterly meetings)
- **IT Audit:** External auditor (SGV or similar) - annual
- **DR Plan:** AWS multi-AZ deployment, daily backups, tested quarterly
- **Incident Reporting:** Automated alerts to BSP for payment failures >₱1M or >100 users affected

### 6.3 Data Privacy Act 2012

**Personal Data Collected by Gateways:**
- Card details (via PayMongo/PayMaya - not stored by us)
- E-wallet phone numbers (via GCash/PayMaya)
- Bank account numbers (via DragonPay)

**User Consent:**
- Explicit consent during payment method setup
- Privacy policy link displayed before payment
- Option to delete saved payment methods

**Data Retention:**
- Payment tokens: Retained until user deletes or account closes
- Transaction logs: 10 years (for AML/CFT compliance)
- Personal data: Deleted within 30 days of account closure (after retention period)

---

## 7. Webhook Management

### 7.1 Webhook Security

**Signature Verification:**
```javascript
// Example: PayMongo webhook verification
const crypto = require('crypto');

function verifyWebhook(payload, signature, secret) {
  const computedSignature = crypto
    .createHmac('sha256', secret)
    .update(JSON.stringify(payload))
    .digest('hex');
  
  return crypto.timingSafeEqual(
    Buffer.from(signature),
    Buffer.from(computedSignature)
  );
}

// Reject webhook if signature doesn't match
if (!verifyWebhook(req.body, req.headers['paymongo-signature'], WEBHOOK_SECRET)) {
  return res.status(401).send('Invalid signature');
}
```

### 7.2 Webhook Handling

**Event Types:**

| Gateway | Event Type | Action |
|---------|-----------|--------|
| PayMongo | `payment.paid` | Update payment status, release escrow if applicable |
| PayMongo | `payment.failed` | Notify user, suggest alternative method |
| PayMongo | `source.chargeable` | Charge the source, create payment |
| GCash | `PAYMENT_SUCCESS` | Update payment status, credit wallet |
| GCash | `PAYMENT_FAILED` | Notify user, log failure reason |
| PayMaya | `PAYMENT_SUCCESS` | Update payment status, release escrow |
| DragonPay | `S` (Success) | Mark payment as paid, credit wallet |
| DragonPay | `F` (Failed) | Mark payment as failed, notify user |

**Idempotency:**
- Webhooks may be sent multiple times (network issues)
- Use unique `idempotency_key` to prevent duplicate processing
- Check if transaction already processed before updating database

**Retry Mechanism:**
- If our server is down, gateways retry webhooks (typically 3-5 times)
- We maintain a webhook queue (Redis) to process missed events
- Manual reconciliation for events not received after 24 hours

---

## 8. Error Handling & Failover

### 8.1 Common Error Scenarios

**1. Insufficient Funds**
- **Detection:** Gateway returns error code (e.g., `card_declined`)
- **User Experience:** Clear error message, suggest wallet top-up or alternative method
- **Logging:** Record decline reason for analytics

**2. Gateway Timeout**
- **Detection:** No response from gateway within 30 seconds
- **Action:** Automatic retry (3 attempts with exponential backoff: 5s, 15s, 45s)
- **Failover:** If retries fail, switch to backup gateway (Xendit)
- **User Experience:** Loading indicator, "Processing payment..." message

**3. Webhook Not Received**
- **Detection:** Payment created but no webhook after 10 minutes
- **Action:** Poll gateway API for payment status (every 5 minutes, max 6 times)
- **Manual Reconciliation:** After 1 hour, flag for compliance team review

**4. Invalid API Credentials**
- **Detection:** 401 Unauthorized response from gateway
- **Action:** Alert engineering team immediately (PagerDuty)
- **Fallback:** Switch to backup gateway
- **Resolution:** Rotate API keys, update environment variables

**5. Rate Limiting**
- **Detection:** 429 Too Many Requests response
- **Action:** Implement exponential backoff, queue requests
- **Prevention:** Respect gateway rate limits (e.g., 100 req/min for PayMongo)

### 8.2 Circuit Breaker Pattern

**Implementation:**
```javascript
class CircuitBreaker {
  constructor(gateway, failureThreshold = 5, timeout = 60000) {
    this.gateway = gateway;
    this.failureCount = 0;
    this.failureThreshold = failureThreshold;
    this.timeout = timeout;
    this.state = 'CLOSED'; // CLOSED, OPEN, HALF_OPEN
  }

  async call(apiFunction) {
    if (this.state === 'OPEN') {
      if (Date.now() - this.openedAt > this.timeout) {
        this.state = 'HALF_OPEN';
      } else {
        throw new Error(`${this.gateway} circuit breaker is OPEN`);
      }
    }

    try {
      const result = await apiFunction();
      this.onSuccess();
      return result;
    } catch (error) {
      this.onFailure();
      throw error;
    }
  }

  onSuccess() {
    this.failureCount = 0;
    if (this.state === 'HALF_OPEN') {
      this.state = 'CLOSED';
    }
  }

  onFailure() {
    this.failureCount++;
    if (this.failureCount >= this.failureThreshold) {
      this.state = 'OPEN';
      this.openedAt = Date.now();
      // Alert engineering team
      alertTeam(`${this.gateway} circuit breaker opened`);
    }
  }
}

// Usage
const paymongoCircuit = new CircuitBreaker('PayMongo');
try {
  await paymongoCircuit.call(() => paymongo.createPayment(data));
} catch (error) {
  // Fallback to Xendit
  await xendit.createPayment(data);
}
```

---

## 9. API Integration Specifications

### 9.1 PayMongo API Reference

**Base URL:** `https://api.paymongo.com`

**Authentication:**
```bash
Authorization: Basic base64(secret_key:)
```

**Create Payment Intent:**
```javascript
POST /v1/payment_intents
{
  "data": {
    "attributes": {
      "amount": 150000,  // ₱1,500 in centavos
      "payment_method_allowed": ["card"],
      "payment_method_options": {
        "card": {
          "request_three_d_secure": "any"
        }
      },
      "currency": "PHP",
      "description": "Service Payment - Job #12345",
      "statement_descriptor": "AI TRUSTTRADE"
    }
  }
}
```

**Response:**
```javascript
{
  "data": {
    "id": "pi_abc123...",
    "type": "payment_intent",
    "attributes": {
      "amount": 150000,
      "currency": "PHP",
      "status": "awaiting_payment_method",
      "client_key": "pi_abc123..._client_...",
      // ...
    }
  }
}
```

**Attach Payment Method:**
```javascript
POST /v1/payment_intents/{id}/attach
{
  "data": {
    "attributes": {
      "payment_method": "pm_xyz789..."
    }
  }
}
```

**Webhook Payload (payment.paid):**
```javascript
{
  "data": {
    "id": "evt_abc123...",
    "type": "event",
    "attributes": {
      "type": "payment.paid",
      "data": {
        "id": "pay_abc123...",
        "type": "payment",
        "attributes": {
          "amount": 150000,
          "status": "paid",
          "paid_at": 1634567890,
          // ...
        }
      }
    }
  }
}
```

---

### 9.2 GCash for Business API Reference

**Base URL:** `https://api.gcash.com`

**Authentication (OAuth 2.0):**
```javascript
POST /oauth/v2/token
{
  "grant_type": "client_credentials",
  "client_id": "your_client_id",
  "client_secret": "your_client_secret"
}

// Response:
{
  "access_token": "eyJhbGci...",
  "token_type": "Bearer",
  "expires_in": 3600
}
```

**Create Payment:**
```javascript
POST /v2/payments
Authorization: Bearer {access_token}

{
  "merchant": {
    "merchantId": "M00000123456",
    "name": "AI TrustTrade"
  },
  "transaction": {
    "amount": 1500.00,
    "currency": "PHP",
    "merchantTransactionId": "TXN_12345_678",
    "description": "Service Payment"
  },
  "redirectUrl": "https://aitrustrade.ph/payment/callback"
}

// Response:
{
  "referenceNo": "GC2025112200001",
  "checkoutUrl": "https://pay.gcash.com/checkout/abc123...",
  "expiryTime": "2025-11-22T12:00:00Z"
}
```

**Check Payment Status:**
```javascript
GET /v2/payments/{referenceNo}
Authorization: Bearer {access_token}

// Response:
{
  "referenceNo": "GC2025112200001",
  "status": "SUCCESS",  // SUCCESS, PENDING, FAILED
  "amount": 1500.00,
  "paidAt": "2025-11-22T10:30:00Z"
}
```

---

### 9.3 PayMaya Checkout API Reference

**Base URL:** `https://pg.paymaya.com`

**Authentication:**
```bash
Authorization: Basic base64(public_key:)  # For creating checkouts
Authorization: Basic base64(secret_key:)  # For payments and refunds
```

**Create Checkout:**
```javascript
POST /checkout/v1/checkouts
Authorization: Basic {public_key}

{
  "totalAmount": {
    "value": 1500.00,
    "currency": "PHP"
  },
  "buyer": {
    "firstName": "Juan",
    "middleName": "D",
    "lastName": "Cruz",
    "contact": {
      "phone": "+639171234567",
      "email": "juan.cruz@example.com"
    }
  },
  "items": [{
    "name": "Aircon Cleaning Service",
    "quantity": 1,
    "amount": {
      "value": 1500.00
    },
    "totalAmount": {
      "value": 1500.00
    }
  }],
  "redirectUrl": {
    "success": "https://aitrustrade.ph/payment/success",
    "failure": "https://aitrustrade.ph/payment/failed",
    "cancel": "https://aitrustrade.ph/payment/cancelled"
  },
  "requestReferenceNumber": "TXN_12345_678"
}

// Response:
{
  "checkoutId": "cs_abc123...",
  "redirectUrl": "https://checkout.paymaya.com/checkout?id=cs_abc123..."
}
```

**Webhook Payload:**
```javascript
{
  "id": "evt_abc123...",
  "isPaid": true,
  "paymentStatus": "PAYMENT_SUCCESS",
  "totalAmount": {
    "amount": 1500.00,
    "currency": "PHP"
  },
  "receiptNumber": "000123456789",
  "transactionReferenceNumber": "TXN_12345_678"
}
```

---

## 10. Testing & Monitoring

### 10.1 Sandbox Testing

**PayMongo Sandbox:**
- **Test Cards:**
  - Success: `4343 4343 4343 4345` (Visa)
  - Decline: `4571 7360 0000 0183` (Insufficient Funds)
  - 3DS Required: `4120 0000 0000 0007`
- **Test Mode:** Use `sk_test_...` keys instead of `sk_live_...`

**GCash Sandbox:**
- **Test Account:** Provided by GCash upon merchant registration
- **Test MPIN:** `1234` or configured in sandbox

**PayMaya Sandbox:**
- **Test Cards:**
  - Success: `5123 4567 8901 2346` (Mastercard)
  - Decline: `5123 4567 8901 2353`

**DragonPay Sandbox:**
- **Test Mode:** Use `TEST` merchant ID
- **Test Banks:** Select "Test Bank" for InstaPay/PESONet

### 10.2 Automated Testing

**Integration Test Suite:**
```javascript
describe('Payment Gateway Integration', () => {
  it('should create PayMongo payment intent', async () => {
    const payment = await paymongo.createPaymentIntent({
      amount: 150000,
      currency: 'PHP'
    });
    expect(payment.status).toBe('awaiting_payment_method');
  });

  it('should handle PayMongo webhook', async () => {
    const webhook = {
      data: {
        attributes: {
          type: 'payment.paid',
          data: { id: 'pay_test123', amount: 150000 }
        }
      }
    };
    const result = await handlePaymongoWebhook(webhook);
    expect(result.paymentStatus).toBe('paid');
  });

  // More tests for each gateway...
});
```

**Load Testing:**
- Simulate 100 concurrent payment requests
- Measure response times (target: <3 seconds)
- Test gateway failover (intentionally fail primary, verify backup used)

### 10.3 Monitoring & Alerts

**Metrics Tracked:**
- **Gateway Uptime:** 99.9% target
- **Payment Success Rate:** >95% target
- **Average Processing Time:** <5 seconds target
- **Failed Payments:** Alert if >10% in 1 hour
- **Webhook Delivery:** Alert if >5% missed in 1 hour

**Monitoring Tools:**
- **Application Performance Monitoring:** New Relic or Datadog
- **Uptime Monitoring:** Pingdom or UptimeRobot (check gateway health endpoints)
- **Log Aggregation:** ELK Stack (Elasticsearch, Logstash, Kibana)
- **Alerting:** PagerDuty for critical issues

**Dashboard Metrics:**
```
┌─────────────────────────────────────────────────────┐
│        Payment Gateway Health Dashboard             │
├─────────────────────────────────────────────────────┤
│                                                     │
│  PayMongo:    ✓ Operational   (98.5% success)      │
│  GCash:       ✓ Operational   (97.2% success)      │
│  PayMaya:     ⚠ Degraded      (89.1% success)      │
│  DragonPay:   ✓ Operational   (96.8% success)      │
│                                                     │
│  Total Transactions Today: 1,247                    │
│  Total Value: ₱2,145,300                            │
│  Failed Payments: 31 (2.5%)                         │
│  Avg Processing Time: 3.2 seconds                   │
│                                                     │
└─────────────────────────────────────────────────────┘
```

---

## 11. Go-Live Checklist

### 11.1 Pre-Launch Requirements

**Gateway Account Setup:**
- ✅ PayMongo: Merchant account approved, live API keys issued
- ✅ GCash: Business account verified, API credentials received
- ✅ PayMaya: Merchant onboarding complete, live keys issued
- ✅ DragonPay: Merchant ID assigned, payment channels activated
- ✅ Xendit: Account created, backup integration tested

**Bank Account Setup:**
- ✅ Operating Account (BDO): Opened, linked to gateways
- ✅ Trust Account (BPI Trust): Opened, BSP compliance verified
- ✅ Settlement Account (UnionBank): Opened, payout integration tested

**Security & Compliance:**
- ✅ PCI-DSS SAQ (Self-Assessment Questionnaire) completed
- ✅ Penetration testing report clean
- ✅ Data Privacy Impact Assessment approved
- ✅ BSP Circular 808 compliance verified

**Technical Readiness:**
- ✅ All API integrations tested in sandbox
- ✅ Webhook endpoints verified and secured
- ✅ Payment routing logic tested (load testing passed)
- ✅ Error handling and failover mechanisms validated
- ✅ Monitoring and alerting systems operational

**Operational Readiness:**
- ✅ Customer support team trained on payment troubleshooting
- ✅ Compliance team trained on reconciliation procedures
- ✅ 24/7 on-call engineer assigned for payment issues
- ✅ Gateway support contacts saved (emergency hotlines)

### 11.2 Launch Day Protocol

**Hour 0: Soft Launch**
- Enable payments for internal beta users only (50 users)
- Monitor closely for any issues
- Verify webhooks are received and processed

**Hour 6: Gradual Rollout**
- Enable for 500 users (10% of pilot target)
- Monitor success rates, processing times
- Check reconciliation is working

**Hour 24: Full Rollout**
- Enable for all users
- Announce via email, push notifications
- Monitor transaction volume

**Week 1: Post-Launch Review**
- Daily reconciliation review
- Weekly meeting with gateway account managers
- Optimize routing based on actual data
- Address any user feedback

---

## 12. Financial Projections

### 12.1 Payment Gateway Costs (Year 1 Sandbox)

**Assumptions:**
- Total GMV: ₱600M (Year 1)
- Payment Method Mix:
  - E-Wallets (GCash + PayMaya): 60%
  - Cards (PayMongo): 30%
  - Bank Transfers (DragonPay): 10%

**Breakdown:**

| Gateway | Volume | Avg Fee | Total Cost |
|---------|--------|---------|------------|
| GCash | ₱360M | 2.5% | ₱9.0M |
| PayMaya | ₱180M | 2.9% | ₱5.2M |
| PayMongo | ₱180M | 2.9% + ₱15 | ₱5.5M |
| DragonPay | ₱60M | ₱10-15 avg | ₱0.9M |
| **Total** | **₱600M** | **~3.4% blended** | **₱20.6M** |

**Platform Revenue:**
- 7% transaction fee on ₱600M = ₱42M
- Less gateway costs: ₱42M - ₱20.6M = ₱21.4M
- **Net transaction revenue:** ₱21.4M (50.5% margin)

### 12.2 Cost Optimization Strategies

**Strategy 1: Volume Discounts**
- Negotiate with gateways when GMV exceeds ₱50M/month
- Target: Reduce fees by 10-20%

**Strategy 2: Wallet Promotion**
- Encourage users to pre-load wallets (reduce gateway transaction count)
- Incentive: ₱20 bonus for first wallet top-up

**Strategy 3: Smart Routing**
- Route large transactions through bank transfers (lower % fee)
- Route small transactions through e-wallets (lower fixed fee)

---

## 13. Risk Mitigation

### 13.1 Key Risks

| Risk | Impact | Probability | Mitigation |
|------|--------|-------------|------------|
| Gateway Downtime | High | Low | Multi-gateway redundancy, circuit breakers |
| Failed Webhook Delivery | Medium | Medium | Polling backup, reconciliation queue |
| Chargeback Fraud | High | Low | 3D Secure mandatory, evidence collection |
| Settlement Delays | Medium | Low | Monitor gateway SLAs, escalate to account manager |
| API Key Leak | Critical | Very Low | Rotate keys quarterly, environment variables only |
| Volume Spike (Gateway Limits) | Medium | Medium | Pre-negotiate higher limits, backup gateways |

### 13.2 Incident Response Plan

**Level 1: Minor (Single gateway degraded)**
- Detection: Monitoring alert (success rate <90%)
- Response: Route traffic to backup gateway
- Timeline: <5 minutes
- Notification: Engineering team (Slack)

**Level 2: Major (Primary gateway down)**
- Detection: Monitoring alert (gateway unreachable)
- Response: Failover to backup, notify users of potential delays
- Timeline: <15 minutes
- Notification: Engineering team + Customer Support (Slack, email)

**Level 3: Critical (All gateways down or systemic issue)**
- Detection: Multiple gateway failures or BSP system-wide issue
- Response: Activate disaster recovery, manual payment processing
- Timeline: <1 hour
- Notification: CEO, CTO, CCO, BSP (email, phone)

---

## 14. BSP Reporting Requirements

### 14.1 Regular Reports

**Monthly Report (Due 10th of following month):**
- Total transaction count and value (by gateway)
- Failed payment breakdown (by reason)
- Chargebacks and disputes
- Reconciliation summary (discrepancies and resolution)

**Quarterly Report (Due 15th after quarter end):**
- Gateway performance metrics (uptime, success rate)
- Cost analysis (fees paid to gateways)
- Volume trends and forecasts
- Integration updates or changes

**Annual Report (Due January 31st):**
- Full-year transaction summary
- Audit findings (external auditor)
- Compliance certifications (PCI-DSS, etc.)
- Recommendations for improvements

### 14.2 Incident Reporting

**Criteria for Immediate Reporting (<24 hours):**
- Payment system downtime >2 hours
- Data breach involving payment information
- Financial loss >₱500,000 due to fraud or error
- User impact >1,000 users

**Report Format:**
1. Incident description and timeline
2. Root cause analysis
3. Impact assessment (users, amount)
4. Remediation steps taken
5. Preventive measures implemented

---

## 15. Continuous Improvement Plan

### 15.1 Quarterly Reviews

**Q1 Review (Month 3):**
- Evaluate gateway performance (success rates, costs)
- User feedback on payment experience
- Identify pain points (e.g., slow settlement, high fees)
- Decide: Renew, renegotiate, or replace gateways

**Q2-Q4 Reviews:**
- Same as Q1, plus:
- Test new payment methods (e.g., buy-now-pay-later)
- Explore international gateways (for expats)
- Optimize routing algorithm based on 6-month data

### 15.2 Innovation Roadmap

**Year 1 (Sandbox):**
- Core integrations: PayMongo, GCash, PayMaya, DragonPay
- Smart routing: Cost-optimized
- Basic monitoring and alerting

**Year 2 (Post-Sandbox):**
- Add: Xendit (backup), BillEase (BNPL)
- Virtual cards: Generate temporary cards from wallet balance
- QR code payments: In-person service payments

**Year 3 (Scale):**
- Cryptocurrency: Coins.ph integration (USDT, Bitcoin)
- Cross-border: Expand to international freelancers
- Direct bank integrations: Reduce gateway dependency

---

## Appendices

### Appendix A: Gateway Comparison Matrix

| Feature | PayMongo | GCash | PayMaya | DragonPay | Xendit |
|---------|----------|-------|---------|-----------|--------|
| **Card Payments** | ✓ | ✗ | ✓ | ✗ | ✓ |
| **E-Wallet** | ✗ | ✓ | ✓ | ✗ | ✓ |
| **Bank Transfer** | ✗ | ✗ | ✗ | ✓ | ✓ |
| **OTC** | ✗ | ✗ | ✗ | ✓ | ✓ |
| **Transaction Fee** | 2.9%+₱15 | 2.5% | 2.9% | ₱10-25 | 2.9%+₱15 |
| **Settlement** | T+2 | T+1 | T+1 | T+0/T+1 | T+2 |
| **API Quality** | Excellent | Good | Good | Fair | Excellent |
| **Support** | 24/7 | Business hours | Business hours | Business hours | 24/7 |
| **Uptime SLA** | 99.9% | 99.5% | 99.5% | 99.0% | 99.9% |

### Appendix B: Contact Information

**PayMongo:**
- Account Manager: [Name], [email], [phone]
- Support: support@paymongo.com, +63 2 8123 4567
- Emergency Hotline: [24/7 number]

**GCash for Business:**
- Account Manager: [Name], [email], [phone]
- Support: gcashbiz@mynt.xyz, +63 2 7777 7777
- Emergency Hotline: [24/7 number]

**PayMaya:**
- Account Manager: [Name], [email], [phone]
- Support: merchants@paymaya.com, +63 2 8845 7788
- Emergency Hotline: [24/7 number]

**DragonPay:**
- Account Manager: [Name], [email], [phone]
- Support: support@dragonpay.ph, +63 2 8687 0199
- Emergency Hotline: [24/7 number]

**Xendit:**
- Account Manager: [Name], [email], [phone]
- Support: support@xendit.co, +63 2 8828 0909
- Emergency Hotline: [24/7 number]

### Appendix C: Glossary

- **3D Secure (3DS):** Additional authentication layer for card payments (OTP from bank)
- **Chargeback:** Customer disputes transaction and bank reverses payment
- **Circuit Breaker:** Design pattern that stops requests to failing service
- **Idempotency:** Handling duplicate requests without side effects
- **InstaPay:** Real-time bank transfer system (max ₱50K)
- **PCI-DSS:** Payment Card Industry Data Security Standard
- **PESONet:** Next-day batch bank transfer system (max ₱1M)
- **ROPPS:** Retail Operators of Payment Systems (BSP license)
- **Settlement:** Transfer of funds from gateway to merchant
- **Tokenization:** Replacing sensitive data with non-sensitive token
- **Webhook:** HTTP callback to notify platform of events

---

**Document Control**

**Version:** 1.0  
**Date:** November 2025  
**Status:** For BSP TRISD Review  
**Classification:** Confidential  
**Next Review:** Quarterly or upon gateway changes

**Approval:**
- ☐ Chief Technology Officer
- ☐ Chief Financial Officer
- ☐ Chief Compliance Officer
- ☐ Chief Executive Officer

---

**END OF DOCUMENT**

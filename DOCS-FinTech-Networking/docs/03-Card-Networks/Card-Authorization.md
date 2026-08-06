# Card Authorization

## Documentation Overview

Card authorization is the real-time decision process that determines whether a card transaction should be approved or declined. It occurs within milliseconds after a card is tapped, inserted, or swiped, involving multiple systems, cryptographic validations, risk assessments, and business rules. This document provides a comprehensive engineering examination of card authorization: the architecture, message flows, decision engines, risk assessment, authentication technologies, and performance engineering that enable instant approval or decline decisions.

## Documentation Objectives

```
DOCUMENTATION OBJECTIVES

    ┌─────────────────────────────────────────────────────────────────────────────┐
    │                                                                             │
    │   Understand the real-time card authorization process                       │
    │   Learn the architecture and components of authorization systems            │
    │   Study ISO 8583 messaging and field structures                             │
    │   Examine decision engines and risk assessment algorithms                   │
    │   Understand EMV, contactless, and online/offline authorization             │
    │   Learn stand-in processing and authorization holds                         │
    │   Study security mechanisms and performance engineering                     │
    │   Analyze real-world authorization scenarios                                │
    │                                                                             │
    └─────────────────────────────────────────────────────────────────────────────┘
```

## 1. Introduction to Card Authorization

**Card authorization** is the real-time decision process that determines whether a card transaction should be approved or declined. It occurs within milliseconds after a card is tapped, inserted, or swiped, involving multiple systems, cryptographic validations, risk assessments, and business rules.

**How it works:** When a card is presented for payment, an authorization request is generated. The request travels from the merchant terminal through the acquirer, the card network, and to the issuer. The issuer validates the card, checks the account, assesses risk, and applies business rules. A decision is made (approve or decline) and returned through the same path. The entire process typically takes 200-500 milliseconds.

```
CARD AUTHORIZATION DEFINITION

                         +---------------------------+
                         |  CARD AUTHORIZATION       |
                         |  Real-time approve/       |
                         |  decline decision         |
                         +-------------+-------------+
                                       |
          +----------------------------+----------------------------+
          │                            │                            │
          ▼                            ▼                            ▼
+---------------------------+  +---------------------------+  +---------------------------+
|  KEY CHARACTERISTICS      |  |  PRIMARY FUNCTIONS        |  |  WHAT IT IS NOT           |
|  - Real-time (< 1s)       |  |  - Validate card          |  |  - Clearing               |
|  - Always online (most)   |  |  - Check funds            |  |  - Settlement             |
|  - Authorizes funds       |  |  - Assess risk            |  |  - Capturing              |
|  - Places hold on funds   |  |  - Apply rules            |  |  - Accounting             |
|  - Returns code           |  |  - Return decision        |  |  - Reconciliation         |
|  - Security critical      |  |  - Reserve funds          |  |  - Payout                 |
+---------------------------+  +---------------------------+  +---------------------------+
```

### Why Is Authorization Required

**Authorization is required to verify that the card is valid, the account has sufficient funds, and the transaction is legitimate.** It prevents fraud by verifying card details and assessing risk. It protects merchants by ensuring payment. It protects cardholders by preventing unauthorized use.

### What Problems Does Authorization Solve

**Authorization solves several critical problems in card payments.** The validation problem ensures card and account validity. The funds problem verifies sufficient balance. The fraud problem detects suspicious activity. The authorization problem provides real-time approval or decline.

### What Happens If Authorization Is Skipped

**If authorization is skipped, merchants have no guarantee of payment.** The card could be invalid, stolen, or over the limit. The merchant would not know until settlement. This would create significant fraud and financial risk.

### Is Authorization the Same as Payment

**No. Authorization is a hold, not a payment.** It verifies funds and reserves them. It does not transfer money. Settlement is the actual transfer of funds.

## 2. Authorization Fundamentals

**What Information Is Checked During Authorization:** Several pieces of information are checked. Card validity verifies the card number, expiration date, and status. Account status checks if the account is active and in good standing. Available funds verify sufficient balance or credit. Risk indicators assess fraud probability. Business rules check limits and restrictions.

**What Determines Approval:** Approval is determined by a combination of factors. The card must be valid. The account must be in good standing. Sufficient funds or credit must be available. The risk score must be acceptable. All business rules must be satisfied.

**What Determines Decline:** A decline occurs when any check fails. The card may be expired, stolen, or invalid. The account may be frozen or over limit. The transaction may exceed velocity limits. The risk score may be too high.

## 3. Authorization Architecture

**Authorization Architecture** consists of multiple systems working together in real time.

The **Terminal/POS** captures the card data and generates the authorization request.

The **Acquirer** receives the request, formats it for the network, and routes it.

The **Card Network** routes the request to the issuer and returns the response.

The **Issuer** validates the card, checks the account, assesses risk, and makes the decision.

The **Authorization Service** within the issuer processes the request.

The **Risk Engine** assesses fraud probability in real time.

The **Database** stores account information, balances, and limits.

```
AUTHORIZATION SYSTEM ARCHITECTURE

    +-----------------------------------------------------------+
    │               AUTHORIZATION SYSTEM ARCHITECTURE           │
    +-----------------------------------------------------------+
    │                                                           │
    │   ┌───────────────────────────────────────────────────┐   │
    │   │              TERMINAL / POS                       │   │
    │   │  - Reads card (magstripe, chip, NFC)              │   │
    │   │  - Generates authorization request                │   │
    │   │  - Displays response to customer                  │   │
    │   └───────────────────────────────────────────────────┘   │
    │                           │                               │
    │                           │ ISO 8583                      │
    │                           ▼                               │
    │   ┌───────────────────────────────────────────────────┐   │
    │   │              ACQUIRER                             │   │
    │   │  - Receives request                               │   │
    │   │  - Formats for network                            │   │
    │   │  - Routes to network                              │   │
    │   │  - Returns response to merchant                   │   │
    │   └───────────────────────────────────────────────────┘   │
    │                           │                               │
    │                           │ Network Message               │
    │                           ▼                               │
    │   ┌───────────────────────────────────────────────────┐   │
    │   │              CARD NETWORK                         │   │
    │   │  - Routes to issuer                               │   │
    │   │  - Applies network rules                          │   │
    │   │  - Stand-in processing (STIP)                     │   │
    │   │  - Returns response to acquirer                   │   │
    │   └───────────────────────────────────────────────────┘   │
    │                           │                               │
    │                           │ Authorization Request         │
    │                           ▼                               │
    │   ┌───────────────────────────────────────────────────┐   │
    │   │              ISSUER                               │   │
    │   │                                                   │   │
    │   │  ┌───────────────────────────────────────────┐    │   │
    │   │  │  Authorization Service                    │    │   │
    │   │  │  - Validate card                          │    │   │
    │   │  │  - Authenticate                           │    │   │
    │   │  │  - Check account                          │    │   │
    │   │  └───────────────────────────────────────────┘    │   │
    │   │  ┌───────────────────────────────────────────┐    │   │
    │   │  │  Risk Engine                              │    │   │
    │   │  │  - Real-time scoring                      │    │   │
    │   │  │  - Rule evaluation                        │    │   │
    │   │  └───────────────────────────────────────────┘    │   │
    │   │  ┌───────────────────────────────────────────┐    │   │
    │   │  │  Decision Engine                          │    │   │
    │   │  │  - Apply business rules                   │    │   │
    │   │  │  - Check limits                           │    │   │
    │   │  │  - Make approve/decline decision          │    │   │
    │   │  └───────────────────────────────────────────┘    │   │
    │   │  ┌───────────────────────────────────────────┐    │   │
    │   │  │  Database                                 │    │   │
    │   │  │  - Account balances                       │    │   │
    │   │  │  - Spending limits                        │    │   │
    │   │  │  - Card status                            │    │   │
    │   │  └───────────────────────────────────────────┘    │   │
    │   └───────────────────────────────────────────────────┘   │
    │                                                           │
    └-----------------------------------------------------------+
```

## 4. Authorization Participants

Several participants are involved in the authorization process.

**Cardholder** is the customer presenting the card. They initiate the transaction.

**Merchant** accepts the card and initiates the authorization request.

**Merchant Terminal** reads the card and generates the request.

**Acquirer** is the merchant's bank. It routes the request to the network.

**Card Network** routes the request to the issuer. It may perform stand-in processing.

**Issuer** is the cardholder's bank. It makes the authorization decision.

```
AUTHORIZATION PARTICIPANTS

              +-------------------------------------------------+
              |          AUTHORIZATION PARTICIPANTS             |
              +-------------------------------------------------+
                                       │
          +----------------------------+----------------------------+
          │                            │                            │
          ▼                            ▼                            ▼
+---------------------------+  +---------------------------+  +---------------------------+
|  CARDHOLDER               |  |  MERCHANT                 |  |  MERCHANT TERMINAL        |
|  - Initiates payment      |  |  - Accepts card           |  |  - Reads card data        |
|  - Presents card          |  |  - Submits authorization  |  |  - Generates request      |
|  - Receives approval      |  |  - Provides goods         |  |  - Displays response      |
+---------------------------+  +---------------------------+  +---------------------------+
          │                            │                            │
          +----------------------------+----------------------------+
                                       │
          +----------------------------+----------------------------+
          │                            │                            │
          ▼                            ▼                            ▼
+---------------------------+  +---------------------------+  +---------------------------+
|  ACQUIRER                 |  |  CARD NETWORK             |  |  ISSUER                   |
|  - Merchant's bank        |  |  - Routes requests        |  |  - Cardholder's bank      |
|  - Routes to network      |  |  - Stand-in processing    |  |  - Validates card         |
|  - Receives response      |  |  - Returns response       |  |  - Checks account         |
|  - Settles funds          |  |  - Applies rules          |  |  - Makes decision         |
+---------------------------+  +---------------------------+  +---------------------------+
```

## 5. Authorization Request Lifecycle

The authorization request lifecycle consists of several phases from card read to response.

**Card Read** is the first phase where the terminal reads the card data (magstripe, chip, or NFC).

**Request Generation** is the second phase where the terminal creates the authorization message.

**Request Transmission** is the third phase where the request is sent to the acquirer.

**Network Routing** is the fourth phase where the network routes to the issuer.

**Issuer Processing** is the fifth phase where the issuer validates and decides.

**Response Routing** is the sixth phase where the response returns through the network.

**Response Display** is the final phase where the terminal displays the result.

```
AUTHORIZATION LIFECYCLE

    +-----------------------------------------------------------+
    │               AUTHORIZATION LIFECYCLE                     │
    +-----------------------------------------------------------+
    │                                                           │
    │   PHASE 1: CARD READ                                      │
    │   ├── Card tapped/inserted/swiped                         │
    │   ├── Data read from chip/magstripe                       │
    │   └── NFC (contactless) data captured                     │
    │                                                           │
    │   PHASE 2: REQUEST GENERATION                             │
    │   ├── Authorization request created                       │
    │   ├── ISO 8583 message constructed                        │
    │   ├── Fields populated                                    │
    │   └── Cryptogram generated (EMV)                          │
    │                                                           │
    │   PHASE 3: REQUEST TRANSMISSION                           │
    │   ├── Sent to acquirer                                    │
    │   ├── Format validated                                    │
    │   └── Forwarded to network                                │
    │                                                           │
    │   PHASE 4: NETWORK ROUTING                                │
    │   ├── Request routed to issuer                            │
    │   ├── Network rules applied                               │
    │   └── Stand-in processing (if issuer unavailable)         │
    │                                                           │
    │   PHASE 5: ISSUER PROCESSING                              │
    │   ├── Card validated                                      │
    │   ├── Account checked                                     │
    │   ├── Risk assessed                                       │
    │   ├── Rules applied                                       │
    │   └── Decision made                                       │
    │                                                           │
    │   PHASE 6: RESPONSE ROUTING                               │
    │   ├── Response sent to network                            │
    │   ├── Network routes to acquirer                          │
    │   └── Acquirer sends to terminal                          │
    │                                                           │
    │   PHASE 7: RESPONSE DISPLAY                               │
    │   ├── Approved/Declined displayed                         │
    │   ├── Receipt printed (optional)                          │
    │   └── Transaction complete (authorization phase)          │
    │                                                           │
    └-----------------------------------------------------------+
```

### How Is the Request Generated

**The request is generated by the merchant terminal.** It reads the card data, populates ISO 8583 fields, and creates the authorization message. It includes card number, amount, merchant ID, and terminal ID.

### How Is the Response Returned

**The response returns through the same path.** The issuer returns a response code and authorization code (if approved). The network routes it back. The terminal displays the result.

## 6. Authorization Messages

### What Information Is Contained in an Authorization Request

**An authorization request contains several fields.** Card number, expiration date, and CVV. Transaction amount and currency. Merchant ID and category code. Terminal ID and location. Transaction date and time. Additional data for EMV and contactless.

### What Fields Are Mandatory

**Mandatory fields include card number, expiration date, amount, merchant ID, and transaction type.** Optional fields include CVV, AVS data, and EMV cryptogram data.

### How Are Authorization Messages Structured

**Authorization messages use ISO 8583 format.** They have a message type indicator (MTI), bitmaps indicating present fields, and data elements. The MTI identifies the message type (e.g., 0100 for authorization request).

```
ISO 8583 AUTHORIZATION MESSAGE

    +-----------------------------------------------------------+
    │               ISO 8583 AUTHORIZATION MESSAGE              │
    +-----------------------------------------------------------+
    │                                                           │
    │   ┌───────────────────────────────────────────────────┐   │
    │   │  MESSAGE TYPE INDICATOR (MTI)                     │   │
    │   │  0100 = Authorization Request                     │   │
    │   │  0110 = Authorization Response                    │   │
    │   └───────────────────────────────────────────────────┘   │
    │                           │                               │
    │                           ▼                               │
    │   ┌───────────────────────────────────────────────────┐   │
    │   │  BITMAP (Primary + Secondary)                     │   │
    │   │  Indicates which data elements are present        │   │
    │   └───────────────────────────────────────────────────┘   │
    │                           │                               │
    │                           ▼                               │
    │   ┌───────────────────────────────────────────────────┐   │
    │   │  DATA ELEMENTS                                    │   │
    │   │                                                   │   │
    │   │  Field 2: Primary Account Number (PAN)            │   │
    │   │  Field 3: Processing Code (Transaction Type)      │   │
    │   │  Field 4: Transaction Amount                      │   │
    │   │  Field 11: System Trace Audit Number (STAN)       │   │
    │   │  Field 12: Local Transaction Time                 │   │
    │   │  Field 13: Local Transaction Date                 │   │
    │   │  Field 14: Expiration Date                        │   │
    │   │  Field 22: POS Entry Mode                         │   │
    │   │  Field 25: POS Condition Code                     │   │
    │   │  Field 32: Acquiring Institution ID               │   │
    │   │  Field 33: Forwarding Institution ID              │   │
    │   │  Field 42: Card Acceptor ID (Merchant ID)         │   │
    │   │  Field 43: Card Acceptor Name/Location            │   │
    │   │  Field 49: Currency Code                          │   │
    │   │  Field 52: PIN Data (encrypted)                   │   │
    │   │  Field 55: EMV Data (chip information)            │   │
    │   │  Field 61: CVV/CVV2 Data                          │   │
    │   └───────────────────────────────────────────────────┘   │
    │                                                           │
    └-----------------------------------------------------------+
```

## 7. Authorization Decision Engine

**Authorization Decision Engine** evaluates multiple factors to make a real-time approve/decline decision.

The engine validates the card and account. It checks available funds or credit. It applies risk scores. It evaluates business rules. It checks velocity and limits. It considers merchant risk. It produces a final decision.

```
DECISION PIPELINE

    +-----------------------------------------------------------+
    │               DECISION PIPELINE                           │
    +-----------------------------------------------------------+
    │                                                           │
    │   AUTHORIZATION REQUEST                                   │
    │        │                                                  │
    │        ▼                                                  │
    │   ┌───────────────────────────────────────────────────┐   │
    │   │    VALIDATION                                     │   │
    │   │  - Card number valid                              │   │
    │   │  - Expiration date valid                          │   │
    │   │  - CVV valid                                      │   │
    │   │  - Card not blocked                               │   │
    │   └───────────────────────────────────────────────────┘   │
    │        │                                                  │
    │        ▼                                                  │
    │   ┌───────────────────────────────────────────────────┐   │
    │   │    AUTHENTICATION                                 │   │
    │   │  - PIN verified (if entered)                      │   │
    │   │  - EMV cryptogram validated                       │   │
    │   │  - 3-D Secure (if applicable)                     │   │
    │   └───────────────────────────────────────────────────┘   │
    │        │                                                  │
    │        ▼                                                  │
    │   ┌───────────────────────────────────────────────────┐   │
    │   │    RISK ASSESSMENT                                │   │
    │   │  - Fraud score                                    │   │
    │   │  - Velocity checks                                │   │
    │   │  - Geographic analysis                            │   │
    │   │  - Merchant risk assessment                       │   │
    │   └───────────────────────────────────────────────────┘   │
    │        │                                                  │
    │        ▼                                                  │
    │   ┌───────────────────────────────────────────────────┐   │
    │   │    RULES ENGINE                                   │   │
    │   │  - Transaction limits                             │   │
    │   │  - Spending limits                                │   │
    │   │  - Merchant category restrictions                 │   │
    │   │  - Velocity limits                                │   │
    │   └───────────────────────────────────────────────────┘   │
    │        │                                                  │
    │        ▼                                                  │
    │   ┌───────────────────────────────────────────────────┐   │
    │   │    BALANCE CHECK                                  │   │
    │   │  - Available credit/balance                       │   │
    │   │  - Credit line                                    │   │
    │   │  - Pending holds                                  │   │
    │   └───────────────────────────────────────────────────┘   │
    │        │                                                  │
    │        ▼                                                  │
    │   ┌───────────────────────────────────────────────────┐   │
    │   │    DECISION                                       │   │
    │   │  - Approve                                        │   │
    │   │  - Decline                                        │   │
    │   │  - Refer (manual review)                          │   │
    │   └───────────────────────────────────────────────────┘   │
    │                                                           │
    └-----------------------------------------------------------+
```

### How Does an Issuer Decide to Approve or Decline

**The issuer applies a decision tree.** It checks card validity and account status. It verifies funds or credit. It assesses risk. It applies rules. It returns the decision.

### What Decision Engines Are Used

**Decision engines include rules engines, machine learning models, and expert systems.** Rules engines apply business logic. Machine learning models predict risk. Expert systems emulate human decision-making.

### What Business Rules Are Evaluated

**Business rules include transaction limits, merchant restrictions, and velocity checks.** Daily limits restrict spending. Merchant category restrictions block prohibited merchants. Velocity checks detect unusual activity.

### How Are Spending Limits Checked

**Spending limits are checked against available credit or balance.** The system subtracts pending holds. It verifies the transaction fits within remaining credit.

## 8. Risk Assessment

**Risk Assessment** occurs in real time during authorization.

### What Risk Checks Occur Before Approval

**Multiple risk checks occur before approval.** Velocity checks detect rapid transactions. Geographic anomalies flag unusual locations. Merchant risk evaluates the merchant. Device fingerprinting identifies suspicious devices.

### How Are Velocity Checks Performed

**Velocity checks count transactions in a time window.** The system tracks transactions per minute, hour, or day. High velocity triggers risk alerts.

### How Are Geographic Anomalies Detected

**Geographic anomalies are detected by comparing transaction location with cardholder patterns.** Anomalies trigger risk assessments. 3-D Secure may be required.

### How Are Merchant Risks Evaluated

**Merchant risks are evaluated based on category, location, and history.** High-risk categories (gaming, adult entertainment) trigger more scrutiny. New merchants may have higher risk.

```
RISK ASSESSMENT SCORING

    +-----------------------------------------------------------+
    │               RISK ASSESSMENT SCORING                     │
    +-----------------------------------------------------------+
    │                                                           │
    │   ┌───────────────────────────────────────────────────┐   │
    │   │  Factor                       │ Weight            │   │
    │   │  ─────────────────────────────│────────────────── │   │
    │   │  Transaction Amount           │ 0.25              │   │
    │   │  Merchant Category            │ 0.20              │   │
    │   │  Transaction Velocity         │ 0.15              │   │
    │   │  Geographic Distance          │ 0.15              │   │
    │   │  Device Fingerprint           │ 0.10              │   │
    │   │  Cardholder History           │ 0.10              │   │
    │   │  Time of Day                  │ 0.05              │   │
    │   │  ─────────────────────────────│────────────────── │   │
    │   │  TOTAL RISK SCORE             │ 1.00              │   │
    │   └───────────────────────────────────────────────────┘   │
    │                                                           │
    │   RISK SCORE INTERPRETATION:                              │
    │   - 0.00 - 0.30: Low Risk → Approve                       │
    │   - 0.31 - 0.60: Medium Risk → Review / 3-D Secure        │
    │   - 0.61 - 1.00: High Risk → Decline                      │
    │                                                           │
    └-----------------------------------------------------------+
```

## 9. Authentication Technologies

### How Is the Cardholder Authenticated

**Cardholder authentication uses multiple methods.** PIN verification confirms the cardholder knows the PIN. CVV verification confirms the card is present. EMV cryptograms verify the chip is genuine. Biometrics (fingerprint, face) authenticate via mobile wallets.

### How Does PIN Verification Work

**PIN verification uses a PIN block.** The terminal encrypts the PIN. The issuer verifies the encrypted PIN. It compares with the stored PIN. It returns success or failure.

### How Does CVV Verification Work

**CVV verification checks the card verification value.** The terminal sends the CVV. The issuer validates it against the stored value. It returns match or mismatch.

### How Does 3-D Secure Affect Authorization

**3-D Secure (3DS) authenticates the cardholder.** It redirects to the issuer. The issuer verifies the cardholder. It returns authentication status. The authorization may be affected.

## 10. Authorization Rules

**Authorization Rules** enforce limits and restrictions.

### What Authorization Rules Exist

**Multiple authorization rules exist.** Transaction limits restrict per-transaction amounts. Daily limits restrict total spending. Merchant category rules restrict specific categories. Velocity rules restrict transaction frequency.

### How Are Transaction Limits Enforced

**Transaction limits are enforced by the issuer.** The system checks the requested amount against the limit. If the amount exceeds the limit, the transaction is declined.

### How Are Blocked Merchants Handled

**Blocked merchants are identified by merchant ID or category.** The issuer maintains a block list. Transactions to blocked merchants are declined.

### How Are Expired Cards Detected

**Expired cards are detected by checking the expiration date.** The issuer compares the date with the current date. If expired, the transaction is declined.

## 11. Card Verification Methods

### What Is CVV

**CVV (Card Verification Value) is a 3- or 4-digit code on the card.** It verifies the card is physically present. It is not stored in the magstripe or chip. It prevents card-not-present fraud.

### What Is AVS

**AVS (Address Verification Service) checks the billing address.** It compares parts of the address with the issuer's records. It returns match codes. It is used for card-not-present transactions.

### How Are Verification Results Used

**Verification results are used in the decision.** CVV mismatch may flag risk. AVS mismatch may trigger additional verification. Results are combined with other risk factors.

### What EMV Cryptograms Are Verified

**EMV cryptograms (ARQC) are verified during authorization.** The issuer validates the cryptogram. It confirms the chip is genuine. It verifies transaction data integrity.

## 12. EMV Authorization

### How Does EMV Authorization Differ

**EMV authorization includes cryptographic validation.** The chip generates an ARQC (Authorization Request Cryptogram). The issuer validates the ARQC. The issuer returns an ARPC (Authorization Response Cryptogram).

### What Is an ARQC

**ARQC (Authorization Request Cryptogram) is generated by the chip.** It includes transaction data. It is encrypted with a secret key. It is sent to the issuer for validation.

### What Is an ARPC

**ARPC (Authorization Response Cryptogram) is returned by the issuer.** It confirms the validation. It may include additional instructions. The terminal validates the ARPC.

### How Are EMV Cryptograms Validated

**EMV cryptograms are validated using cryptographic algorithms.** The issuer decrypts the cryptogram. It verifies the transaction data. It confirms the card is genuine.

```
EMV AUTHORIZATION FLOW

    +-----------------------------------------------------------+
    │               EMV AUTHORIZATION FLOW                      │
    +-----------------------------------------------------------+
    │                                                           │
    │   CARD INSERTED INTO TERMINAL                             │
    │        │                                                  │
    │        ▼                                                  │
    │   TERMINAL READS CHIP DATA                                │
    │        │                                                  │
    │        ▼                                                  │
    │   TERMINAL GENERATES AUTHORIZATION REQUEST                │
    │        │                                                  │
    │        ▼                                                  │
    │   CHIP GENERATES ARQC (Authorization Request              │
    │   Cryptogram)                                             │
    │        │                                                  │
    │        ▼                                                  │
    │   REQUEST SENT TO ISSUER                                  │
    │        │                                                  │
    │        ▼                                                  │
    │   ISSUER VALIDATES ARQC                                   │
    │        │                                                  │
    │        ▼                                                  │
    │   ISSUER GENERATES ARPC (Authorization Response           │
    │   Cryptogram)                                             │
    │        │                                                  │
    │        ▼                                                  │
    │   RESPONSE SENT TO TERMINAL                               │
    │        │                                                  │
    │        ▼                                                  │
    │   TERMINAL VALIDATES ARPC                                 │
    │        │                                                  │
    │        ▼                                                  │
    │   TRANSACTION APPROVED                                    │
    │                                                           │
    └-----------------------------------------------------------+
```

## 13. Contactless Authorization

### How Are NFC Transactions Authorized

**NFC transactions are authorized like contact transactions.** The terminal reads the NFC data. It generates an authorization request. The issuer processes it.

### When Is PIN Bypass Allowed

**PIN bypass is allowed for low-value transactions.** Thresholds vary by country and card type. Contactless limits are typically $25-$100. High-value transactions require PIN or signature.

### What Are Contactless Transaction Limits

**Contactless transaction limits restrict tap-to-pay amounts.** The limit is set by the issuer. It may vary by merchant category. Above the limit, PIN is required.

## 14. Online vs Offline Authorization

### What Is Online Authorization

**Online authorization sends the request to the issuer.** It validates in real time. It provides immediate approval or decline. It is used for most transactions.

### What Is Offline Authorization

**Offline authorization is performed by the terminal without contacting the issuer.** It uses floor limits. It relies on risk assessment. It is used when online is unavailable.

### When Is Offline Approval Used

**Offline approval is used when the issuer is unavailable.** The terminal may approve small amounts. It may approve based on previous authorization. It carries higher risk.

### What Are Floor Limits

**Floor limits are the maximum amount for offline authorization.** Below the limit, the terminal may approve offline. Above the limit, online authorization is required.

## 15. Stand-In Processing (STIP)

**Stand-In Processing (STIP)** occurs when the issuer is unavailable. The card network authorizes on behalf of the issuer.

### What Is Stand-In Processing (STIP)

**STIP is processing performed by the network when the issuer is unavailable.** The network uses configured rules. It may approve or decline. It limits risk exposure.

### When Does the Network Authorize Instead of the Issuer

**The network authorizes when the issuer is offline or unresponsive.** The network applies STIP rules. It uses pre-configured parameters.

### How Are STIP Rules Configured

**STIP rules are configured by the issuer.** Rules include approval amounts, merchant categories, and risk thresholds. The issuer updates rules regularly.

```
STAND-IN PROCESSING (STIP)

    +-----------------------------------------------------------+
    │               STAND-IN PROCESSING (STIP)                  │
    +-----------------------------------------------------------+
    │                                                           │
    │   AUTHORIZATION REQUEST                                   │
    │        │                                                  │
    │        ▼                                                  │
    │   CARD NETWORK                                            │
    │        │                                                  │
    │        │ Attempt issuer connection                        │
    │        ▼                                                  │
    │   ISSUER AVAILABLE?                                       │
    │        │                                                  │
    │   ┌────┴────┐                                             │
    │   │         │                                             │
    │   ▼         ▼                                             │
    │  YES       NO                                             │
    │   │         │                                             │
    │   │         ▼                                             │
    │   │    STIP ACTIVATED                                     │
    │   │         │                                             │
    │   │         ▼                                             │
    │   │    APPLY STIP RULES                                   │
    │   │         │                                             │
    │   │         ▼                                             │
    │   │    AUTHORIZE/DECLINE                                  │
    │   │         │                                             │
    │   │         │                                             │
    │   └────┬────┘                                             │
    │        │                                                  │
    │        ▼                                                  │
    │   RESPONSE                                                │
    │                                                           │
    └-----------------------------------------------------------+
```

## 16. Authorization Holds

### What Is an Authorization Hold

**An authorization hold reserves funds for a transaction.** The issuer reduces available credit/balance. The hold is temporary. It is released on settlement or reversal.

### Why Do Hotels Use Authorization Holds

**Hotels use authorization holds to cover potential charges.** The hold covers room charges, minibar, and damages. It may be for the full stay amount plus incidentals.

### Why Do Fuel Stations Pre-Authorize

**Fuel stations pre-authorize a fixed amount.** The amount is typically $1 or $100. It verifies the card is valid. The final amount is charged at settlement.

### How Long Can Holds Remain

**Holds remain until settlement or reversal.** They typically expire after 7-10 days. The issuer sets the expiration time.

## 17. Partial Authorization

### What Is Partial Authorization

**Partial authorization approves less than the requested amount.** It is used when funds are insufficient. The merchant may accept the partial amount.

### When Is It Used

**Partial authorization is used for fuel stations and restaurants.** It is used for split payments. It may be offered when the full amount is unavailable.

### How Are Remaining Balances Handled

**The remaining balance may be settled by the merchant.** The customer may pay the remainder with another method. The merchant may adjust the total.

## 18. Incremental Authorization

### What Is Incremental Authorization

**Incremental authorization increases the authorized amount.** It is used for hotels and rentals. It adds charges to the hold.

### Why Do Hotels and Rentals Use It

**Hotels and rentals use incremental authorization for additional charges.** Extra nights and services are authorized. The hold is increased.

### How Is the Authorization Amount Increased

**The authorization amount is increased by sending a new request.** The additional amount is authorized. The total hold increases.

## 19. Reversals & Voids

### What Is an Authorization Reversal

**An authorization reversal cancels the authorization.** It releases the hold on funds. It is sent when the transaction is canceled.

### What Is a Void

**A void is a cancellation before settlement.** It is similar to a reversal. It cancels the transaction entirely.

### When Should Reversals Be Sent

**Reversals should be sent when a transaction is canceled.** If a customer cancels an order, a reversal is sent. If a technical error occurs, a reversal is sent.

### How Do Reversals Release Held Funds

**Reversals release funds immediately.** The hold is removed from the available balance. The funds become available again.

## 20. Authorization Response Codes

**Authorization Response Codes** indicate the result of the authorization.

```
RESPONSE CODES

    +-----------------------------------------------------------+
    │               RESPONSE CODES                              │
    +-----------------------------------------------------------+
    │                                                           │
    │   Code  │ Description                                     │
    │   ──────│─────────────────────────────────────────────────│
    │   00    │ Approved (Transaction approved)                 │
    │   01    │ Refer to Issuer (call card center)              │
    │   02    │ Refer to Issuer (special condition)             │
    │   03    │ Invalid Merchant (merchant ID invalid)          │
    │   04    │ Pick Up Card (capture card)                     │
    │   05    │ Do Not Honor (decline)                          │
    │   06    │ Error                                           │
    │   07    │ Pick Up Card (special condition)                │
    │   10    │ Partial Approval (approved partial amount)      │
    │   11    │ VIP Approval (approved)                         │
    │   12    │ Invalid Transaction (invalid data)              │
    │   13    │ Invalid Amount (amount limit exceeded)          │
    │   14    │ Invalid Card Number (card number invalid)       │
    │   15    │ No Such Issuer (issuer not found)               │
    │   19    │ Re-enter Transaction                            │
    │   22    │ Suspected Malfunction                           │
    │   28    │ File Not Found (account not found)              │
    │   30    │ Format Error (invalid message format)           │
    │   31    │ Bank Not Supported                              │
    │   33    │ Expired Card (card expired)                     │
    │   34    │ Suspected Fraud (fraud suspected)               │
    │   40    │ Restricted Card (card restricted)               │
    │   41    │ Lost Card (card reported lost)                  │
    │   43    │ Stolen Card (card reported stolen)              │
    │   51    │ Insufficient Funds (over limit)                 │
    │   54    │ Expired Card (card expired)                     │
    │   55    │ Incorrect PIN (PIN invalid)                     │
    │   57    │ Transaction Not Permitted                       │
    │   58    │ Transaction Not Permitted (terminal)            │
    │   61    │ Exceeds Withdrawal Limit                        │
    │   62    │ Restricted Card                                 │
    │   63    │ Security Violation                              │
    │   65    │ Exceeds Withdrawal Frequency                    │
    │   66    │ Exceeds Withdrawal Frequency                    │
    │   75    │ PIN Tries Exceeded                              │
    │   76    │ Invalid Institution ID                          │
    │   78    │ Invalid Account                                 │
    │   81    │ PIN Blocked                                     │
    │   82    │ Invalid CVV                                     │
    │   91    │ Issuer Unavailable (STIP processing)            │
    │   92    │ Destination Not Found                           │
    │   94    │ Duplicate Transaction                           │
    │   96    │ System Error                                    │
    │                                                           │
    └-----------------------------------------------------------+
```

## 21. Authorization Security

### How Are Authorization Requests Encrypted

**Authorization requests use TLS/SSL for network encryption.** PIN blocks are encrypted with AES. EMV data is encrypted. The entire message is protected.

### How Are Message Authentication Codes Used

**MACs verify message integrity.** The sender generates a MAC. The receiver validates the MAC. It ensures the message is unaltered.

### How Are HSMs Involved

**HSMs generate and store cryptographic keys.** They perform encryption and decryption. They sign and verify messages.

### How Is Replay Prevented

**Replay is prevented by sequence numbers and timestamps.** Each request has a unique STAN. Timestamps are validated. Duplicates are rejected.

```
AUTHORIZATION SECURITY LAYERS

    +-----------------------------------------------------------+
    │               AUTHORIZATION SECURITY LAYERS               │
    +-----------------------------------------------------------+
    │                                                           │
    │   ┌───────────────────────────────────────────────────┐   │
    │   │  NETWORK SECURITY                                 │   │
    │   │  - TLS 1.3 encryption                             │   │
    │   │  - Secure network connections                     │   │
    │   │  - VPN (private networks)                         │   │
    │   └───────────────────────────────────────────────────┘   │
    │                           │                               │
    │   ┌───────────────────────────────────────────────────┐   │
    │   │  MESSAGE SECURITY                                 │   │
    │   │  - MAC (Message Authentication Code)              │   │
    │   │  - Digital signatures                             │   │
    │   │  - Sequence numbers                               │   │
    │   └───────────────────────────────────────────────────┘   │
    │                           │                               │
    │   ┌───────────────────────────────────────────────────┐   │
    │   │  DATA SECURITY                                    │   │
    │   │  - AES-256 encryption (PIN blocks)                │   │
    │   │  - EMV cryptograms                                │   │
    │   │  - HSM key storage                                │   │
    │   └───────────────────────────────────────────────────┘   │
    │                           │                               │
    │   ┌───────────────────────────────────────────────────┐   │
    │   │  APPLICATION SECURITY                             │   │
    │   │  - Authentication                                 │   │
    │   │  - Authorization                                  │   │
    │   │  - Audit logging                                  │   │
    │   └───────────────────────────────────────────────────┘   │
    │                                                           │
    └-----------------------------------------------------------+
```

## 22. Authorization Performance

### What Latency Targets Exist

**Latency targets are typically under 500ms.** Most transactions complete in 200-300ms. The issuer must respond within the network timeout (typically 5-10 seconds).

### How Many Authorization Requests per Second Can Systems Handle

**Systems handle thousands of requests per second.** Peak volumes occur during holidays. Systems scale horizontally.

### How Is High Availability Achieved

**High availability is achieved through redundancy.** Multiple data centers, servers, and network paths. Failover is automatic.

```
AUTHORIZATION TIMELINE

    +-----------------------------------------------------------+
    │               AUTHORIZATION TIMELINE                      │
    +-----------------------------------------------------------+
    │                                                           │
    │   0 ms   Card tapped/inserted                             │
    │   5 ms   Terminal reads card data                         │
    │   20 ms  Terminal generates authorization request         │
    │   30 ms  Request sent to acquirer                         │
    │   50 ms  Acquirer processes request                       │
    │   70 ms  Network routes request                           │
    │   90 ms  Issuer receives request                          │
    │   100 ms Issuer validates card                            │
    │   120 ms Issuer checks account                            │
    │   140 ms Risk engine runs                                 │
    │   160 ms Rules engine evaluates                           │
    │   180 ms Decision made                                    │
    │   200 ms Response sent to network                         │
    │   220 ms Network routes response                          │
    │   240 ms Acquirer receives response                       │
    │   260 ms Terminal displays result                         │
    │   280 ms Transaction complete                             │
    │                                                           │
    │   TOTAL: 280 ms (well under 500 ms target)                │
    │                                                           │
    └-----------------------------------------------------------+
```

## 23. Authorization Databases

### What Databases Support Authorization

**Authorization databases are high-performance relational databases.** They store account balances, limits, and card status. They are replicated for availability.

### How Are Account Balances Retrieved

**Account balances are retrieved in real time.** The database is queried for the account. The balance is checked against the transaction amount.

### How Are Spending Limits Stored

**Spending limits are stored in the database.** They are per card, per account, or per merchant category. They are updated dynamically.

### How Is Authorization History Maintained

**Authorization history is maintained for fraud detection.** It is used for velocity checks. It is used for dispute resolution.

## 24. Authorization Engineering

### How Are Authorization Systems Horizontally Scaled

**Authorization systems are horizontally scaled by adding servers.** Load balancers distribute requests. State is shared across servers.

### How Is Caching Used

**Caching is used for high-performance reads.** Account balances may be cached. Card status is cached. Limits are cached.

### How Are Duplicate Requests Prevented

**Duplicate requests are prevented by STAN (System Trace Audit Number).** Each request has a unique STAN. Duplicates are rejected.

### How Are Retries Handled Safely

**Retries are handled with idempotency.** Each request has a unique ID. If the request is retried, it returns the same result. Duplicate processing is prevented.

```
DUPLICATE PREVENTION

    +-----------------------------------------------------------+
    │               DUPLICATE PREVENTION                        │
    +-----------------------------------------------------------+
    │                                                           │
    │   REQUEST WITH STAN: 123456                               │
    │        │                                                  │
    │        ▼                                                  │
    │   SYSTEM CHECKS STAN                                      │
    │        │                                                  │
    │   ┌────┴────┐                                             │
    │   │         │                                             │
    │   ▼         ▼                                             │
    │ EXISTS    NOT EXISTS                                      │
    │   │         │                                             │
    │   ▼         ▼                                             │
    │ RETURN    PROCESS                                         │
    │ RESULT    TRANSACTION                                     │
    │              │                                            │
    │              ▼                                            │
    │          STORE RESULT                                     │
    │          WITH STAN                                        │
    │              │                                            │
    │              ▼                                            │
    │          RETURN RESULT                                    │
    │                                                           │
    └-----------------------------------------------------------+
```

## 25. Real-World Examples

### What Happens When a Card Is Tapped at a Supermarket

**A card tapped at a supermarket triggers a contactless authorization.** The NFC reader communicates with the chip. The chip generates an ARQC. The terminal sends the authorization request. The issuer approves or declines. The response is displayed.

### What Happens When Buying Online

**An online purchase uses card-not-present authorization.** The customer enters card details. The merchant sends the authorization request. CVV and AVS are checked. 3-D Secure may be triggered. The issuer approves or declines.

### What Happens at an ATM

**An ATM authorization is PIN-authenticated.** The customer inserts the card and enters a PIN. The ATM sends the authorization request. The issuer validates the PIN and checks the account. Cash is dispensed on approval.

### What Happens at a Fuel Pump

**A fuel pump pre-authorizes a fixed amount.** The pump validates the card. It authorizes a small amount (typically $1). The customer pumps fuel. The final amount is captured at settlement.

```
REAL-WORLD EXAMPLES

    +-----------------------------------------------------------+
    │               REAL-WORLD EXAMPLES                         │
    +-----------------------------------------------------------+
    │                                                           │
    │   SUPERMARKET (Contactless, $50)                          │
    │   ──────────────────────────────────────────────────────  │
    │   0 ms: Card tapped                                       │
    │   20 ms: NFC read complete                                │
    │   50 ms: ARQC generated                                   │
    │   100 ms: Authorization request sent                      │
    │   200 ms: Response received (Approved)                    │
    │   230 ms: Display shows "Approved"                        │
    │   250 ms: Receipt printed                                 │
    │                                                           │
    │   ONLINE PURCHASE (Card-Not-Present, $200)                │
    │   ──────────────────────────────────────────────────────  │
    │   0 ms: Customer enters card details                      │
    │   30 ms: CVV validated                                    │
    │   50 ms: AVS checked                                      │
    │   80 ms: 3-D Secure authentication triggered              │
    │   120 ms: 3-D Secure passed                               │
    │   150 ms: Authorization request sent                      │
    │   300 ms: Response received (Approved)                    │
    │   320 ms: Order confirmation displayed                    │
    │                                                           │
    │   ATM WITHDRAWAL (PIN, $200)                              │
    │   ──────────────────────────────────────────────────────  │
    │   0 ms: Card inserted                                     │
    │   20 ms: PIN entered                                      │
    │   50 ms: PIN block encrypted                              │
    │   80 ms: Authorization request sent                       │
    │   200 ms: PIN verified                                    │
    │   230 ms: Account checked                                 │
    │   260 ms: Cash dispensed                                  │
    │   280 ms: Receipt printed                                 │
    │                                                           │
    └-----------------------------------------------------------+
```

## 26. Future of Card Authorization

**AI will improve authorization accuracy.** Machine learning models will detect fraud faster. They will reduce false positives. They will optimize decisions.

**Tokenization will change authorization.** Tokens will replace PANs. Authorization will use tokens. The token will be validated.

**Biometric authentication will evolve.** Fingerprint and facial recognition will become more common. They will replace PINs. They will improve security.

**ISO 20022 will influence card authorization.** Richer data will be available. More fields will be transmitted. Authorization will be more informed.

```
FUTURE AUTHORIZATION

    +-----------------------------------------------------------+
    │               FUTURE AUTHORIZATION                        │
    +-----------------------------------------------------------+
    │                                                           │
    │   CURRENT:                                                │
    │   ┌───────────────────────────────────────────────────┐   │
    │   │  - PAN-based authorization                        │   │
    │   │  - PIN or CVV verification                        │   │
    │   │  - Rule-based risk engines                        │   │
    │   │  - 200-500 ms latency                             │   │
    │   └───────────────────────────────────────────────────┘   │
    │                           │                               │
    │                           ▼                               │
    │   FUTURE:                                                 │
    │   ┌───────────────────────────────────────────────────┐   │
    │   │  - Token-based authorization                      │   │
    │   │  - Biometric authentication                       │   │
    │   │  - AI-powered risk engines                        │   │
    │   │  - Sub-100 ms latency                             │   │
    │   │  - ISO 20022 rich data                            │   │
    │   │  - Real-time behavioral analysis                  │   │
    │   └───────────────────────────────────────────────────┘   │
    │                                                           │
    └-----------------------------------------------------------+
```

## 27. Summary

```
SUMMARY

    +-------------------------------------------------+
    |  WHAT IS CARD AUTHORIZATION?                    |
    |  Real-time approve/decline decision for        |
    |  card transactions                            |
    +-------------------------------------------------+

    +-------------------------------------------------+
    |  KEY CHARACTERISTICS                           |
    |  - Sub-second response                        |
    |  - Real-time validation                      |
    |  - Risk assessment                          |
    |  - Funds reservation                       |
    |  - Security critical                      |
    +-------------------------------------------------+

    +-------------------------------------------------+
    |  PARTICIPANTS                                  |
    |  Cardholder, Merchant, Terminal, Acquirer,     |
    |  Card Network, Issuer                         |
    +-------------------------------------------------+

    +-------------------------------------------------+
    |  DECISION PIPELINE                             |
    |  Validation → Authentication → Risk → Rules →  |
    |  Balance Check → Decision                     |
    +-------------------------------------------------+

    +-------------------------------------------------+
    |  RESPONSE CODES                                |
    |  00 (Approved), 05 (Do Not Honor), 51          |
    |  (Insufficient Funds), 91 (Issuer Unavailable) |
    +-------------------------------------------------+

    +-------------------------------------------------+
    |  KEY TAKEAWAY                                  |
    |  Card authorization is the most critical      |
    |  real-time decision in payments. It           |
    |  combines cryptography, risk assessment,      |
    |  business rules, and high-performance         |
    |  systems to make instant approve/decline      |
    |  decisions with sub-second latency.          |
    +-------------------------------------------------+
```

*This documentation belongs to https://github.com/InterCentury*
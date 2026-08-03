# American Express

## Documentation Overview

American Express (AmEx) is a globally integrated payments company that operates a unique closed-loop payment network. Unlike Visa and Mastercard, which are open-loop networks that connect issuing banks and acquiring banks, American Express acts as both the issuer and the network. This closed-loop architecture fundamentally changes how transactions are processed, how data flows, and how the business operates. This document provides a comprehensive engineering examination of American Express: its closed-loop architecture, network infrastructure, transaction processing, security technologies, and unique business model.

## Documentation Objectives

```
DOCUMENTATION OBJECTIVES

    ┌─────────────────────────────────────────────────────────────────────────────┐
    │                                                                             │
    │   Understand the unique closed-loop architecture of American Express        │
    │   Learn how AmEx differs from Visa and Mastercard                           │
    │   Study the AmEx network infrastructure and transaction flow                │
    │   Examine authorization engines and risk management systems                 │
    │   Understand tokenization, EMV implementation, and contactless              │
    │   payments                                                                  │
    │   Learn about Membership Rewards infrastructure                             │
    │   Study Global Network Services (GNS) and partner bank model                │
    │   Analyze engineering architecture and performance targets                  │
    │                                                                             │
    └─────────────────────────────────────────────────────────────────────────────┘
```

## 1. Introduction to American Express

**American Express (AmEx)** is a globally integrated payments company that operates a unique closed-loop payment network. Unlike Visa and Mastercard, which are open-loop networks that connect issuing banks and acquiring banks, American Express acts as both the issuer and the network.

**How it works:** AmEx issues cards directly to consumers and businesses. It operates its own payment processing network. It acquires merchants directly. It settles transactions internally. The entire transaction lifecycle—from card issuance to merchant settlement—occurs within the AmEx ecosystem.

```
AMERICAN EXPRESS DEFINITION

                         +---------------------------+
                         |   AMERICAN EXPRESS        |
                         |  Closed-loop payment      |
                         |  network and issuer       |
                         +-------------+-------------+
                                       |
          +----------------------------+----------------------------+
          │                            │                            │
          ▼                            ▼                            ▼
+---------------------------+  +---------------------------+  +---------------------------+
|  KEY CHARACTERISTICS      |  |  WHAT AMEX DOES           |  |  WHAT AMEX IS NOT         |
|  - Closed-loop network    |  |  - Issues cards           |  |  - Visa (open-loop)       |
|  - Issuer + network       |  |  - Processes payments     |  |  - Mastercard (open-loop) |
|  - Direct merchant        |  |  - Acquires merchants     |  |  - Bank (not a bank)      |
|    acquisition            |  |  - Settles transactions   |  |  - ACH network            |
|  - Premium brand          |  |  - Manages rewards        |  |  - Wire transfer system   |
|  - High spend customers   |  |  - Provides risk          |  |  - Payment processor      |
|  - High merchant fees     |  |    management             |  |  - Credit card issuer     |
|                           |  |                           |  |    (though it acts as     |
|                           |  |                           |  |    one)                   |
+---------------------------+  +---------------------------+  +---------------------------+
```

### Why Was American Express Created

**American Express was founded in 1850 as a freight forwarding and express mail company.** It evolved into a financial services company. It introduced the first travel charge card in 1958. It became a global payments company. It pioneered premium credit services.

### What Services Does American Express Provide

**American Express provides card issuance, payment processing, merchant acquisition, rewards, and travel services.** It issues consumer, business, and corporate cards. It processes payments through its network. It acquires merchants directly. It operates the Membership Rewards program. It provides travel and concierge services.

### What Makes AmEx Different from Other Card Networks

**AmEx is different because it operates a closed-loop network.** It controls the entire transaction flow. It does not rely on separate issuing banks. It does not rely on separate acquiring banks (in most cases). It has direct relationships with cardholders and merchants. It collects richer data. It can offer premium services.

## 2. History & Evolution

```
AMERICAN EXPRESS HISTORY

    1850: Founded as express mail and freight company
    │
    ▼
    1891: Introduces Travelers Cheques
    │
    ▼
    1958: First travel charge card
    │
    ▼
    1959: First plastic card
    │
    ▼
    1965: Introduces green card
    │
    ▼
    1984: Optima card (first revolving credit)
    │
    ▼
    1987: Gold Card introduced
    │
    ▼
    1991: Platinum Card introduced
    │
    ▼
    1995: Centurion Card (invitation only)
    │
    ▼
    2000s: Digital transformation
    │
    ▼
    2010s: Mobile payments, tokenization
    │
    ▼
    2020s: AI, real-time analytics, open banking
```

### How Did American Express Evolve

**American Express evolved from a freight company to a financial services leader.** It started as an express mail service. It introduced travelers cheques in 1891. It launched its first charge card in 1958. It expanded into premium cards. It built the Membership Rewards platform. It became a global payments company.

### When Did It Become a Card Company

**American Express became a card company in 1958 when it launched its first travel charge card.** This was a significant shift from its express mail and travelers cheque business.

### What Major Technological Milestones Has AmEx Introduced

**AmEx has introduced several technological milestones.** The first plastic card was introduced in 1959. The first contactless card was introduced in 2005. Tokenization was introduced in 2015. Real-time fraud detection was implemented. AI-powered risk engines were deployed.

## 3. American Express Business Model

### What Is a Closed-Loop Payment Network

**A closed-loop payment network is one where the network operator also acts as the issuer.** The same entity issues the cards, operates the network, and manages merchant relationships. There are no separate acquirers or issuers (in most cases).

```
OPEN LOOP VS CLOSED LOOP

                         +---------------------------+
                         |  OPEN VS CLOSED LOOP      |
                         +-------------+-------------+
                                       |
               +-----------------------+----------------------+
               │                                              │
               ▼                                              ▼
           +---------------------------+---------------------------+
           |  OPEN LOOP (Visa/MC)      |  CLOSED LOOP (AmEx)       |
           +---------------------------+---------------------------+
           |  Multiple issuers         │  Single issuer (AmEx)     |
           |  Multiple acquirers       │  Single acquirer (AmEx)   |
           |  Network only             │  Issuer + Network         |
           |  Bank relationships       │  Direct to consumer       |
           |  Interchange fees         │  Merchant fees            |
           |  Complex flow             │  Simplified flow          |
           |  4-party model            │  3-party model            |
           +---------------------------+---------------------------+
```

### How Does American Express Make Money

**American Express makes money through three primary channels.** Merchant discount fees are charged to merchants for accepting AmEx cards. Card fees are charged to cardholders for annual fees. Interest income is earned on revolving balances. Additionally, AmEx earns revenue from travel services, foreign exchange fees, and payment processing.

### Why Does AmEx Act as Both Issuer and Network

**AmEx acts as both issuer and network to control the entire customer experience.** It controls card issuance, transaction processing, and merchant relationships. It collects richer data. It can offer premium services. It can tailor rewards and benefits.

### What Advantages Does This Provide

**The closed-loop model provides several advantages.** It provides end-to-end control of the customer experience. It provides richer data for risk management and rewards. It enables faster settlement. It allows for customized products and services. It creates stronger customer relationships.

```
CLOSED-LOOP ADVANTAGES

                               +---------------------------+
                               |  CLOSED-LOOP ADVANTAGES   |
                               +-------------+-------------+
                                             |
                +----------------------------+----------------------------+
                │                            │                            │
                ▼                            ▼                            ▼
+---------------------------+  +---------------------------+  +---------------------------+
|  DATA ADVANTAGES          |  |  CONTROL ADVANTAGES       |  |  FINANCIAL ADVANTAGES     |
|  - Richer transaction     |  |  - End-to-end control     |  |  - Higher revenue per     |
|    data                   |  |  - Customized products    |  |    card                   |
|  - Full customer view     |  |  - Faster settlement      |  |  - More predictable       |
|  - Better risk analysis   |  |  - Better fraud           |  |    revenue                |
|  - Targeted rewards       |  |    detection              |  |  - Lower transaction      |
|                           |  |  - Consistent             |  |    costs                  |
|                           |  |    experience             |  |  - Merchant fees          |
+---------------------------+  +---------------------------+  +---------------------------+
```

## 4. Closed-Loop Network Architecture

### What Is a Closed-Loop Architecture

**A closed-loop architecture is a payment network where the same entity handles issuance, processing, and merchant acquisition.** The transaction flows through a single system. There are no external banks involved in the core transaction flow.

### How Does It Differ from Visa's Open-Loop Model

**Visa's open-loop model separates issuers and acquirers.** Multiple banks issue cards. Multiple banks acquire merchants. Visa operates the network between them. AmEx's closed-loop model combines all functions into one entity.

### Why Does AmEx Not Always Require an Acquiring Bank

**AmEx does not require an acquiring bank because it acquires merchants directly.** AmEx signs merchant agreements directly. It processes transactions directly. It settles funds directly to merchants.

### How Are Transaction Flows Simplified

**Transaction flows are simplified because there are fewer participants.** The cardholder pays AmEx. The merchant is paid by AmEx. There is no separate acquirer. There is no separate issuer.

```
CLOSED-LOOP ARCHITECTURE

    +-----------------------------------------------------------+
    │               CLOSED-LOOP ARCHITECTURE                    │
    +-----------------------------------------------------------+
    │                                                           │
    │   ┌───────────────────────────────────────────────────┐   │
    │   │              CARDHOLDER                           │   │
    │   │  - Applies for AmEx card                          │   │
    │   │  - Uses card for purchases                        │   │
    │   │  - Pays AmEx directly                             │   │
    │   └───────────────────────────────────────────────────┘   │
    │                           │                               │
    │                           │ Transaction                   │
    │                           ▼                               │
    │   ┌───────────────────────────────────────────────────┐   │
    │   │            AMERICAN EXPRESS NETWORK               │   │
    │   │                                                   │   │
    │   │  ┌───────────────────────────────────────────┐    │   │
    │   │  │  Card Issuance                            │    │   │
    │   │  │  - Credit approval                        │    │   │
    │   │  │  - Card production                        │    │   │
    │   │  │  - Account management                     │    │   │
    │   │  └───────────────────────────────────────────┘    │   │
    │   │                                                   │   │
    │   │  ┌───────────────────────────────────────────┐    │   │
    │   │  │  Transaction Processing                   │    │   │
    │   │  │  - Authorization                          │    │   │
    │   │  │  - Clearing                               │    │   │
    │   │  │  - Settlement                             │    │   │
    │   │  └───────────────────────────────────────────┘    │   │
    │   │                                                   │   │
    │   │  ┌───────────────────────────────────────────┐    │   │
    │   │  │  Merchant Acquisition                     │    │   │
    │   │  │  - Merchant onboarding                    │    │   │
    │   │  │  - Settlement                             │    │   │
    │   │  │  - Fee collection                         │    │   │
    │   │  └───────────────────────────────────────────┘    │   │
    │   └───────────────────────────────────────────────────┘   │
    │                           │                               │
    │                           │ Settlement                    │
    │                           ▼                               │
    │   ┌───────────────────────────────────────────────────┐   │
    │   │              MERCHANT                             │   │
    │   │  - Accepts AmEx cards                             │   │
    │   │  - Receives settlement from AmEx                  │   │
    │   └───────────────────────────────────────────────────┘   │
    │                                                           │
    └-----------------------------------------------------------+
```

## 5. American Express Network Infrastructure

### What Infrastructure Powers the AmEx Network

**AmEx operates a global network infrastructure with multiple data centers.** It uses high-performance servers and network equipment. It has redundant systems. It uses secure data centers.

### How Are Data Centers Organized

**AmEx data centers are organized in active-active configuration.** Multiple data centers operate simultaneously. They share processing load. They provide failover capability. They are geographically distributed.

### How Are Transactions Routed Internally

**Transactions are routed internally through AmEx's proprietary network.** The network uses load balancers and message queues. It processes transactions in real time. It routes to authorization engines.

### How Is Redundancy Implemented

**Redundancy is implemented at every level.** Multiple data centers provide geographic redundancy. Multiple servers provide hardware redundancy. Multiple network paths provide connectivity redundancy.

```
AMEX NETWORK INFRASTRUCTURE

    +-----------------------------------------------------------+
    │               AMEX NETWORK INFRASTRUCTURE                 │
    +-----------------------------------------------------------+
    │                                                           │
    │   ┌───────────────────────────────────────────────────┐   │
    │   │              DATA CENTER A (Primary)              │   │
    │   │  ┌─────────────────────────────────────────┐      │   │
    │   │  │  Web/API Gateway                        │      │   │
    │   │  └─────────────────────────────────────────┘      │   │
    │   │  ┌─────────────────────────────────────────┐      │   │
    │   │  │  Message Queue (Kafka)                  │      │   │
    │   │  └─────────────────────────────────────────┘      │   │
    │   │  ┌─────────────────────────────────────────┐      │   │
    │   │  │  Authorization Engine                   │      │   │
    │   │  └─────────────────────────────────────────┘      │   │
    │   │  ┌─────────────────────────────────────────┐      │   │
    │   │  │  Risk Engine (ML)                       │      │   │
    │   │  └─────────────────────────────────────────┘      │   │
    │   │  ┌─────────────────────────────────────────┐      │   │
    │   │  │  Settlement Engine                      │      │   │
    │   │  └─────────────────────────────────────────┘      │   │
    │   │  ┌─────────────────────────────────────────┐      │   │
    │   │  │  Database Cluster                       │      │   │
    │   │  └─────────────────────────────────────────┘      │   │
    │   └───────────────────────────────────────────────────┘   │
    │                           │                               │
    │                           │ Sync                          │
    │                           ▼                               │
    │   ┌───────────────────────────────────────────────────┐   │
    │   │              DATA CENTER B (Secondary)            │   │
    │   │  (Identical configuration, active-standby)        │   │
    │   └───────────────────────────────────────────────────┘   │
    │                                                           │
    └-----------------------------------------------------------+
```

## 6. American Express Card Products

### What Consumer Cards Does AmEx Offer

**AmEx offers several consumer cards.** The Green Card is the entry-level charge card. The Gold Card offers rewards on dining and groceries. The Platinum Card offers premium benefits. The Centurion Card is invitation-only. The Blue Cash Everyday offers cash back.

### What Business Cards Exist

**AmEx offers business cards for small and large businesses.** The Business Gold Card offers rewards on business spending. The Business Platinum Card offers premium benefits. The Corporate Card is for large enterprises.

### What Premium Cards Exist

**Premium cards include Platinum and Centurion.** Platinum offers airport lounge access, travel credits, and concierge services. Centurion (Black Card) is invitation-only with elite benefits.

### How Are Corporate Cards Different

**Corporate cards are designed for enterprise expense management.** They provide spending controls. They generate expense reports. They integrate with accounting systems. They offer liability protection.

## 7. Card Number Structure

### How Are American Express Card Numbers Structured

**American Express card numbers have 15 digits (2+6+7).** The first digit is 3. The second digit is 4 (for personal) or 7 (for corporate/business). The next 6 digits are the BIN/IIN. The next 7 digits are the account number. The last digit is the check digit.

### Why Do AmEx Cards Have 15 Digits

**AmEx uses 15 digits instead of the standard 16.** Visa and Mastercard use 16 digits. AmEx's 15-digit format is historical. It still fits within the Luhn algorithm.

### What Prefixes Are Used

**AmEx uses specific prefixes.** 34 and 37 are the primary prefixes. 34 is for personal cards. 37 is for corporate and business cards.

### How Is the Checksum Calculated

**The checksum is calculated using the Luhn algorithm.** The algorithm validates the card number. It detects common errors. It is used for quick validation.

```
AMEX CARD NUMBER STRUCTURE

    +-----------------------------------------------------------+
    │               AMEX CARD NUMBER STRUCTURE                  │
    +-----------------------------------------------------------+
    │                                                           │
    │   3   4   123456   7890123   8                            │
    │   │   │    │        │        │                            │
    │   │   │    │        │        └── Check Digit              │
    │   │   │    │        └─────────── Account Number (7 digits)│
    │   │   │    └──────────────────── BIN/IIN (6 digits)       │
    │   │   └───────────────────────── Card Type (4=Personal,   │
    │   │                             7=Corporate)              │
    │   └───────────────────────────── Industry (3=Travel &     │
    │                                   Entertainment)          │
    │                                                           │
    │   Example: 371234567890123                                │
    │   - 3: Travel/Entertainment industry                      │
    │   - 7: Corporate/Business card                            │
    │   - 123456: BIN/IIN                                       │
    │   - 7890123: Account number                               │
    │   - 8: Check digit                                        │
    │                                                           │
    └-----------------------------------------------------------+
```

### Luhn Algorithm Implementation

```
LUHN ALGORITHM (Python Example)

    +-----------------------------------------------------------+
    │               LUHN ALGORITHM IMPLEMENTATION               │
    +-----------------------------------------------------------+
    │                                                           │
    │   def validate_amex(number):                              │
    │       # Remove spaces                                     │
    │       number = number.replace(' ', '')                    │
    │                                                           │
    │       # Check prefix (34 or 37)                           │
    │       if not (number.startswith('34') or                  │
    │               number.startswith('37')):                   │
    │           return False                                    │
    │                                                           │
    │       # Check length (15 digits)                          │
    │       if len(number) != 15:                               │
    │           return False                                    │
    │                                                           │
    │       # Luhn algorithm                                    │
    │       sum = 0                                             │
    │       for i in range(len(number)):                        │
    │           digit = int(number[i])                          │
    │           if i % 2 == 0:                                  │
    │               digit *= 2                                  │
    │               if digit > 9:                               │
    │                   digit -= 9                              │
    │           sum += digit                                    │
    │                                                           │
    │       return sum % 10 == 0                                │
    │                                                           │
    └-----------------------------------------------------------+
```

## 8. BIN/IIN Allocation

### What BIN Ranges Belong to American Express

**AmEx BINs are 6-digit numbers beginning with 34 or 37.** Common BINs include 34xxxx and 37xxxx. These are registered with the ISO.

### How Are BINs Assigned

**BINs are assigned by the ISO** (International Organization for Standardization). AmEx controls its own BIN allocation. It manages the range within its assigned prefixes.

### How Do Merchants Identify an AmEx Card

**Merchants identify AmEx cards by the prefix.** 34 or 37 indicates AmEx. They may also use BIN lookup tables. Payment processors validate the prefix.

## 9. Transaction Flow Inside AmEx

### How Does an AmEx Transaction Differ from Visa

**An AmEx transaction is simpler because there is no separate issuer or acquirer.** The transaction goes from merchant to AmEx to merchant. There is no external issuer (except through GNS). There is no external acquirer (except through GNS).

### What Systems Participate

**The transaction flow involves merchant systems, AmEx authorization, risk engines, settlement systems, and merchant systems.** The merchant submits the transaction. AmEx authorizes it. AmEx settles it.

### How Are Approvals Returned

**Approvals are returned in real time.** The authorization response is sent back to the merchant. The response includes approval code. The merchant completes the sale.

### How Are Declines Handled

**Declines are returned with a reason code.** The merchant receives the decline. The customer may be asked for an alternate payment method.

```
AMEX TRANSACTION FLOW

    +-----------------------------------------------------------+
    │               AMEX TRANSACTION FLOW                       │
    +-----------------------------------------------------------+
    │                                                           │
    │   CUSTOMER                                                │
    │        │                                                  │
    │        │ Presents AmEx card                               │
    │        ▼                                                  │
    │   MERCHANT                                                │
    │        │                                                  │
    │        │ Authorization request                            │
    │        ▼                                                  │
    │   ┌───────────────────────────────────────────────────┐   │
    │   │           AMERICAN EXPRESS NETWORK                │   │
    │   │                                                   │   │
    │   │  1. Validate merchant                             │   │
    │   │  2. Check card validity                           │   │
    │   │  3. Verify account status                         │   │
    │   │  4. Check available credit                        │   │
    │   │  5. Run fraud checks                              │   │
    │   │  6. Make authorization decision                   │   │
    │   │  7. Reserve funds (or charge)                     │   │
    │   │  8. Return approval/decline                       │   │
    │   └───────────────────────────────────────────────────┘   │
    │        │                                                  │
    │        │ Authorization response                           │
    │        ▼                                                  │
    │   MERCHANT                                                │
    │        │                                                  │
    │        │ Settlement                                       │
    │        ▼                                                  │
    │   ┌───────────────────────────────────────────────────┐   │
    │   │           AMERICAN EXPRESS SETTLEMENT             │   │
    │   │  1. Capture transaction                           │   │
    │   │  2. Debit cardholder's account                    │   │
    │   │  3. Credit merchant's account                     │   │
    │   │  4. Post to ledgers                               │   │
    │   │  5. Generate statement                            │   │
    │   └───────────────────────────────────────────────────┘   │
    │                                                           │
    └-----------------------------------------------------------+
```

## 10. Authorization Engine

### How Does AmEx Authorize Transactions

**AmEx authorizes transactions through a multi-stage decision engine.** The system validates the card. It checks account status. It verifies available credit or charge limit. It applies business rules. It makes a decision.

### What Decision Engines Are Used

**AmEx uses multiple decision engines.** Real-time rule engines apply business rules. Machine learning models predict risk. Fraud detection systems identify suspicious activity. Spending limit engines calculate available credit.

### How Are Spending Limits Calculated

**Spending limits are calculated based on card type, account history, credit profile, and transaction patterns.** AmEx uses proprietary algorithms. It considers historical spending. It considers payment history. It considers risk scores.

### How Are Real-Time Risk Checks Performed

**Real-time risk checks use machine learning models.** The models are trained on historical data. They analyze transaction patterns. They detect anomalies. They make decisions in milliseconds.

## 11. Risk & Fraud Systems

### How Does AmEx Detect Fraud

**AmEx detects fraud through multiple layers.** Rule-based systems flag suspicious patterns. Machine learning models predict fraud probability. Behavioral analysis identifies anomalies. Real-time monitoring detects unusual activity.

### What Machine Learning Systems Are Used

**AmEx uses advanced machine learning systems.** Deep learning models analyze transaction patterns. Gradient boosting models predict risk. Ensemble models combine multiple algorithms.

### How Are Unusual Transactions Identified

**Unusual transactions are identified through anomaly detection.** The system compares transactions to historical patterns. It looks for deviations. It flags anomalies for review.

### How Are False Positives Minimized

**False positives are minimized through model tuning and feedback loops.** Models are continuously retrained. False positives are analyzed. The system learns from corrections.

```
FRAUD DETECTION ARCHITECTURE

    +-----------------------------------------------------------+
    │               FRAUD DETECTION ARCHITECTURE                │
    +-----------------------------------------------------------+
    │                                                           │
    │   TRANSACTION DATA                                        │
    │        │                                                  │
    │        ▼                                                  │
    │   ┌───────────────────────────────────────────────────┐   │
    │   │           FEATURE EXTRACTION                      │   │
    │   │  - Merchant category                              │   │
    │   │  - Transaction amount                             │   │
    │   │  - Location                                       │   │
    │   │  - Device fingerprint                             │   │
    │   │  - Historical patterns                            │   │
    │   └───────────────────────────────────────────────────┘   │
    │        │                                                  │
    │        ▼                                                  │
    │   ┌───────────────────────────────────────────────────┐   │
    │   │           RULE-BASED ENGINE                       │   │
    │   │  - Velocity checks                                │   │
    │   │  - Amount limits                                  │   │
    │   │  - Geographic checks                              │   │
    │   │  - Card type checks                               │   │
    │   └───────────────────────────────────────────────────┘   │
    │        │                                                  │
    │        ▼                                                  │
    │   ┌───────────────────────────────────────────────────┐   │
    │   │           MACHINE LEARNING MODELS                 │   │
    │   │  - Random Forest                                  │   │
    │   │  - Gradient Boosting                              │   │
    │   │  - Deep Neural Networks                           │   │
    │   │  - Ensemble Models                                │   │
    │   └───────────────────────────────────────────────────┘   │
    │        │                                                  │
    │        ▼                                                  │
    │   ┌───────────────────────────────────────────────────┐   │
    │   │           RISK SCORE                              │   │
    │   │  - Low: Accept                                    │   │
    │   │  - Medium: Review                                 │   │
    │   │  - High: Decline                                  │   │
    │   └───────────────────────────────────────────────────┘   │
    │                                                           │
    └-----------------------------------------------------------+
```

## 12. Merchant Acceptance

### How Do Merchants Accept American Express

**Merchants accept American Express through direct agreements or through payment processors.** Direct agreements are signed with AmEx. Payment processors integrate with AmEx's network.

### Why Have AmEx Merchant Fees Traditionally Been Higher

**AmEx merchant fees have been higher due to the premium customer base, rewards program, and closed-loop model.** AmEx charges higher fees because it provides premium cardholders with high spending power. Rewards are funded in part by merchant fees.

### How Are Merchant Agreements Managed

**Merchant agreements are managed through AmEx's merchant services.** Agreements specify fees, settlement terms, and service levels. They are renewed periodically.

```
MERCHANT ACCEPTANCE FLOW

    +-----------------------------------------------------------+
    │               MERCHANT ACCEPTANCE FLOW                    │
    +-----------------------------------------------------------+
    │                                                           │
    │   MERCHANT REQUESTS ACCEPTANCE                            │
    │        │                                                  │
    │        ▼                                                  │
    │   AMEX MERCHANT ONBOARDING                                │
    │        │                                                  │
    │        ▼                                                  │
    │   DUE DILIGENCE                                           │
    │        │                                                  │
    │        ▼                                                  │
    │   MERCHANT AGREEMENT EXECUTED                             │
    │        │                                                  │
    │        ▼                                                  │
    │   TECHNICAL INTEGRATION                                   │
    │        │                                                  │
    │        ▼                                                  │
    │   READY TO ACCEPT                                         │
    │                                                           │
    │   +---------------------------------------------------+   │
    │   │  INTEGRATION OPTIONS:                             │   │
    │   │  - Direct API                                     │   │
    │   │  - Payment gateway (Stripe, Adyen)                │   │
    │   │  - POS terminal integration                       │   │
    │   │  - Online checkout integration                    │   │
    │   └---------------------------------------------------+   │
    │                                                           │
    └-----------------------------------------------------------+
```

## 13. American Express APIs

### What APIs Does American Express Provide

**AmEx provides several APIs for developers.** Transaction APIs process payments. Token APIs generate and manage tokens. Reporting APIs provide settlement data. Authentication APIs handle security.

### How Do Developers Integrate with AmEx

**Developers integrate through AmEx's developer portal.** They register for API keys. They implement authentication. They make API calls. They handle webhooks for notifications.

### What Authentication Methods Are Used

**AmEx uses OAuth 2.0 for authentication.** API keys identify the client. Access tokens authorize requests. Digital signatures ensure integrity.

## 14. Digital Wallet Integration

### How Does AmEx Integrate with Apple Pay

**AmEx integrates with Apple Pay through network tokenization.** The card is provisioned to the Apple Pay wallet. A token is generated. The token is stored on the device. Payments use the token.

### How Does AmEx Integrate with Google Wallet

**AmEx integrates with Google Wallet similarly.** Tokens are generated and stored on the device. Payments use the token. The real PAN is never shared.

### How Are Tokens Generated

**Tokens are generated by AmEx's token service provider.** The PAN is sent to the TSP. The TSP returns a token. The token is device-specific.

## 15. Tokenization

### How Does AmEx Implement Network Tokenization

**AmEx implements network tokenization through its token service provider.** The token replaces the PAN. The token is domain-controlled. It works for specific merchants or channels.

### How Are Payment Tokens Managed

**Tokens are managed through AmEx's token management system.** Tokens are generated for each device or merchant. They have defined lifecycles. They can be suspended or revoked.

### How Are Tokens Mapped to PANs

**Tokens are mapped to PANs through AmEx's token vault.** The vault securely stores the mapping. Only authorized systems can access it. The real PAN is never exposed.

```
TOKENIZATION ARCHITECTURE

    +-----------------------------------------------------------+
    │               TOKENIZATION ARCHITECTURE                   │
    +-----------------------------------------------------------+
    │                                                           │
    │   CARD PAN (371234567890123)                              │
    │        │                                                  │
    │        │ Request token                                    │
    │        ▼                                                  │
    │   ┌───────────────────────────────────────────────────┐   │
    │   │           AMEX TOKEN SERVICE PROVIDER             │   │
    │   │                                                   │   │
    │   │  1. Validate PAN                                  │   │
    │   │  2. Generate token (unique per device)            │   │
    │   │  3. Store mapping in secure vault                 │   │
    │   │  4. Return token                                  │   │
    │   └───────────────────────────────────────────────────┘   │
    │        │                                                  │
    │        │ Payment token                                    │
    │        ▼                                                  │
    │   ┌───────────────────────────────────────────────────┐   │
    │   │           DEVICE / MERCHANT                       │   │
    │   │                                                   │   │
    │   │  - Store token (not PAN)                          │   │
    │   │  - Send token for payments                        │   │
    │   │  - Token has no value if intercepted              │   │
    │   └───────────────────────────────────────────────────┘   │
    │                                                           │
    └-----------------------------------------------------------+
```

## 16. Contactless Payments

### How Does AmEx Support NFC Payments

**AmEx supports NFC (Near-Field Communication) payments.** AmEx cards include an NFC chip. The chip enables tap-to-pay. Mobile wallets use NFC.

### Which EMV Contactless Specifications Are Used

**AmEx uses EMVCo contactless specifications.** It follows EMV Contactless standards. It supports quick-read and quick-pass functionality.

### How Are Cryptograms Generated

**Cryptograms are generated using the EMV algorithms.** The card generates a dynamic cryptogram. The cryptogram includes transaction-specific data. It is validated by AmEx.

## 17. EMV Implementation

### How Does AmEx Implement EMV

**AmEx implements EMV chip technology.** Cards have an EMV chip. The chip stores card data securely. The chip generates dynamic cryptograms.

### What Cryptographic Methods Are Used

**AmEx uses cryptographic methods for EMV.** AES is used for encryption. RSA is used for authentication. SHA is used for hashing.

### How Are Dynamic Authentication Values Generated

**Dynamic authentication values are generated by the EMV chip.** The chip uses a secret key. It combines transaction data with the key. It produces a cryptogram.

## 18. Rewards Infrastructure

### How Does Membership Rewards Work

**Membership Rewards is AmEx's proprietary rewards program.** Cardholders earn points for purchases. Points can be redeemed for travel, merchandise, or gift cards. Points can be transferred to partners.

### How Are Reward Points Calculated

**Reward points are calculated based on spending, card type, and bonus categories.** Each dollar spent earns a certain number of points. Bonus categories earn more points.

### How Are Points Redeemed

**Points are redeemed through AmEx's redemption portal.** Cardholders can book travel. They can purchase gift cards. They can transfer to partners.

### How Are Partner Integrations Implemented

**Partner integrations use APIs.** Partners access the rewards system. They validate and redeem points. They confirm redemptions.

```
MEMBERSHIP REWARDS PIPELINE

    +-----------------------------------------------------------+
    │               MEMBERSHIP REWARDS PIPELINE                 │
    +-----------------------------------------------------------+
    │                                                           │
    │   PURCHASE                                                │
    │        │                                                  │
    │        ▼                                                  │
    │   TRANSACTION DATA                                        │
    │        │                                                  │
    │        ▼                                                  │
    │   ┌───────────────────────────────────────────────────┐   │
    │   │           REWARDS ENGINE                          │   │
    │   │                                                   │   │
    │   │  1. Identify merchant category                    │   │
    │   │  2. Apply bonus multipliers                       │   │
    │   │  3. Calculate points earned                       │   │
    │   │  4. Credit to member account                      │   │
    │   │  5. Update point balance                          │   │
    │   └───────────────────────────────────────────────────┘   │
    │        │                                                  │
    │        ▼                                                  │
    │   POINTS ACCOUNT                                          │
    │        │                                                  │
    │        │ Redemption request                               │
    │        ▼                                                  │
    │   ┌───────────────────────────────────────────────────┐   │
    │   │           REDEMPTION PORTAL                       │   │
    │   │                                                   │   │
    │   │  - Travel booking                                 │   │
    │   │  - Gift card selection                            │   │
    │   │  - Partner transfers                              │   │
    │   │  - Statement credit                               │   │
    │   └───────────────────────────────────────────────────┘   │
    │                                                           │
    └-----------------------------------------------------------+
```

## 19. Corporate & Commercial Cards

### What Enterprise Features Exist

**Corporate cards provide enterprise features.** Spending controls limit employee spending. Expense reports track spending. Approval workflows enforce policies. Liability protection limits corporate exposure.

### How Are Expense Reports Generated

**Expense reports are generated automatically.** Transactions are captured. Receipts are stored. Reports are compiled. Approvals are routed.

### How Are Spending Controls Implemented

**Spending controls are implemented through the corporate portal.** Limits are set per card. Categories are restricted. Approvals are required for large purchases.

## 20. Global Network Services (GNS)

### What Is Global Network Services (GNS)

**Global Network Services (GNS) is AmEx's partner bank program.** It allows partner banks to issue AmEx-branded cards. It expands AmEx's global reach. It uses AmEx's network.

### How Do Partner Banks Issue AmEx Cards

**Partner banks issue cards through GNS.** They use their own banking infrastructure. They use AmEx's network. They share revenue with AmEx.

### How Does GNS Expand AmEx Globally

**GNS expands AmEx globally where it does not have direct operations.** Partner banks have local expertise. They have local licenses. They have local customer relationships.

## 21. American Express vs Visa vs Mastercard

### How Does AmEx Differ from Visa

**AmEx differs from Visa in several ways.** AmEx is a closed-loop network. Visa is an open-loop network. AmEx issues cards directly. Visa does not issue cards. AmEx acquires merchants directly. Visa does not acquire merchants.

### How Does AmEx Differ from Mastercard

**AmEx differs from Mastercard similarly.** AmEx is closed-loop. Mastercard is open-loop. AmEx controls the full stack. Mastercard is a network only.

### What Advantages Does AmEx Provide

**AmEx provides several advantages.** It offers premium customer service. It provides rich rewards programs. It has high spending limits. It offers exclusive benefits.

### What Disadvantages Exist

**AmEx has some disadvantages.** Merchant fees are higher. It is less widely accepted. It has fewer cards issued globally.

```
AMEX VS VISA VS MASTERCARD

                         +---------------------------+
                         |  AMEX VS VISA VS MC     |
                         +-------------+-------------+
                                       |
          +----------------------------+----------------------------+
          │                                                         │
          ▼                                                         ▼
+---------------------------+                            +---------------------------+
|  AMERICAN EXPRESS        |                            |  VISA                    |
|  - Closed-loop           |                            |  - Open-loop             |
|  - Issuer + network     |                            |  - Network only          |
|  - Direct merchant      |                            |  - Multiple issuers      |
|  - 3-party model       |                            |  - 4-party model        |
|  - Higher fees         |                            |  - Lower fees           |
|  - Premium brand       |                            |  - Widely accepted      |
|  - 15-digit numbers    |                            |  - 16-digit numbers     |
|  - Prefix: 34, 37      |                            |  - Prefix: 4           |
+---------------------------+                            +---------------------------+
          │                                              │
          ▼                                              ▼
+---------------------------+              +---------------------------+---------------------------+
|  MASTERCARD              |
|  - Open-loop            |
|  - Network only         |
|  - Multiple issuers     |
|  - 4-party model        |
|  - Lower fees           |
|  - Widely accepted      |
|  - 16-digit numbers     |
|  - Prefix: 51-55,      |
|    2221-2720           |
+---------------------------+
```

## 22. Engineering Architecture

### How Are AmEx Systems Architected

**AmEx systems are architected with microservices.** Services are organized by function. They communicate through APIs. They are deployed in containers.

### How Is Scalability Achieved

**Scalability is achieved through horizontal scaling.** Services are replicated across nodes. Load balancers distribute traffic. Databases are sharded.

### How Are Transactions Distributed

**Transactions are distributed across processing nodes.** They are routed to healthy nodes. State is managed centrally. Failover is automatic.

### What Reliability Targets Exist

**AmEx targets 99.99% availability.** This is less than 1 hour of downtime per year. Redundancy is built in. Failover is automatic.

## 23. Security Technologies

### What Encryption Methods Does AmEx Use

**AmEx uses AES-256 for data at rest.** TLS 1.3 is used for data in transit. RSA and ECC are used for key exchange.

### How Are HSMs Used

**HSMs are used for key storage and cryptographic operations.** They are tamper-resistant. They protect sensitive keys. They perform encryption and signing.

### How Are Keys Rotated

**Keys are rotated on a regular schedule.** The schedule is determined by security policy. Rotation is automated. Old keys are retired.

### How Are Credentials Protected

**Credentials are protected through secure storage and access controls.** Passwords are hashed. Tokens are encrypted. Access is audited.

## 24. Performance & Reliability

### What Latency Targets Exist

**AmEx targets sub-second authorization latency.** End-to-end latency is typically under 500ms. The goal is 99% under 300ms.

### How Many TPS Can AmEx Process

**AmEx can process thousands of transactions per second.** Peak volume is during holidays. The system scales automatically.

### How Is High Availability Implemented

**High availability is implemented through redundancy.** Multiple data centers. Multiple servers. Multiple network paths.

## 25. Real-World Transaction Example

### What Happens When an AmEx Card Is Tapped

**When an AmEx card is tapped, the NFC reader communicates with the chip.** The chip generates a cryptogram. The cryptogram is sent to AmEx. AmEx validates and approves.

### How Does an Online AmEx Payment Work

**An online AmEx payment uses the AmEx API.** The merchant sends a request. AmEx authorizes it. The merchant receives the response.

### How Does an International AmEx Payment Work

**An international AmEx payment uses the AmEx network.** The merchant is in one country. AmEx processes globally. Settlement is in the merchant's currency.

```
REAL-WORLD TRANSACTION EXAMPLE

    +-----------------------------------------------------------+
    │               REAL-WORLD TRANSACTION EXAMPLE              │
    +-----------------------------------------------------------+
    │                                                           │
    │   CUSTOMER BUYS $100 ONLINE                             │
    │        │                                                  │
    │        │ Enters AmEx card details                       │
    │        ▼                                                  │
    │   MERCHANT                                                │
    │        │                                                  │
    │        │ API request to AmEx                            │
    │        ▼                                                  │
    │   AMEX NETWORK                                            │
    │        │                                                  │
    │        │ Authorization (50ms)                           │
    │        │ Risk check (30ms)                             │
    │        │ Decision (10ms)                              │
    │        ▼                                                  │
    │   AMEX RESPONSE (APPROVED)                               │
    │        │                                                  │
    │        │ Approval code: 123456                          │
    │        ▼                                                  │
    │   MERCHANT                                                │
    │        │                                                  │
    │        │ Settlement (end of day)                        │
    │        ▼                                                  │
    │   AMEX SETTLEMENT                                         │
    │        │                                                  │
    │        │ Merchant receives $96 (less $4 fee)            │
    │                                                           │
    └-----------------------------------------------------------+
```

## 26. Future Technologies

### How Is AmEx Adopting ISO 20022

**AmEx is adopting ISO 20022 for richer data.** It provides more detailed transaction information. It improves reconciliation. It supports better analytics.

### How Is AI Used at American Express

**AI is used for fraud detection, credit decisions, and customer service.** Machine learning models predict fraud. AI chatbots handle customer queries. AI optimizes rewards.

### How Is Tokenization Evolving

**Tokenization is evolving to support more use cases.** Device tokens are used for mobile wallets. Merchant tokens are used for recurring payments. Network tokens work across channels.

### How Are Digital Identities Changing AmEx

**Digital identities are enabling new experiences.** Biometrics are used for authentication. Digital IDs are used for onboarding. Identity verification is automated.

## 27. Summary

```
SUMMARY

    +-------------------------------------------------+
    |  WHAT IS AMERICAN EXPRESS?                     |
    |  Closed-loop payment network and issuer       |
    |  End-to-end control of transactions          |
    +-------------------------------------------------+

    +-------------------------------------------------+
    |  KEY CHARACTERISTICS                           |
    |  - Closed-loop network                        |
    |  - Issuer + network in one                   |
    |  - Direct merchant relationships             |
    |  - Premium brand                            |
    +-------------------------------------------------+

    +-------------------------------------------------+
    |  BUSINESS MODEL                               |
    |  Merchant fees + card fees + interest         |
    |  Rewards funded by merchant fees              |
    +-------------------------------------------------+

    +-------------------------------------------------+
    |  CARD NUMBER STRUCTURE                         |
    |  15 digits (2+6+7)                           |
    |  Prefix 34 or 37                            |
    +-------------------------------------------------+

    +-------------------------------------------------+
    |  COMPARISON                                    |
    |  AmEx: Closed-loop, 3-party model            |
    |  Visa: Open-loop, 4-party model              |
    |  Mastercard: Open-loop, 4-party model       |
    +-------------------------------------------------+

    +-------------------------------------------------+
    |  KEY TAKEAWAY                                  |
    |  American Express is unique in the payments   |
    |  industry. Its closed-loop architecture      |
    |  provides end-to-end control, richer data,   |
    |  and a premium customer experience. It      |
    |  operates as both issuer and network,       |
    |  simplifying the transaction flow and      |
    |  enabling personalized services.          |
    +-------------------------------------------------+
```

*This documentation belongs to https://github.com/InterCentury*
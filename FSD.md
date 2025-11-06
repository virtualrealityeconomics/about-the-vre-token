# Functional Specification Document (FSD)
## VRE Platform - Virtual Reality Economics

**Version**: 1.0
**Date**: November 2024
**Status**: Living Document

---

## Table of Contents

1. [Executive Summary](#1-executive-summary)
2. [User Workflows](#2-user-workflows)
3. [Feature Specifications](#3-feature-specifications)
4. [User Interface Requirements](#4-user-interface-requirements)
5. [Data Models](#5-data-models)
6. [Business Logic](#6-business-logic)
7. [Edge Cases and Error Handling](#7-edge-cases-and-error-handling)

---

## 1. Executive Summary

### 1.1 Document Purpose

This Functional Specification Document (FSD) details how users interact with the VRE platform, describing workflows, user interfaces, and business logic that brings the technical requirements to life.

### 1.2 Platform Vision

VRE creates a blockchain-powered ecosystem where creators, backers, builders, and consumers interact through transparent, trustless economic relationships enabled by smart contracts and cryptographic verification.

---

## 2. User Workflows

### 2.1 New User Onboarding

**Workflow**: First-Time Creator Registration

1. User navigates to vre.life
2. User clicks "Connect Wallet" button
3. System displays wallet provider selection modal (Phantom, Solflare, Ledger, etc.)
4. User selects wallet provider
5. Wallet extension prompts for connection approval
6. User approves connection
7. System detects new wallet address
8. System displays profile creation form:
   - Display name (required)
   - Profile bio (optional)
   - Profile image upload (optional)
   - User type selection: Creator / Backer / Builder / Consumer
9. User completes form and submits
10. System creates profile record in database
11. System displays success message and dashboard

**Expected Duration**: 2-3 minutes

**Exit Points**:
- User cancels wallet connection → Return to landing page
- User closes profile form → Save draft and allow completion later

---

### 2.2 Creator Video Upload Workflow

**Workflow**: Uploading and Validating Content

1. Creator navigates to "Upload Video" section
2. Creator clicks "Select Video File" button
3. System opens file browser dialog
4. Creator selects video file (MP4, MOV, AVI supported)
5. System validates file:
   - File size ≤ 5GB
   - File format is supported
   - File is not corrupted
6. System displays upload progress bar
7. System generates cryptographic hash (SHA-256) during upload
8. System uploads video to IPFS/Arweave
9. System displays metadata entry form:
   - Video title (required, max 100 characters)
   - Description (required, max 5000 characters)
   - Tags (optional, max 10 tags)
   - Thumbnail (auto-generated or custom upload)
   - Visibility: Public / Unlisted / Private
10. Creator completes metadata and clicks "Publish"
11. System creates blockchain transaction:
    - Video hash
    - Timestamp
    - Creator wallet address
    - IPFS CID
12. Creator approves transaction in wallet
13. Transaction confirms on blockchain
14. System displays success message with video URL
15. Video appears in creator's profile

**Expected Duration**: 5-10 minutes (depending on file size)

**Error Handling**:
- File too large → Display error: "File exceeds 5GB limit. Please compress or split."
- Unsupported format → Display error: "Format not supported. Please use MP4, MOV, or AVI."
- Upload failure → "Upload failed. Retrying..." (auto-retry 3 times)
- Transaction rejection → "Transaction cancelled. Video saved as draft."

---

### 2.3 Backer Investment Workflow

**Workflow**: Investing in a Creator

1. Backer browses creator profiles
2. Backer clicks on creator profile to view details:
   - Total backers
   - Total investment received
   - Content portfolio
   - Performance metrics
   - Investment terms
3. Backer clicks "Back This Creator" button
4. System displays investment modal:
   - Investment amount (VRE tokens)
   - Expected revenue share percentage (calculated)
   - Investment duration (permanent, transferable)
   - Risk disclaimer
5. Backer enters investment amount
6. System calculates and displays:
   - Revenue share percentage
   - Estimated monthly returns
   - Transaction fee
7. Backer clicks "Confirm Investment"
8. System creates smart contract transaction
9. Backer approves transaction in wallet
10. Smart contract executes:
    - Transfers VRE from backer to creator backing pool
    - Records investment on-chain
    - Updates revenue share calculations
11. Transaction confirms
12. System displays success message
13. Investment appears in backer's portfolio
14. Creator receives notification of new backer

**Expected Duration**: 2-3 minutes

**Business Rules**:
- Minimum investment: 100 VRE
- Maximum investment per backer: 10% of creator's total backing
- Revenue share calculated as: (Individual Investment / Total Investment) × Creator Revenue Share Pool

---

### 2.4 Marketplace Purchase Workflow

**Workflow**: Buying Digital Goods

1. Consumer browses marketplace
2. Consumer applies filters:
   - Category (digital assets, physical goods, services)
   - Price range
   - Seller rating
3. Consumer clicks on product listing
4. System displays product details:
   - Title and description
   - Price in VRE
   - Seller information
   - Reviews and ratings
   - Product images/preview
5. Consumer clicks "Purchase with VRE"
6. System displays purchase confirmation modal:
   - Product details
   - Total price
   - Escrow terms (funds held for 7 days or until consumer confirms delivery)
   - Dispute resolution clause
7. Consumer clicks "Confirm Purchase"
8. System creates escrow smart contract transaction
9. Consumer approves transaction in wallet
10. Smart contract executes:
    - Transfers VRE from consumer to escrow
    - Notifies seller of purchase
    - Starts delivery countdown
11. Seller receives notification
12. Seller delivers product/service (digital download link or shipping confirmation)
13. Consumer receives product
14. Consumer clicks "Confirm Receipt" (or automatic after 7 days)
15. Smart contract releases funds from escrow to seller
16. Both parties can leave reviews

**Expected Duration**: 3-5 minutes

**Escrow Logic**:
- Funds locked in smart contract
- Consumer has 7 days to dispute
- If disputed, enters dispute resolution workflow
- If no dispute, funds auto-release after 7 days

---

### 2.5 Dispute Resolution Workflow

**Workflow**: Resolving a Marketplace Dispute

1. Consumer or seller initiates dispute by clicking "Open Dispute" on transaction
2. System displays dispute form:
   - Reason for dispute (predefined categories)
   - Evidence submission (text description, images, documents)
   - Desired outcome
3. Disputing party submits dispute
4. System notifies other party
5. Other party has 48 hours to respond with counter-evidence
6. System randomly selects 5 token-holder arbitrators:
   - Must hold minimum 1,000 VRE
   - Cannot be involved in the transaction
   - Weighted selection based on token holdings (quadratic)
7. Arbitrators receive notification
8. Arbitrators review evidence for 7 days
9. System displays arbitration interface:
   - All evidence from both parties
   - Transaction history
   - Previous dispute records (if any)
10. Each arbitrator submits vote: Favor Consumer / Favor Seller / Split Decision
11. System tallies votes
12. Majority decision wins (3+ votes required)
13. Smart contract executes outcome:
    - Full refund to consumer, OR
    - Release funds to seller, OR
    - Split funds 50/50
14. Both parties notified of decision
15. Arbitrators receive small VRE compensation for participation
16. Decision recorded permanently on-chain

**Expected Duration**: 7-10 days

**Arbitrator Incentives**:
- Compensation: 0.5% of disputed amount split among arbitrators
- Reputation score tracking
- Voting history visible for transparency

---

### 2.6 Developer Code Ownership Workflow

**Workflow**: Claiming Code Contribution

1. Builder completes code contribution to VRE ecosystem project
2. Builder commits code to GitHub repository
3. Builder navigates to "Code Ownership" section in VRE platform
4. Builder clicks "Register Contribution"
5. System displays contribution form:
   - GitHub repository URL
   - Commit hash
   - Lines of code contributed
   - Module/feature description
   - License type
6. Builder completes form and submits
7. System verifies:
   - GitHub commit exists
   - Builder owns the GitHub account
   - Code hasn't been previously claimed
8. System creates blockchain transaction:
   - Commit hash
   - Repository identifier
   - Builder wallet address
   - Timestamp
9. Builder approves transaction in wallet
10. Transaction confirms on blockchain
11. System displays success message
12. Contribution appears in builder's portfolio
13. Builder can now claim compensation when code is used commercially

**Expected Duration**: 3-5 minutes

**Verification Process**:
- GitHub OAuth integration for identity verification
- Commit signature validation
- Code uniqueness check via hash comparison

---

### 2.7 Governance Participation Workflow

**Workflow**: Creating and Voting on Proposals

**Creating a Proposal:**

1. Token holder navigates to "Governance" section
2. Token holder clicks "Create Proposal"
3. System verifies minimum token requirement (10,000 VRE to propose)
4. System displays proposal form:
   - Proposal title (max 100 characters)
   - Detailed description (max 10,000 characters)
   - Proposal category: Protocol / Treasury / Partnership / Feature
   - Voting options (Yes/No or custom options)
   - Voting duration (7, 14, or 30 days)
   - Required quorum (% of total supply)
5. Token holder completes form and submits
6. System creates proposal on-chain
7. Token holder approves transaction
8. Proposal enters "Active" state
9. Community notified via platform and email

**Voting on a Proposal:**

1. Token holder views active proposals
2. Token holder clicks on proposal to view details
3. System displays:
   - Full proposal description
   - Current vote tally
   - Time remaining
   - Proposer information
   - Community discussion
4. Token holder clicks "Vote"
5. System displays voting modal:
   - Voting power (based on token holdings, quadratic)
   - Vote options
   - "Vote with conviction" slider (lock tokens for stronger vote)
6. Token holder selects vote and conviction level
7. Token holder clicks "Submit Vote"
8. System creates voting transaction
9. Token holder approves in wallet
10. Vote recorded on-chain
11. Vote tally updates in real-time
12. When voting period ends:
    - System tallies final results
    - If quorum met and majority achieved → Proposal passes
    - If quorum not met or majority not achieved → Proposal fails
13. Passed proposals enter execution queue

**Expected Duration**: 2-3 minutes per vote

---

## 3. Feature Specifications

### 3.1 Video Cryptographic Validation

**Feature Description**: Every uploaded video receives a unique cryptographic hash stored on-chain, providing immutable proof of ownership and timestamp.

**Technical Implementation**:
- Hash Algorithm: SHA-256
- On-Chain Storage: Solana account data
- IPFS CID: Content Identifier for decentralized storage
- Metadata: JSON object with title, description, tags

**User Benefits**:
- Prove content authenticity
- Timestamp proof for copyright
- Prevent unauthorized modifications
- Enable content monetization

**Display Elements**:
- Verification badge on validated videos
- "Verified on blockchain" timestamp display
- Hash explorer link for transparency

---

### 3.2 Revenue Sharing Smart Contract

**Feature Description**: Automated distribution of creator revenue to backers based on investment proportions.

**Revenue Sources**:
1. Video monetization (ads, premium views)
2. Marketplace sales commissions
3. Tip/donation receipts
4. Subscription income

**Distribution Logic**:
```
Creator receives 70% of revenue
Backers receive 25% split proportionally
Platform fee: 5%

Example:
Total Revenue: 1000 VRE
Creator: 700 VRE
Backer Pool: 250 VRE
  - Backer A (invested 40%): 100 VRE
  - Backer B (invested 30%): 75 VRE
  - Backer C (invested 30%): 75 VRE
Platform: 50 VRE
```

**Execution Frequency**:
- Daily revenue calculation
- Weekly distribution transactions
- Automatic smart contract execution

---

### 3.3 Marketplace Escrow System

**Feature Description**: Trust-minimized transactions using smart contract escrow.

**Escrow States**:
1. **Initiated**: Funds locked, waiting for delivery
2. **Delivered**: Seller claims delivery, consumer verification pending
3. **Completed**: Consumer confirmed, funds released
4. **Disputed**: Either party initiated dispute
5. **Resolved**: Arbitration complete, funds distributed

**Escrow Parameters**:
- Lock Duration: 7 days default
- Dispute Window: 7 days from delivery
- Auto-Release: If no action after 7 days

---

### 3.4 Token-Holder Arbitration

**Feature Description**: Decentralized dispute resolution by randomly selected token holders.

**Arbitrator Selection**:
- Minimum holding: 1,000 VRE
- Quadratic weighting (prevents whale dominance)
- Random selection algorithm (verifiable randomness)
- Conflict of interest checks

**Voting Mechanism**:
- 5 arbitrators per case
- Majority decision (3+ votes)
- Anonymized voting to prevent collusion
- Evidence-based decisions

---

## 4. User Interface Requirements

### 4.1 Dashboard Design

**Creator Dashboard Must Include**:
- Total VRE earned (all-time, monthly, weekly)
- Active backers count
- Video portfolio grid
- Upload button (prominent placement)
- Analytics charts (views, engagement, revenue)
- Recent activity feed
- Backer leaderboard

**Backer Dashboard Must Include**:
- Investment portfolio (all creators backed)
- Total returns (realized and unrealized)
- Creator performance metrics
- Recommended creators
- Transaction history
- Investment analytics

**Consumer Dashboard Must Include**:
- Purchase history
- Marketplace recommendations
- Saved items / wishlist
- Active orders with tracking
- Review management
- VRE balance and wallet info

---

### 4.2 Responsive Design Requirements

- Mobile-first approach
- Breakpoints: 320px, 768px, 1024px, 1440px
- Touch-optimized controls for mobile
- Wallet integration compatible with mobile wallets
- Progressive Web App (PWA) capabilities

---

### 4.3 Accessibility Requirements

- WCAG 2.1 Level AA compliance
- Keyboard navigation for all functions
- Screen reader compatibility
- Color contrast ratios ≥ 4.5:1
- Alternative text for all images
- Captions for video content

---

## 5. Data Models

### 5.1 User Profile Schema

```json
{
  "wallet_address": "string (primary key)",
  "display_name": "string",
  "bio": "string",
  "profile_image_url": "string",
  "user_type": "enum [creator, backer, builder, consumer]",
  "created_at": "timestamp",
  "updated_at": "timestamp",
  "email": "string (optional, encrypted)",
  "social_links": {
    "twitter": "string",
    "discord": "string",
    "website": "string"
  },
  "verification_status": "boolean",
  "reputation_score": "integer"
}
```

### 5.2 Video Content Schema

```json
{
  "video_id": "uuid (primary key)",
  "creator_wallet": "string (foreign key)",
  "title": "string",
  "description": "string",
  "ipfs_cid": "string",
  "video_hash": "string (SHA-256)",
  "blockchain_tx": "string",
  "timestamp": "timestamp",
  "duration_seconds": "integer",
  "file_size_bytes": "integer",
  "thumbnail_url": "string",
  "tags": "array<string>",
  "visibility": "enum [public, unlisted, private]",
  "views_count": "integer",
  "likes_count": "integer"
}
```

### 5.3 Investment Record Schema

```json
{
  "investment_id": "uuid (primary key)",
  "backer_wallet": "string",
  "creator_wallet": "string",
  "amount_vre": "decimal",
  "investment_date": "timestamp",
  "blockchain_tx": "string",
  "revenue_share_percentage": "decimal",
  "total_returns_vre": "decimal",
  "status": "enum [active, withdrawn]"
}
```

---

## 6. Business Logic

### 6.1 Revenue Share Calculation

**Algorithm**:
```python
def calculate_backer_share(creator_revenue, backer_investment, total_investment):
    backer_pool = creator_revenue * 0.25  # 25% to backers
    backer_percentage = backer_investment / total_investment
    backer_earnings = backer_pool * backer_percentage
    return backer_earnings
```

### 6.2 Reputation Scoring

**Factors**:
- Successful transactions: +10 points each
- Positive reviews: +5 points each
- Disputes won: +20 points
- Disputes lost: -50 points
- Account age bonus: +1 point per month
- Arbitrator participation: +15 points per case

**Score Ranges**:
- 0-100: New User
- 101-500: Established
- 501-1000: Trusted
- 1001+: Elite

---

## 7. Edge Cases and Error Handling

### 7.1 Transaction Failures

**Scenario**: Blockchain transaction fails due to insufficient SOL for gas fees

**Handling**:
1. Detect failure immediately
2. Display clear error message: "Transaction failed: Insufficient SOL for network fees. Please add SOL to your wallet and try again."
3. Save form data locally (don't lose user input)
4. Provide "Retry" button
5. Offer link to acquire SOL (MoonPay integration)

---

### 7.2 Wallet Disconnection

**Scenario**: User's wallet disconnects mid-session

**Handling**:
1. Detect disconnection event
2. Save current application state
3. Display modal: "Wallet disconnected. Please reconnect to continue."
4. Disable actions requiring wallet signature
5. Provide "Reconnect Wallet" button
6. Restore session state upon reconnection

---

### 7.3 Escrow Timeout

**Scenario**: Seller never delivers product after consumer payment

**Handling**:
1. After 14 days of no seller activity
2. Automatically initiate dispute resolution
3. Notify both parties
4. Default outcome: Full refund to consumer
5. Mark seller account with warning
6. After 3 strikes, suspend seller privileges

---

### 7.4 Arbitrator Unavailability

**Scenario**: Selected arbitrators don't respond within voting period

**Handling**:
1. Send reminder notifications at day 3
2. If no response by day 5, select replacement arbitrators
3. Extend voting period by 3 days for new arbitrators
4. Penalize non-responsive arbitrators (reputation decrease)
5. Maximum 2 arbitrator replacements allowed
6. If still insufficient, escalate to platform admin review

---

## Appendix A: User Interface Mockups

*(Placeholder for UI/UX design mockups)*

- Landing Page
- Creator Dashboard
- Video Upload Flow
- Marketplace Browse
- Dispute Resolution Interface
- Governance Voting Screen

---

## Appendix B: API Endpoints

*(Summary of key API endpoints - detailed in separate API documentation)*

- `POST /api/auth/connect` - Wallet connection
- `POST /api/video/upload` - Video upload initiation
- `GET /api/creator/:wallet` - Fetch creator profile
- `POST /api/invest` - Create backer investment
- `GET /api/marketplace/listings` - Browse marketplace
- `POST /api/dispute/create` - Initiate dispute

---

**Document Control**

| Version | Date | Author | Changes |
|---------|------|--------|---------|
| 1.0 | Nov 2024 | VRE Team | Initial FSD release |


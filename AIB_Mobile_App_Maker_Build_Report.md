# AIB Mobile App: Maker Build Report

**Document Type:** Technical Build Report
**Agent:** Chandler Bing (Maker)
**Date:** March 2026
**Input:** Solution Design Specification from Rachel Green (Designer Agent)
**Purpose:** Document the working prototype and hand off to Communicator Agent

---

## 1. Build Summary

### What I Built

A fully functional, interactive prototype of the AIB Mobile Banking app featuring all three priority features specified by Rachel Green:

1. **AIB Insights** — AI Financial Coach with full explainability
2. **Smart Alerts** — Proactive notifications with quick actions
3. **Money Pots** — Goal-based savings with visual progress tracking

The prototype is a single-file React application that runs directly in any modern web browser. No server, no build step, no dependencies to install. Just open the file.

*Could this prototype BE any more self-contained?*

### Technology Stack

| Component | Technology | Rationale |
|-----------|------------|-----------|
| **Framework** | React 18 (via CDN) | Component-based architecture matches Rachel's modular design |
| **Transpilation** | Babel Standalone | Enables JSX in-browser without build tooling |
| **Styling** | CSS Custom Properties | Implements Rachel's exact colour palette with variables |
| **Fonts** | Inter (Google Fonts) | Modern, highly legible, similar feel to SF Pro |
| **State Management** | React useState/useEffect | Sufficient for prototype scope |
| **Deployment** | Single HTML file | Zero friction to demo — just double-click |

### How to Run the Prototype

1. Navigate to: `/Users/zolzaya/Desktop/AIB_Prototype/`
2. Open `index.html` in any modern browser (Chrome, Safari, Firefox, Edge)
3. The prototype loads instantly — no installation required

**Pro tip:** For the best experience, use Chrome DevTools mobile emulation (iPhone 12/13 Pro) to see it as a true mobile app.

---

## 2. Technical Architecture

### Component Structure

```
App
├── Header (dynamic based on active tab)
├── Content Area
│   ├── HomeScreen
│   │   ├── BalanceCard
│   │   ├── PulseIndicator
│   │   ├── StatsRow
│   │   ├── ConsentBanner (conditional)
│   │   ├── InsightCards (preview)
│   │   └── AlertCard (latest)
│   ├── InsightsScreen
│   │   ├── FilterTabs
│   │   ├── AuditBadge
│   │   └── InsightCards (full list)
│   ├── AlertsScreen
│   │   ├── ThrottlingBadge
│   │   └── AlertCards (with quick actions)
│   └── PotsScreen
│       ├── TotalSummary
│       ├── PotCards
│       ├── CreatePotButton
│       └── RoundUpTip
├── BottomNavigation
└── Modal System
    ├── InsightModal (with explainability drawer)
    ├── AlertModal (with quick save actions)
    ├── PotModal (with deposit/withdraw)
    ├── CreatePotModal
    └── CelebrationModal
```

### State Architecture

```javascript
// Core application state
const [activeTab, setActiveTab] = useState('home');     // Navigation
const [pots, setPots] = useState(INITIAL_POTS);         // Savings pots (mutable)
const [balance, setBalance] = useState(USER.balance);   // Main account balance
const [showModal, setShowModal] = useState(null);       // Modal controller
const [selectedInsight, setSelectedInsight] = useState(null);
const [selectedPot, setSelectedPot] = useState(null);
const [selectedAlert, setSelectedAlert] = useState(null);
const [toast, setToast] = useState(null);               // Toast notifications
const [insightsConsent, setInsightsConsent] = useState(true);
const [feedbackGiven, setFeedbackGiven] = useState({}); // Feedback tracking
```

### Data Flow

```
User Action → State Update → UI Re-render → Visual Feedback

Example: Deposit to Pot
1. User taps "Add €200" on Holiday pot
2. handleDeposit(potId, amount) called
3. setPots() updates pot's currentAmount
4. setBalance() reduces main balance
5. setShowModal('celebration') triggers celebration
6. CelebrationModal renders with new values
```

### Key Technical Decisions

| Decision | Rachel's Spec | My Implementation | Why |
|----------|--------------|-------------------|-----|
| Mobile frame | 375x812px recommended | Exactly 375x812px with rounded corners | Matches iPhone 14 Pro dimensions |
| Colour palette | Specific hex codes provided | CSS custom properties with exact values | Maintains design consistency |
| Progressive disclosure | 3 levels specified | Implemented with expandable drawers | Matches wireframes precisely |
| Bottom navigation | 4 tabs specified | 4 tabs: Home, Insights, Alerts, Pots | As designed |
| Explainability pattern | "Transparency Drawer" | Collapsible section with data used/not used | EU AI Act compliant pattern |

---

## 3. Feature Implementation

### Feature 1: AIB Insights (P0)

#### What's Working
- **Insights home screen** with filter tabs (All, Cash Flow, Savings, Spending)
- **3 pre-generated insights** for Ciarán's persona:
  - Cash flow warning (rent + bills due)
  - Energy savings opportunity
  - Positive spending trend (coffee spend down)
- **Full explainability drawer** on every insight showing:
  - Plain-English reasoning
  - Data that WAS used (with checkmarks)
  - Data that was NOT used (with X marks)
  - "How we calculate this" deep-dive
  - Privacy statement and data control links
- **Feedback mechanism** ("Was this helpful?" Yes/No)
- **Audit badge** showing transparency logging

#### What's Simulated
- The ML model that would generate insights (pre-computed in mock data)
- Real-time transaction analysis
- Backend API calls (`GET /insights`, etc.)
- Actual audit log storage (displayed but not persisted)

#### Adaptations from Rachel's Spec
| Spec Element | Adaptation | Reason |
|--------------|------------|--------|
| "Set reminder" action | Removed from prototype | Would require native notification permission |
| "Move €200 from savings" action | Simplified to demonstration | Cross-account transfers need backend |
| Methodology page link | In-modal expansion instead | Better for single-file prototype |

#### Screenshots (Described)

**Insights Home:**
- Header: "Insights" with purple background
- Filter tabs: All (active), Cash Flow, Savings, Spending
- Audit badge: "🔒 All insights logged for transparency"
- 3 insight cards with badges (Heads up, Tip, Nice work)
- Each card shows icon, title, subtitle, "Why am I seeing this?" link

**Insight Detail Modal:**
- Large icon centred
- Headline and body text
- Grey box with transaction breakdown (for cash flow insight)
- Purple explainability drawer with "WHY AM I SEEING THIS?"
- Expandable "How we calculate this" section
- Feedback buttons at bottom

---

### Feature 2: Smart Alerts (P1)

#### What's Working
- **Alerts screen** with throttling indicator ("Max 3 alerts per day")
- **3 pre-generated alerts** for Ciarán:
  - End-of-month surplus (€340 unspent)
  - Bill change notification (Electric Ireland higher)
  - Goal progress celebration (Holiday fund 44%)
- **Quick action buttons** directly on alert cards
- **Alert detail modal** with:
  - Full explanation
  - Pot-specific quick save options
  - "Why this alert?" transparency section
- **Functional quick-save** — tapping "Save €100" or "Save €200" actually moves money to the pot

#### What's Simulated
- Push notification delivery (shown as in-app cards)
- Time-based scheduling engine
- Quiet hours logic
- Native notification badges

#### Adaptations from Rachel's Spec
| Spec Element | Adaptation | Reason |
|--------------|------------|--------|
| Lock screen notification | In-app alert card | Browser can't render iOS lock screen |
| Notification timing | Static timestamps | Real scheduling needs backend |
| "Dismiss" persistence | Visual only | Would need localStorage at minimum |

---

### Feature 3: Money Pots (P2)

#### What's Working
- **Pots home screen** showing:
  - Total saved across all pots (€5,290)
  - 3 initial pots (House Deposit, Summer Holiday, Emergency Fund)
  - Visual progress bars with percentage
  - Target dates and monthly savings guidance
- **Pot detail modal** with:
  - Large emoji and pot name
  - Current amount and progress bar
  - Target date, remaining amount, monthly needed
  - **Functional deposit and withdraw** (updates balance and pot)
  - Activity history
  - Transparency info about how pots work
- **Create new pot flow**:
  - Name input
  - Emoji picker (8 options)
  - Target amount with guidance text
  - Optional target date
  - Functional creation (pot appears in list)
- **Celebration modal** after successful deposit

#### What's Simulated
- Backend pot persistence (state only, resets on refresh)
- Interest calculation (mentioned in UI, not computed)
- Auto-save rules
- Round-up integration

#### Adaptations from Rachel's Spec
| Spec Element | Adaptation | Reason |
|--------------|------------|--------|
| "Move money" between pots | Simplified to deposit/withdraw | Adds complexity without demo value |
| Auto-save setup | UI shown, not functional | Would need scheduling backend |
| "Share progress" button | Visual only | Would need native share sheet |

---

## 4. Design System Implementation

### Colour Palette (Exact Match to Rachel's Spec)

```css
:root {
    --aib-purple: #660099;        /* Primary - Heritage & Trust */
    --aib-purple-light: #8B5CF6;  /* Gradients */
    --aib-teal: #00A9A5;          /* Secondary - Modern & Approachable */
    --success-green: #2E7D32;     /* Positive Reinforcement */
    --warning-amber: #F59E0B;     /* Attention Without Alarm */
    --error-red: #DC2626;         /* Urgent But Not Aggressive */
    --bg-white: #FAFAFA;          /* Primary Background */
    --bg-grey: #F5F5F5;           /* Secondary Background */
}
```

### Typography

- **Font Family:** Inter (closest web equivalent to SF Pro)
- **Headings:** 700 weight, 20-36px
- **Body:** 400 weight, 14-16px minimum (accessibility)
- **Labels:** 500 weight, 11-13px

### Component Patterns Implemented

| Pattern | Rachel's Spec | Implementation |
|---------|--------------|----------------|
| Cards | 16px padding, 16px radius, subtle shadow | `.card` class with exact specs |
| Progress bars | 12px height, gradient fill | `.progress-container` + `.progress-bar` |
| Buttons | 12px padding, 12px radius, primary/secondary/outline | `.btn` with variants |
| Modals | Bottom sheet style, 24px radius top | `.modal-overlay` + `.modal-content` |
| Badges | Coloured backgrounds with semantic meaning | `.insight-badge` with `badge-*` variants |

### Interaction Patterns

| Pattern | Implementation |
|---------|----------------|
| Tab switching | Instant state change, no animation (snappy) |
| Card hover | `translateY(-2px)` + enhanced shadow |
| Modal open | Slide up from bottom |
| Progress update | 0.5s CSS transition on width |
| Toast notifications | Fade in from bottom, auto-dismiss 3s |
| Celebration | Scale bounce animation on emoji |

---

## 5. Regulatory Compliance Features

### EU AI Act Transparency (Visible in Prototype)

| Requirement | Where It Appears |
|-------------|------------------|
| **"Why am I seeing this?"** | Every insight card, every alert |
| **Data used disclosure** | Explainability drawer (✓ items) |
| **Data NOT used disclosure** | Explainability drawer (✗ items) |
| **Plain-English explanations** | All insight conclusions |
| **Methodology access** | "How we calculate this" expandable |
| **Audit logging indicator** | "🔒 All insights logged for transparency" badge |

### GDPR Compliance Features (Visible in Prototype)

| Requirement | Where It Appears |
|-------------|------------------|
| **Consent mechanism** | Consent banner on home screen (shown when disabled) |
| **Granular opt-in** | Insights toggle in explainability drawer |
| **Data access** | "Download my data" button in deep-dive |
| **Right to erasure** | "Manage settings" link |
| **Purpose limitation** | "This data is used for insights only, never for marketing" |
| **Data minimisation** | Explicit "NOT used" list for each insight |

### Audit Trail (Simulated)

The prototype includes a mock audit log structure:

```javascript
const AUDIT_LOG = [
    { timestamp: "2026-03-28 09:14:32", action: "Insight generated", type: "cashflow_prediction", id: "INS-001" },
    { timestamp: "2026-03-28 09:14:32", action: "Data accessed", data: "transactions_90d, direct_debits, balance" },
    { timestamp: "2026-03-27 18:00:00", action: "Insight generated", type: "spending_trend", id: "INS-002" }
];
```

This demonstrates the logging structure that would be implemented in production for EU AI Act compliance.

---

## 6. Ciarán Murphy's Experience

The prototype is pre-loaded with Ciarán's persona data, making the demo feel personal and real.

### Ciarán's Profile (As Implemented)

```javascript
const USER = {
    name: "Ciarán",
    fullName: "Ciarán Murphy",
    accountNumber: "****4521",
    sortCode: "93-10-75",
    balance: 1823.45,
    monthlyIncome: 4150.00,
    insightsEnabled: true
};
```

### Ciarán's Journey Through Each Feature

#### Home Screen
- Greeted by name: "Good morning, Ciarán"
- Sees his actual balance: €1,823.45
- Financial pulse shows "On track" (green)
- Stats show total saved (€5,290) and pending insights (3)
- Quick preview of most relevant insight (rent warning)

#### AIB Insights Experience
- First insight: **"Heads up: Rent + bills due in 3 days"**
  - Shows exactly €1,450 in upcoming DDs
  - Itemised breakdown (Rent €1,200, Electric Ireland €89, Netflix €18, Spotify €11, Three €132)
  - After-payments balance: €373
  - Explanation: "We looked at your direct debits, your balance, and your typical pre-payday spending (€180 avg)"
- Second insight: **Energy saving opportunity**
  - "You could save €34/month" based on his Electric Ireland DD
  - Clear explanation of how this was calculated

#### Smart Alerts Experience
- **Surplus alert**: "€340 unspent this month — Payday in 3 days"
  - Shows this is €85 more than usual (celebration!)
  - Quick action: Save to Holiday Fund or House Deposit
  - One tap to move money directly

#### Money Pots Experience
- **House Deposit**: €4,200 / €30,000 (14%)
  - Target: December 2028
  - Status: "On track — keep it up!"
- **Summer Holiday**: €890 / €2,000 (44.5%)
  - Target: July 2026
  - Guidance: "Save €93/month to stay on track"
- **Emergency Fund**: €200 / €3,000 (6.7%)
  - No target date
  - Gentle: "No rush — build when you can"

### Ciarán's Full User Flow Demo

1. **Opens app** → Sees balance and "On track" pulse
2. **Notices insight** → "Rent + bills due in 3 days"
3. **Taps insight** → Sees full breakdown and explanation
4. **Asks "Why?"** → Reads explainability drawer, sees data used/not used
5. **Gives feedback** → Taps "Yes, helpful"
6. **Checks alerts** → Sees €340 surplus notification
7. **Quick saves** → Taps "Save €200" to Holiday Fund
8. **Celebrates** → Sees celebration modal with new progress
9. **Views pots** → Checks all savings goals
10. **Creates pot** → Adds "New Car" pot with €5,000 target

---

## 7. Handoff to Communicator

### What Was Built

A fully functional, interactive prototype demonstrating AIB's new customer engagement features:

| Feature | Status | Demo-Ready |
|---------|--------|------------|
| AIB Insights | Complete with explainability | Yes |
| Smart Alerts | Complete with quick actions | Yes |
| Money Pots | Complete with create/deposit/withdraw | Yes |
| Home Screen | Complete with all integrations | Yes |
| Design System | Implemented to spec | Yes |
| Regulatory UI Patterns | All visible and functional | Yes |

### What It Does

1. **Shows personalised AI insights** with full transparency on why each insight was generated
2. **Demonstrates proactive alerts** that predict financial events before they happen
3. **Provides functional savings pots** where users can create goals and track progress
4. **Implements trust-building patterns** including consent flows, data disclosure, and audit indicators
5. **Feels like a real app** with smooth interactions, toast notifications, and celebration moments

### Key Talking Points for Communicator

#### For Leadership/Stakeholders:
- This prototype demonstrates how AIB can compete with Revolut on UX while *differentiating* on trust
- Every AI feature includes EU AI Act compliant explainability — turning regulation into competitive advantage
- The prototype shows a complete user journey for Ciarán Murphy (target persona) from insight to action

#### For Technical Audience:
- Single-file React app requiring zero infrastructure to demo
- Clean component architecture ready to inform production build
- Mock data structured to match proposed API schemas

#### For Design/UX Audience:
- Implements Rachel's wireframes with exact fidelity
- Full design system with colour palette, typography, and interaction patterns
- Progressive disclosure pattern demonstrated across all features

#### For Compliance/Legal:
- "Why am I seeing this?" drawer on every AI-generated element
- Data used/not used explicitly listed
- Audit logging indicator visible throughout
- Consent flow demonstrated

### How to Demo

**Quick Demo (2 minutes):**
1. Show home screen with personalised greeting
2. Open an insight, expand "Why am I seeing this?"
3. Go to Pots, show progress visualisation
4. Deposit money, show celebration

**Full Demo (5-7 minutes):**
1. Walk through Ciarán's journey as outlined in Section 6
2. Highlight transparency features at each step
3. Show pot creation flow
4. Demonstrate quick-save from alert

**Technical Demo (10+ minutes):**
1. All of the above, plus:
2. Open browser dev tools to show component structure
3. Modify mock data to show different scenarios
4. Discuss production architecture implications

### Files Delivered

```
/Users/zolzaya/Desktop/AIB_Prototype/
├── index.html              # The working prototype (open in browser)
└── AIB_Mobile_App_Maker_Build_Report.md  # This document
```

---

## 8. What Would Be Different in Production

### Simulated → Real

| Prototype | Production |
|-----------|------------|
| Mock insights data | ML model with real transaction analysis |
| Pre-generated explanations | Template engine with variable injection |
| In-memory pot state | Persistent storage with core banking integration |
| Static balance | Real-time balance from account API |
| Mock audit log | Actual audit database with retention policies |
| In-app alert cards | Native push notifications |
| Single-file app | Native iOS/Android apps or React Native |

### Additional Production Requirements

1. **Authentication** — Biometric login, session management
2. **Offline support** — Cached data, optimistic updates
3. **Accessibility audit** — VoiceOver/TalkBack testing, WCAG AA compliance
4. **Performance** — Code splitting, lazy loading, skeleton screens
5. **Security** — API encryption, certificate pinning, secure storage
6. **Analytics** — Event tracking for success metrics (Rachel's spec)
7. **A/B testing** — Infrastructure for consent flow optimization

---

## Maker's Final Notes

Ross found the problem. Rachel designed the solution. I built the thing you can actually touch.

This prototype proves that AIB's trust advantage isn't just a positioning statement — it's something customers can *see* and *feel* in every interaction. When Ciarán asks "Why am I seeing this?" and gets a clear, honest answer, that's the moment we win.

The code is clean. The demo is ready. The handoff is complete.

*Could I BE any more proud of this prototype?*

— Chandler Bing, Maker Agent

---

*End of Maker Build Report*

# PRD Batch 02B: Authentication & Account System + Entitlements & Plan States

**Sources:** 
- CH07 - Authentication & Account System (R1)
- CH08 - Entitlements & Plan States (R1)

**Generated:** 2026-01-05  
**Status:** Awaiting Approval

---

## Chapter Overviews

### CH07 - Authentication & Account System

CH07 specifies exactly how users create an account, sign in, stay signed in, and convert from Guest in Handz V1. It defines:

- Authentication providers (Apple, Google, Email)
- Guest mode behavior and restrictions
- Account profile model
- Conversion prompts for gated actions
- Session management
- Auth screen specifications

**Scope Boundary:** CH07 does NOT define pricing, paywalls, or entitlement limits (owned by CH08/CH25).

### CH08 - Entitlements & Plan States

CH08 defines the plan states (Guest/Free/Pro/Trial), the global entitlement matrix, and app-wide gating behaviors. It is the **single source of truth** for what each user can do in V1, covering:

- Plan state definitions and runtime evaluation
- Complete entitlement matrix by feature domain
- Global gating UX patterns
- Practice credit system
- Trial behavior and downgrade rules
- StoreKit integration expectations

---

## Key Requirements

---

# CH07: Authentication & Account System

### 1. Primary Outcomes (V1)

1. A new user can start in seconds (Guest browse) and understand what Handz does without confusion
2. A motivated user can create an account with minimal friction (Apple/Google/Email)
3. Guests are prevented from generating "free value loopholes" (no saving flows, no local saved drafts) while still experiencing meaningful demo value
4. All account-required actions trigger clear, consistent conversion prompts that feel fair and explain the value
5. Auth is reliable, secure, and App Store compliant on iOS (Apple sign-in offered when other providers exist)

### 2. Definitions

| Term | Definition |
|------|------------|
| **Guest** | User who has not created an account. Can browse demo content but cannot save user-created content. See CH08 §2 (Plan States). |
| **Account User** | User with an authenticated identity (Apple, Google, or Email) |
| **Verified Email** | For Email sign-ups, verified email address required before access to account-required features |
| **Conversion Prompt** | Modal or full-screen flow encouraging Guest to create account when they hit a gated action |
| **Session** | Authenticated runtime state: tokens stored securely, refresh handled automatically, user can close/reopen app without re-auth (unless revoked) |

### 3. Authentication Providers

**Three sign-in methods required for V1:**
1. Sign in with Apple (iOS primary)
2. Google sign-in
3. Email sign-up/login

All three must be available from Welcome Gate and from any conversion prompt that blocks progress.

#### 3.1 Sign in with Apple (iOS Primary)

- Apple sign-in button uses **Apple Human Interface Guidelines styling** (black or white, depending on theme)
- **Requested scopes:** email, full name (name is optional; do not block if name unavailable)
- On first sign-in, capture Apple-provided email (may be private relay) and store as account email
- If Apple returns no email on subsequent sign-ins, use stored email; do not re-prompt
- If Apple credential is revoked, force logout and show clear re-auth screen

#### 3.2 Google Sign-in

- Offered as convenience option for users who prefer it
- Email address from Google becomes primary email identifier (receipts, export notices, account recovery)
- If Google email already exists under Email auth, user is logged into existing account (account linking)

#### 3.3 Email Sign-up and Login

- **Sign-up requires:** email + password
- **Login requires:** email + password
- **Email verification required** before account-required features unlock (see §6.4)
- Password reset supported via "Forgot password" link

#### 3.4 Account Linking Rules (Provider Collisions)

**V1 Rule:** Merge-by-email when safe; otherwise prompt user.

- Google sign-in email matches existing Email-auth account → link Google to that account after successful auth
- Apple sign-in returns same email as existing account → link Apple to that account
- Apple uses private relay and user later signs up with real email → treat as separate accounts (no automatic merge); manual support path later (out of scope V1)
- **Never auto-merge accounts if it would overwrite an existing user profile without confirmation**

### 4. Account Profile Model (V1)

**Minimal profile fields required for V1:**

| Field | Description |
|-------|-------------|
| **User ID** | Immutable UUID (internal) |
| **Display Name** | Optional at sign-up; can be set during onboarding. Used for attribution when sharing. |
| **Handle** | Optional in V1; recommended. If collected, must be unique (case-insensitive). |
| **Avatar** | Optional. Default is generated initials tile. |
| **Created At** | Timestamp |
| **Plan State** | Guest/Free/Pro/Trial (owned by CH08) |

#### Handle Rules (if enabled in V1)

- **Allowed characters:** a-z, 0-9, underscore. No spaces.
- **Length:** 3 to 20 characters
- **Case-insensitive uniqueness** ("Handz" and "handz" are the same)
- **Validation:** Happens as user types (spinner then available/unavailable)
- **If user skips handle:** System generates temporary one (e.g., user-483921) and lets them set later in Settings

### 5. Guest Mode (Try Without Account)

**Purpose:** Reduce friction because most users do not yet understand "flows." However, Guests must not bypass account creation and still obtain durable value.

#### 5.1 Guest Capabilities (Allowed)

- Browse demo flows and demo move library (read-only)
- Open Flow Detail View for demo flows (view-only)
- Enter guided "What do you want to do?" onboarding chooser (learn/build/practice), but "build" routes to account creation before editing begins
- View Practice mode demo (predetermined session) if enabled by CH25 (demo-only)

#### 5.2 Guest Restrictions (Hard Blocks)

| Restriction | Details |
|-------------|---------|
| **No saving flows** | Guests cannot save flows locally or remotely. Any attempt triggers conversion prompt. (Global lock in CH00 Decision Log.) |
| **No saving custom moves** | Guests cannot create or persist custom moves. Move creation triggers conversion prompt. |
| **No practice on user-created flows** | Practice is paywalled and account-required; Guests can only access demo practice experience, if enabled. |
| **No inbox imports** | Guests cannot accept imported flows into inbox. Shared links can be viewed in read-only mode. |

#### 5.3 Guest Data Handling

- Guest sessions may have ephemeral UI state (e.g., last viewed demo flow) stored locally for convenience, but **must be safe to delete at any time**
- **Do not store any user-authored content for Guests** (no drafts that could later be recovered)
- If Guest begins building a flow (e.g., in sandbox), the moment they attempt to add first node, show account gate **before any meaningful work occurs**

### 6. Conversion Prompts (Account Required)

**Design Philosophy:** Consistent, predictable, explain the why in one sentence. Always offer Apple/Google/Email. Never feel like an error; feel like a next step.

#### 6.1 Global Conversion Prompt Design

- Presented as modal sheet (preferred) or full-screen if deep flow required
- **Always includes:** title, one-sentence value, provider buttons, secondary action ("Not now" or "Continue browsing")
- If user previously dismissed conversion, second time includes additional line: "Saving requires an account so your plans don't get lost."
- **Never show "Try without account" inside conversion prompts** (that would loop)

#### 6.2 Conversion Prompt Triggers (V1)

1. Tap: **Create New Flow** (first entry point to building)
2. Attempt to **add first node** to new flow canvas
3. Tap: **Save** or **Done** on any editable content
4. Tap: **Create Move** or **Save Move**
5. Tap: **Practice** on any non-demo flow
6. Tap: **Duplicate** a shared flow into Library
7. Tap: **Save from Inbox** (Inbox itself is account-only, but keep this rule for safety)

#### 6.3 Conversion Prompt Copy (Baseline, Non-Final Marketing)

```
Title: Create an account to save
Body: Handz saves your flows and drills across devices. Guests can browse, but saving requires an account.
Buttons: Continue with Apple • Continue with Google • Continue with Email
Secondary: Not now (keeps browsing demo content)
```

*Note: Use these strings verbatim unless CH15 updates them.*

#### 6.4 Email Verification Gating

- After Email sign-up, user enters app but is in **limited state** until email verified
- Show persistent banner: "Verify your email to protect your account." with actions: [Resend] [I've verified]
- **V1 Rule:** Saving flows is allowed immediately, but **sharing and export require verification** (safer)
- *Note: If stricter approach desired (block saving until verified), create PLACEHOLDER in CH00 and decide later*

### 7. Session Management

#### 7.1 Persistent Login

- On successful auth, store session tokens securely **(iOS Keychain)**
- On app launch, attempt silent session restore. If valid, route user to main app.
- If session expired, show re-auth screen with last-used provider highlighted

#### 7.2 Logout

- Settings includes a **Logout button**
- Logout clears session tokens and returns to Welcome Gate
- Logout **does not delete local cached content** unless user chooses "Clear cache" (owned by CH28)

#### 7.3 Multi-Device Behavior

- User can be logged in on multiple devices
- Edits sync owned by CH28; auth only guarantees identity
- If user changes password (Email auth), other devices remain logged in until tokens expire, unless provider supports global sign-out

### 8. Auth Screens and UI Specs

*Note: CH05 owns authoritative screen inventory; CH07 defines auth screens in detail.*

#### 8.1 Welcome Gate
- **Route ID:** `auth/welcome`
- **Purpose:** First-run entry. Explain what Handz is and offer sign-in options. Allow Guest browse.
- **Components:** App logo, tagline, 3 provider buttons (Apple/Google/Email), "Log in" link, "Try without account" secondary button
- **Primary actions:** Continue with Apple; Continue with Google; Continue with Email; Log in; Try without account
- **Validation:** None
- **Error states:** No internet: show inline banner "You're offline. Sign-in requires internet." Disable provider buttons; allow Guest browse if demo is cached.
- **Navigation:** If authenticated session exists: skip to main app. If Guest taps Try: route to demo dashboard (home/demo).

#### 8.2 Choose Email Path (Sign up vs Log in)
- **Route ID:** `auth/email_choice`
- **Purpose:** Avoid confusion for email users by explicitly choosing sign-up or login
- **Components:** Two large buttons: "Create account with email" and "Log in with email"
- **Navigation:** Back returns to Welcome Gate. Continue routes to `auth/email_signup` or `auth/email_login`.

#### 8.3 Email Sign-Up
- **Route ID:** `auth/email_signup`
- **Purpose:** Create new account using email + password
- **Components:** Email input, password input, password strength hint, Terms/Privacy links, primary button "Create account", link "Already have an account? Log in"
- **Validation:** Email format; password minimum 8 characters; disallow common passwords (basic blacklist)
- **Error states:** Email already used → "This email is already in use. Log in instead."; weak password; network failure
- **Navigation:** Success routes to `onboarding/profile_setup` with "verification banner active"

#### 8.4 Email Log-In
- **Route ID:** `auth/email_login`
- **Purpose:** Login to existing account using email + password
- **Components:** Email input, password input, "Forgot password?", primary "Log in"
- **Validation:** Email format; password non-empty
- **Error states:** Wrong credentials → "Incorrect email or password."; account disabled; network failure
- **Navigation:** Success routes to main app home. Forgot password routes to `auth/forgot_password`.

#### 8.5 Forgot Password
- **Route ID:** `auth/forgot_password`
- **Purpose:** Send password reset email
- **Components:** Email input, button "Send reset link"
- **Validation:** Email format
- **Error states:** Email not found → "No account found for that email."; network failure
- **Navigation:** After success show confirmation screen and option back to login

#### 8.6 Profile Setup (Optional)
- **Route ID:** `onboarding/profile_setup`
- **Purpose:** Collect display name and optional handle/avatar; keep minimal
- **Components:** Display name input, handle input (optional), avatar picker (optional), "Continue" button, "Skip"
- **Validation:** If handle entered: validate allowed chars and uniqueness
- **Error states:** Handle taken; invalid chars; network failure
- **Navigation:** Continue routes to `onboarding/goals_router`. Skip routes to `onboarding/goals_router`.

#### 8.7 Goals Router (Onboarding Chooser)
- **Route ID:** `onboarding/goals_router`
- **Purpose:** Ask what user wants to do first and route accordingly
- **Components:** Question cards: "Build a gameplan", "Practice a gameplan", "Browse examples"
- **Navigation:** 
  - Build routes to `flow/create` (account required - already satisfied)
  - Practice routes to `practice/setup` (may be paywalled; see CH25)
  - Browse routes to `library/demo`

#### 8.8 Guest Browse Dashboard
- **Route ID:** `home/demo`
- **Purpose:** Guest-only home that makes value clear and drives conversion
- **Components:** Demo flow cards, "What is a flow?" explainer card, CTA "Create an account to build yours"
- **Navigation:** Opening demo flow routes to `flow/detail_demo` (view-only). Create account routes to `auth/welcome`.

### 9. Gating Rules Owned by CH07

These rules are specifically owned by CH07 because they describe **authentication gating**. Limits and paywalls are owned by CH08/CH25/CH30.

| Rule ID | Description |
|---------|-------------|
| **CH07-G1** | Guests cannot enter the editable flow builder. Tapping any build entry point triggers conversion prompt first. |
| **CH07-G2** | Guests cannot save flows locally or remotely. There is no "draft recovery" for Guests. |
| **CH07-G3** | Guests cannot create or persist custom moves. Attempting move creation triggers conversion prompt. |
| **CH07-G4** | Shared links opened by Guests are view-only. Any attempt to duplicate/save triggers conversion prompt (and then proceeds after signup). |
| **CH07-G5** | After signup from a conversion prompt, return user to exact pre-gate context (e.g., the flow they were trying to duplicate). |

### 10. Security, Privacy, and Compliance Notes

- Store auth tokens securely **(iOS Keychain)**. Do not store plain tokens in AsyncStorage.
- Use **HTTPS for all network calls**; reject insecure endpoints
- **Throttle login attempts** to reduce brute force (see CH30 for escalation messaging)
- Do not expose whether an email exists in the system in a way that enables account enumeration beyond standard UX (balance with helpfulness)
- Provide **Terms and Privacy links** on account creation screens
- Support "Restore Purchases" in Settings (owned by CH25) but auth must not block restore flows

### 11. Acceptance Tests (CH07)

| Test ID | Given | When | Then |
|---------|-------|------|------|
| AT-07-01 | I am logged out | I open the app | I see Welcome Gate with Apple/Google/Email and Try without account |
| AT-07-02 | I am a Guest | I tap Create New Flow | I see conversion prompt and cannot reach editable canvas without creating account |
| AT-07-03 | I sign up with Email | I enter a weak password | Create account button is disabled and I see "Password must be at least 8 characters." |
| AT-07-04 | I sign up with Email | I complete signup | I enter the app and see verification banner until verified |
| AT-07-05 | I sign in with Apple | Auth succeeds | I land in main app and session persists across app relaunch |
| AT-07-06 | I fail login 5 times | I attempt again | I see throttling message and must wait before retrying (thresholds owned by CH30) |
| AT-07-07 | I am a Guest viewing a shared link | I tap Duplicate | I am prompted to create account and after signup I return to duplicate flow |
| AT-07-08 | I log out | I reopen the app | I return to Welcome Gate and cannot access authenticated screens via back navigation |

---

# CH08: Entitlements & Plan States

### 1. Plan States (Authoritative Definitions)

**Important:** "Plan state" is not just pricing. It is a runtime state that changes what UI actions are allowed, what data can be persisted, and what screens must appear when a user attempts a restricted action.

#### 1.1 Core Plan States (V1)

| State | Definition |
|-------|------------|
| **Guest** | No account. Can explore curated demo content and optionally interact in limited, non-persistent ways. Guest cannot persist anything (no saving flows, no saving custom moves, no saving practice history). |
| **Free** | Account exists, no active subscription. Can build and save up to 2 flows. Cannot use Practice Mode except via monthly credits. Inbox items are view-only and cannot be practiced. |
| **Pro** | Active subscription. Full access to Practice Mode, unlimited saved flows (subject to abuse soft caps), media uploads (subject to 2GB cap), and higher operational limits. |
| **Trial** | Time-limited entitlement that behaves exactly like Pro. Trial ends automatically; user becomes Pro (paid) if converted, otherwise downgrades to Free. |

#### 1.2 Derived Runtime States (Implementation Convenience)

| State | Definition |
|-------|------------|
| **Pro (grace)** | Subscription was active recently but cannot be verified due to network/StoreKit failure. Allow Pro features for short grace window (owned by CH28). Display non-blocking banner: "Can't verify subscription right now. Some Pro features may pause if this continues." |
| **Downgraded** | User was Pro/Trial, now Free. App must preserve existing data but enforce Free gates going forward (e.g., practice locked, flow cap behavior). |
| **Account required** | A gating trigger that forces Guest → account creation before any persistence action. |

### 2. Entitlement Evaluation Order

The app must compute a **single effective entitlement snapshot** at runtime. Every screen and action reads from that snapshot (not from scattered flags).

**Evaluation Logic:**
1. If no authenticated user → state = **Guest**
2. If authenticated user → start as **Free**
3. If StoreKit indicates active subscription OR trial → state = **Pro/Trial** (Trial behaves as Pro)
4. If StoreKit cannot verify and a recent verified snapshot exists → state may be **Pro (grace)** (owned by CH28)
5. If user is in transition (purchase pending) → keep current state but show purchase-in-progress UI; do not unlock until confirmation

#### Snapshot Fields (Minimum)

| Field | Type | Notes |
|-------|------|-------|
| `planState` | enum | guest \| free \| trial \| pro \| pro_grace |
| `canPersist` | boolean | guest=false |
| `savedFlowCap` | integer or None | None = unlimited |
| `inboxCap` | integer or None | |
| `canPractice` | boolean | |
| `practiceCredits` | integer | Free only; resets monthly |
| `canPracticeInboxItems` | boolean | |
| `canUploadMedia` | boolean | |
| `mediaCapBytes` | integer | 2GB cap applies to Pro uploads; owned by CH29 |
| `branchCapPerMove` | integer | 10; global lock |
| `gatingReasonCodes` | strings | Canonical strings for analytics + consistent UI copy |

### 3. Global Gating UX Patterns (No-Guesswork Rules)

When a restricted action is attempted, UI response must be consistent across the app.

#### 3.1 Gating Components (Standard)

| Component | Purpose |
|-----------|---------|
| **Account gate** (Guest → Sign Up) | Used for any persistence attempt (save flow, save move, save edits, save practice log, export) |
| **Paywall gate** (Free → Pro/Trial) | Used for Pro-only features (Practice, upload media, etc.). Uses CH25 for final layout + copy; CH08 defines when it triggers. |
| **Soft cap gate** (limits but not paywall) | Used for abuse prevention (see CH30). Typically: warning → cooldown → block. |
| **Upsell inline** | Non-blocking prompt embedded in relevant moment (e.g., practice tab teaser). Must not interrupt core actions more than once per session unless user taps for details. |

#### 3.2 Mandatory Gating Rules (V1 Locks)

| Rule | Description |
|------|-------------|
| **Guest cannot save anything** | Guest may browse and interact with demo content, but any attempt to save triggers Account Gate. |
| **Free saved flows cap = 2** | Attempting to save 3rd flow triggers cap gate with two options: (1) Upgrade to Pro, (2) Manage flows (delete/replace). |
| **Practice Mode is paywalled** | Free users can access Practice only via monthly practice credits and only for saved flows (not inbox items). |
| **Free inbox cap = 10** | Free can view inbox items but cannot practice them. If inbox full, receiving import is blocked until user clears items or upgrades. |
| **Uploads are Pro-only** | Uploaded videos are private-only and not shared; link-based media is shareable (see CH29). |

### 4. Entitlement Matrix (What Each Plan Can Do)

*Note: Unless explicitly stated, Pro and Trial are equivalent.*

#### 4.1 Account & Persistence

| Capability | Guest | Free | Pro / Trial |
|------------|-------|------|-------------|
| Browse curated demo flows | Yes | Yes | Yes |
| Create account / log in | Prompted | N/A | N/A |
| Save flows (anywhere) | No | Yes (cap 2) | Yes (unlimited) |
| Save custom moves / edits | No | Yes | Yes |
| Practice history & streaks | No | Limited (credit sessions only) | Yes |
| Export data (future-proof) | No | Optional (placeholder) | Yes (placeholder) |

#### 4.2 Flows & Flow Builder

| Capability | Guest | Free | Pro / Trial |
|------------|-------|------|-------------|
| Create/edit flows | Limited (demo-only) | Yes | Yes |
| Outgoing branches per move | Up to 10 | Up to 10 | Up to 10 |
| Merges (multiple incoming) | Yes | Yes | Yes |
| Dangling paths allowed | Yes | Yes | Yes |
| Reorder nodes / replace root | Yes | Yes | Yes |
| Save drafts | No (no persistence) | Yes | Yes |

#### 4.3 Sharing & Inbox

| Capability | Guest | Free | Pro / Trial |
|------------|-------|------|-------------|
| Create unlisted share links | No | Limited (placeholder) | Yes (higher limits; placeholder) |
| Open share link & view flow | Yes | Yes | Yes |
| Accept imports into Inbox | Yes (view-only) | Yes (cap 10; view-only) | Yes (cap soft) |
| Practice imported Inbox item | No | No | Yes |
| Move Inbox item to saved library | No | Yes (if within flow cap) | Yes |

#### 4.4 Practice Mode

| Capability | Guest | Free | Pro / Trial |
|------------|-------|------|-------------|
| Practice saved flows | No | Yes (credits only) | Yes (unlimited) |
| Practice Inbox items | No | No | Yes |
| Monthly practice credits | N/A | 3 / month | N/A |
| Practice session length | N/A | No restriction | No restriction |
| Logging (actual duration) | No | Yes (credit sessions) | Yes |

#### 4.5 Media Rules

| Capability | Guest | Free | Pro / Trial |
|------------|-------|------|-------------|
| Attach link-based media (YouTube, etc.) | View only | Yes | Yes |
| Upload video from gallery/camera | No | No | Yes (private-only) |
| Upload cap (all uploads total) | N/A | N/A | 2GB total cap |
| Uploads are shareable via links | No | No | No (private-only; use links to share) |

### 5. Practice Credits (Free)

Practice is a Pro feature. Free users receive a small number of practice credits to experience core value without immediately hitting a hard wall.

| Rule | Details |
|------|---------|
| **Grant** | Free receives **3 practice credits per month** (reset monthly) |
| **Spend rule** | Starting a Practice session on a **saved flow** consumes 1 credit. Credits **cannot be used on Inbox items**. |
| **Scope** | Credit covers entire session regardless of duration or number of paths selected |
| **Exhaustion behavior** | When credits = 0, attempting to start practice triggers Paywall Gate (CH25) |
| **Transparency** | Practice entry points must clearly show remaining credits: "Credits: 2/3 this month" |
| **Anti-exploit** | Credits cannot be transferred; reinstalling does not reset credits; credits are tied to account |

#### 5.1 Credit UI Surfaces (Minimum)

- **Practice tab header:** Shows remaining credits (Free only)
- **Flow detail screen:** Practice CTA shows either "Practice (1 credit)" or "Practice (Pro)"
- **Paywall:** Includes short explanation that Pro removes credit limits and unlocks full practice features
- **Settings → Subscription:** Shows current plan and next renewal date (Pro/Trial) or upgrade CTA (Free)

### 6. Trial Behavior (7-Day Trial)

| Rule | Details |
|------|---------|
| **Trial = Pro** | During trial, all Pro gates are removed (Practice, uploads, etc.) |
| **Start trial triggers** | Any Pro-only gate can offer a trial CTA (owned by CH25) |
| **End of trial** | If user converts, plan becomes Pro; if user cancels or trial expires without conversion, plan becomes Free |
| **Downgrade enforcement** | If user downgrades to Free and has more than 2 saved flows, **do not delete data**; enforce cap by making flows beyond cap **read-only** until upgrade or deletion (UX owned by CH15/CH25) |
| **Restore purchases** | Must be available from Settings and from paywall if purchase state is unclear |

### 7. Standard Gating Copy (Non-Final, but Canonical Intent)

*Note: Exact marketing copy owned by CH25/CH26. CH08 defines intent for consistent triggers.*

| Gate Type | Copy |
|-----------|------|
| **Account Gate (Guest saving)** | "Create an account to save your work." Secondary: "Guest mode is for exploring only." |
| **Flow cap gate (Free cap=2)** | "You've reached the Free limit (2 saved flows)." Options: "Upgrade to Pro" and "Manage flows". |
| **Practice credit gate (credits remain)** | "This practice uses 1 credit." |
| **Practice credit gate (exhausted)** | "Practice is a Pro feature." |
| **Inbox practice gate (Free)** | "Save this flow to your library to practice it." (then apply flow cap rules) |
| **Upload gate (Free)** | "Uploads are Pro-only. Use links to share videos." |

### 8. Subscription System Expectations (StoreKit)

| Requirement | Details |
|-------------|---------|
| **Platform** | Use StoreKit subscriptions (iOS-only V1) |
| **Pricing** | Single monthly product at **$9.99** with **7-day trial** is default direction (see CH25) |
| **Required flows** | Purchase, restore, cancel flow (link out to Apple subscription management), and clear plan status screen |
| **Error handling** | When purchase is pending or fails, keep user in current plan and show recoverable error state (see CH31) |

### 9. Analytics Hooks (Entitlement-Related)

*Event taxonomy owned by CH33, but CH08 specifies minimum events needed to validate gating and conversion flows.*

| Event | Parameters |
|-------|------------|
| `entitlement_snapshot_loaded` | planState, creditsRemaining, caps |
| `gate_shown` | reasonCode, planState, surface (flow_save\|practice_start\|upload\|share\|inbox) |
| `upgrade_clicked` | surface, planState |
| `trial_started` | — |
| `purchase_started` | — |
| `purchase_success` | — |
| `purchase_failed` | — |
| `restore_clicked` | — |
| `restore_success` | — |
| `restore_failed` | — |
| `credit_spent` | flowId, sessionId |
| `downgrade_detected` | previousPlan, newPlan, hasOverCapFlows |

### 10. Acceptance Tests (CH08)

| Test | Given | When | Then |
|------|-------|------|------|
| Guest saving | I am a Guest | I tap Save on a flow | I see Account Gate and flow is not persisted |
| Free flow cap | I am Free with 2 saved flows | I attempt to save new flow | I see cap gate with Upgrade and Manage options, and save does not complete |
| Free credits | I am Free with 2 credits remaining | I start Practice from saved flow | 1 credit is consumed and session begins |
| No inbox practice for Free | I am Free | I open Inbox flow and tap Practice | I see gating that instructs saving to library (and cap rules apply) |
| Inbox cap | I am Free with 10 inbox items | I receive another import | Import is blocked with clear message and CTA to clear items or upgrade |
| Trial parity | I am in Trial | I open Practice or Upload | No paywall or credit gates appear |
| Downgrade behavior | I was Pro and saved 10 flows | My plan becomes Free | My data remains intact and app enforces Free cap without deleting flows |
| Uploads | I am Pro | I upload a video under 2GB cap | It saves as private-only and is not included in share payloads |

---

## Open Questions / Ambiguities / Placeholders

### From CH07

1. **Handle Feature Enablement**
   - Section 4: Handle is "optional in V1; recommended. If collected..."
   - **Question:** Is handle collection enabled for V1 or deferred?
   - **Impact:** Profile setup flow, uniqueness validation infrastructure

2. **Email Verification Strictness**
   - Section 6.4: "If you prefer stricter: block saving until verified. If that change is desired, create PLACEHOLDER in CH00 and decide later."
   - **Current V1 Rule:** Saving flows allowed immediately; sharing and export require verification
   - **Decision needed:** Confirm this is acceptable or escalate stricter approach
   - **Impact:** Auth flow complexity, user onboarding friction

3. **Practice Demo for Guests**
   - Section 5.1: "View the Practice mode demo (predetermined session) if enabled by CH25 (demo-only)"
   - **Dependency:** CH25 must specify if Guest practice demo is enabled
   - **Impact:** Guest experience, demo content requirements

4. **Password Blacklist Implementation**
   - Section 8.3: "disallow common passwords (basic blacklist)"
   - **Specification needed:** Size and source of blacklist
   - **Impact:** Sign-up validation implementation

5. **Login Throttling Thresholds**
   - Section 10: "Throttle login attempts to reduce brute force (see CH30 for escalation messaging)"
   - AT-07-06 references "5 times" but thresholds owned by CH30
   - **Dependency:** CH30 must define exact throttle thresholds
   - **Impact:** Security implementation

6. **Route ID Discrepancy with CH05**
   - CH07 §8 uses route IDs like `auth/welcome`, `auth/email_choice`, etc.
   - CH05 uses Route IDs like `Welcome`, `AuthEmailSignup`, etc.
   - **Reconciliation needed:** Align route ID naming conventions
   - **Impact:** Navigation implementation consistency

### From CH08

7. **PLACEHOLDER: Pro Share-Link Daily Cap**
   - **Options:** Soft caps only / Hard daily limit
   - **Default:** Soft caps only
   - **Owner:** CH17/CH30

8. **PLACEHOLDER: Pro Saved-Flow Soft Cap**
   - **Owner:** CH15/CH30
   - **Impact:** Abuse prevention for Pro users

9. **PLACEHOLDER: Pro Inbox Cap**
   - **Owner:** CH18/CH30
   - **Impact:** Storage limits for Pro users

10. **PLACEHOLDER: Offline Subscription Verification Grace Period**
    - **Owner:** CH28
    - **Question:** How many hours/days before Pro (grace) degrades to Free?
    - **Impact:** Offline user experience

11. **PLACEHOLDER: Exact Paywall Copy, Scientific Claims, Education Pages**
    - **Owner:** CH25/CH26
    - **Impact:** Paywall implementation, App Store compliance

12. **PLACEHOLDER: Exact Node Caps Beyond Branch=10**
    - **Owner:** CH12/CH30
    - **Question:** Is there a max total nodes per flow?
    - **Impact:** Flow builder limits

13. **Free Share Link Limits**
    - Section 4.3: "Create unlisted share links - Limited (placeholder)"
    - **Specification needed:** Exact limit for Free users
    - **Owner:** CH17

14. **Export Data Implementation**
    - Section 4.1: "Export data - Optional (placeholder) for Free, Yes (placeholder) for Pro"
    - **Specification needed:** What formats, what data, when available?
    - **Owner:** CH34

15. **Practice Credit Reset Timing**
    - Section 5: "3 practice credits per month (reset monthly)"
    - **Question:** Reset on calendar month or 30 days from signup?
    - **Impact:** Credit tracking implementation

16. **Downgrade Flow UI**
    - Section 6: "making flows beyond the cap read-only until upgrade or deletion (UX owned by CH15/CH25)"
    - **Dependency:** CH15/CH25 must define exact read-only UX
    - **Impact:** Downgrade user experience

---

## Cross-References to Other Chapters

### From CH07

| Chapter | Dependency |
|---------|------------|
| **CH00** | Manifest, global locks, decision log |
| **CH01** | V1 Scope |
| **CH02** | Constraints |
| **CH04** | Navigation Map (all routes must exist) |
| **CH05** | Screen Inventory (authoritative screen list) |
| **CH08** | Entitlements & Plan States (Guest/Free/Pro definitions) |
| **CH25** | Monetization (demo practice enablement, paywalls) |
| **CH28** | Offline Behavior & Sync (clear cache, edits sync) |
| **CH30** | Safety/Abuse (login throttle thresholds) |
| **CH31** | Error States |
| **CH34** | Data Export & Account Deletion |

### From CH08

| Chapter | Dependency |
|---------|------------|
| **CH00** | Manifest |
| **CH07** | Auth & Account (Guest definition) |
| **CH12-CH22** | Flow Builder & Practice (feature gates) |
| **CH15** | Library (flow cap UX, downgrade UX) |
| **CH17-CH19** | Sharing/Inbox/Imports (share limits, inbox caps) |
| **CH25** | Monetization (paywall layout, copy, pricing) |
| **CH26** | Scientific Claims (paywall education content) |
| **CH28** | Offline Behavior (grace period) |
| **CH29** | Storage & Limits (media cap, upload rules) |
| **CH30** | Safety/Abuse (soft caps) |
| **CH31** | Error States (purchase errors) |
| **CH33** | Analytics (event taxonomy) |
| **CH34** | Export & Deletion |

---

## Explicit Assumptions Detected

### From CH07

1. **Firebase Authentication Backend**
   - Based on context.md: Backend is Firebase
   - Auth will use Firebase Authentication with Apple/Google/Email providers

2. **iOS Keychain for Token Storage**
   - Explicitly stated (Section 7.1, 10)
   - No AsyncStorage for auth tokens

3. **No Magic Link Authentication**
   - Email auth uses password, not magic links
   - Different from CH05 which mentioned "magic link" as option

4. **App Store Compliance Required**
   - Apple sign-in must be offered when other providers exist
   - Required for App Store approval

5. **Single Device per Session Not Required**
   - Multi-device login explicitly allowed (Section 7.3)
   - Sync handled by CH28

### From CH08

6. **StoreKit for iOS Subscriptions**
   - Explicitly stated (Section 8)
   - No alternative payment providers in V1

7. **Single Subscription Product**
   - $9.99/month with 7-day trial
   - No annual option mentioned for V1

8. **Credits Tied to Account, Not Device**
   - Section 5: "reinstalling does not reset credits; credits are tied to account"
   - Prevents credit exploitation

9. **Trial Behaves Exactly as Pro**
   - No feature differences during trial
   - All Pro gates removed

10. **Downgrade Preserves Data**
    - Section 6: "do not delete data"
    - Flows become read-only, not deleted

11. **Branch Cap is Global (10)**
    - Section 2: "branchCapPerMove: integer (10; global lock)"
    - Applies to all plan states equally

12. **Uploaded Media is Private-Only**
    - Cannot be shared
    - Link-based media is the shareable option

---

## Implementation Notes

### CH07 Replit Build Prompt

```
You are implementing Handz V1 PRD Bundle: Chapter CH07 Authentication & Account System only.
Bundle ID: HZ-V1. Follow CH00 rules: no guessing, cross-reference dependencies, and stop if an assumption is needed.

Implement the following, in order:
1) Routing: Create route IDs exactly as listed in CH07 §8
2) Welcome Gate UI: 3 provider buttons + Try without account. If offline, disable provider buttons and show banner. If session exists, auto-navigate to main app.
3) Provider auth: Sign in with Apple (iOS), Google sign-in, and Email sign-up/login flows. Store session tokens securely (Keychain).
4) Guest rules: Enforce CH07-G1..G5. Guests must not access editable flow builder or save flows/moves locally. Any attempt triggers conversion prompt modal.
5) Conversion prompt: Build a reusable modal component with title/body/buttons per CH07 §6.3. Ensure post-signup returns user to pre-gate context.
6) Email verification banner: After Email sign-up, show persistent banner with Resend and I've verified; block sharing/export until verified (note: saving flows allowed).
7) Logout: Add Settings action that clears tokens and resets navigation stack to Welcome Gate.

Do NOT implement monetization screens, practice gating, flow builder, or storage limits beyond the Guest gates described here.
```

### CH08 Replit Build Prompt

```
You are implementing Handz V1. Use CH00 and CH08 only.

Goal: implement a global Entitlements system and gating utilities that every screen can call.

Build steps:
1) Create an EntitlementsContext that exposes:
   - planState (guest/free/trial/pro/pro_grace)
   - caps (savedFlowCap=2 for free; inboxCap=10 for free; branchCapPerMove=10)
   - practiceCreditsRemaining (free only; 3/month)
   - booleans: canPersist, canPractice, canPracticeInboxItems, canUploadMedia, canCreateShareLinks

2) Implement a single function: requireEntitlement(action, context) -> { allowed, gateType, reasonCode }

3) Implement standard gating surfaces:
   - AccountGateModal for Guest persistence attempts
   - PaywallModal for Pro features (stub content; final layout owned by CH25)
   - CapGateSheet for flow cap and inbox cap (Upgrade / Manage)

4) Wire gating into key CTAs

5) Add analytics hooks: gate_shown, upgrade_clicked, credit_spent

Constraints:
- Do not guess caps; use CH08 exactly.
- If you must make an assumption, write it in a PRD_ASSUMPTIONS block and stop that feature.
```

### Troubleshooting Notes

**CH07:**
- Apple sign-in works on simulator but fails on device: verify bundle ID, entitlements, and Apple sign-in capability enabled
- Google sign-in redirect errors: confirm OAuth client IDs for iOS and correct redirect URI scheme
- Email verification never clears: ensure verification status is re-fetched from auth provider after "I've verified" tap
- Users can access private screen after logout via back button: reset navigation stack; do not just navigate
- Conversion prompt loops: ensure "Not now" only returns to browsing demo routes, never to gated build routes
- Duplicate accounts created: confirm merge-by-email logic in §3.4 and prevent auto-merge on private relay emails

**CH08:**
- User sees Pro UI but gates still appear: check entitlement snapshot refresh timing; ensure UI reads from same snapshot used by requireEntitlement()
- Credits not decrementing: credit spend is tied to "session start" and is transactional; prevent double-spend on screen re-mount
- User downgraded but cannot access any flows: cap enforcement should be "read-only beyond cap", not "hide everything"
- Inbox full blocks imports unexpectedly: distinguish "import receipt" vs "save to library"; Free cap 10 applies to Inbox items stored
- Uploads included in share payloads: enforce CH29: uploaded media is private-only and must be excluded from share/export

---

## Summary

**CH07 (Authentication & Account System)** provides a complete specification for auth flows, Guest mode, and account gating. Key strengths:

✅ Clear authentication provider specifications (Apple/Google/Email)  
✅ Well-defined Guest restrictions with 5 explicit gating rules (CH07-G1 to G5)  
✅ Detailed screen specifications with route IDs, components, validation, and error states  
✅ Post-conversion context restoration (returns user to pre-gate action)  
✅ Security and compliance considerations documented  

**CH08 (Entitlements & Plan States)** provides a comprehensive entitlement system specification. Key strengths:

✅ Clear plan state definitions (Guest/Free/Pro/Trial + derived states)  
✅ Complete entitlement matrix across 5 feature domains  
✅ Practice credit system with anti-exploit rules  
✅ Trial and downgrade behavior clearly specified  
✅ Analytics hooks for conversion tracking  

**Combined, these chapters form the complete access control foundation for Handz V1.**

**Primary blockers for implementation:**
1. Route ID reconciliation between CH05 and CH07
2. CH25/CH26 for paywall copy and scientific claims
3. CH30 for login throttle thresholds and soft cap definitions
4. CH28 for offline grace period duration

**Recommendation:** Approve this batch. Both chapters are well-specified and implementation-ready for core functionality. Outstanding placeholders are properly delegated to their owning chapters.

---

**End of PRD Batch 02B**

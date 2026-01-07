# PRD Batch 02A1: Screen Inventory

**Source:** CH05 - Screen Inventory (R1)  
**Generated:** 2026-01-05  
**Status:** Awaiting Approval

---

## Chapter Overview

CH05 provides a comprehensive enumeration of all 58 user-facing screens (S00-S58) required for Handz V1. The chapter serves as the canonical list of screens and their minimal required behaviors, including:

- Screen purpose and access rules (Guest/Free/Pro)
- Primary and secondary actions
- Key states for each screen
- Route IDs for navigation implementation
- Presentation types (full-screen, modal, overlay)

This chapter is intended as a reference for navigation implementation and scope validation, delegating deeper specifications to other chapters (CH04 for navigation flow, CH06 for design system, individual feature chapters for business logic).

---

## Key Requirements

### Screen Inventory Structure

**Total Screens:** 58 screens (S00-S58)

**Screen Categories:**
1. Global & System (3 screens: S00, S01, S58)
2. Entry & Authentication (5 screens: S02-S06)
3. Onboarding & Setup (3 screens: S07-S09)
4. Main Shell (2 screens: S10-S11)
5. Library & Flow Management (5 screens: S12-S16)
6. Flow Builder (5 screens: S17-S21)
7. Move Library (4 screens: S22-S25)
8. Sharing & Inbox (6 screens: S26-S31)
9. Practice (8 screens: S32-S39)
10. Gameplans, Mastery & Maintenance (5 screens: S40-S44)
11. Monetization & Subscription (5 screens: S45-S49)
12. Settings, Account, Support, Legal (8 screens: S50-S57)

### Global & System Screens

**S00 - App Launch / Splash**
- Route ID: `Splash`
- Access: All users
- Purpose: Show brand mark while initializing app, loading auth/session state, checking critical conditions
- Auto-advance to next appropriate entry screen once init completes
- States: Cold start, Warm start, Offline start, Init failure
- Note: iOS permissions/StoreKit checks should occur after navigation to avoid blocking splash

**S01 - Force Update / Maintenance Gate**
- Route ID: `MaintenanceGate`
- Access: All users (conditional)
- Purpose: Block usage if breaking backend change or required app update detected
- Actions: Show 'Update' (opens App Store) or 'Try again' if maintenance
- Only shown when remote config indicates forced update or maintenance

**S58 - Soft Cap / Warning Modal**
- Route ID: `WarningModal`
- Access: Contextual (Free/Pro/Guest)
- Purpose: Standard warning ladder UI for approaching/exceeding soft caps
- Actions: Upgrade, Adjust behavior, Dismiss, Learn more
- States: Soft warning, Hard block

### Entry & Authentication Screens

**S02 - Welcome Gate**
- Route ID: `Welcome`
- Access: All users (first-run or logged-out)
- Purpose: Primary entry screen explaining Handz value
- Actions: Sign up with Apple/Google/Email, Log in, Try Without Account (Guest)
- Guest choice must communicate limitations (cannot save flows, cannot customize move packs, cannot practice)

**S03 - Email Sign Up**
- Route ID: `AuthEmailSignup`
- Access: Logged-out users
- Purpose: Collect email + password (and optionally display name)
- Note: If using magic link instead of password, replace with email-only + 'Send link'

**S04 - Email Log In**
- Route ID: `AuthEmailLogin`
- Access: Logged-out users
- Purpose: Email login with password (or magic link)
- States: Invalid credentials, Rate-limited, Offline

**S05 - Forgot Password**
- Route ID: `AuthForgotPassword`
- Access: Logged-out users
- Purpose: Request password reset email
- Note: Copy should avoid account enumeration where possible

**S06 - Email Verification Pending**
- Route ID: `AuthVerifyPending`
- Access: New email signups (conditional)
- Purpose: Explain user must verify email; allow resend; allow changing email
- Actions: Resend verification, Open email app, Continue (re-check), Change email, Log out

### Onboarding & Setup Screens

**S07 - Quick Setup: Training Styles**
- Route ID: `OnboardingStyles`
- Access: New account users (optional)
- Purpose: Select training styles (Boxing, Muay Thai, Kickboxing, Karate, TKD, MMA, Other) - drives default move pack suggestions
- Note: No gym code/community in V1; keep lightweight

**S08 - Quick Setup: Move Pack Picker**
- Route ID: `OnboardingMovePacks`
- Access: New account users (recommended); not available for Guest
- Purpose: Choose default move packs to load into library (e.g., Striking Essentials, Boxing Essentials)
- Guest users do not get this; show prompt to create account to customize

**S09 - Onboarding: What is a Flow?**
- Route ID: `OnboardingFlowIntro`
- Access: New users (optional but recommended)
- Purpose: Teach core mental model: flow = decision tree of striking actions/responses; show micro-demo
- Note: Keep vocabulary accessible; avoid assuming users know 'nodes' or 'graphs'

### Main Shell Screens

**S10 - Main App Shell (Tab Bar)**
- Route ID: `AppTabs`
- Access: Logged-in users; Guest sees limited version
- Purpose: Holds bottom tab navigation for V1 sections
- Note: CH05 lists screens; CH04 defines exact tab titles/order

**S11 - Home / Dashboard**
- Route ID: `Home`
- Access: Logged-in users; Guest has demo variant
- Purpose: Landing area showing recent flows, quick actions, practice CTA, streak summary, demo flow cards
- Actions: Open Flow, Create New Flow, Start Practice (gated), Browse Demo Flows (Guest)
- States: Empty library, Has flows, Guest mode, Offline
- Note: If CH04 decides no Home tab, this becomes Library root header section

### Library & Flow Management Screens

**S12 - Library: Flows List**
- Route ID: `LibraryFlows`
- Access: Logged-in users; Guest sees demo-only list
- Purpose: Browse user flows; search by flow name; sort; move flows into folders; create new flow
- Actions: Search flows, Open flow, Create flow (gated for Guest), Create folder, Move flow to folder
- States: Empty state (0 flows), At cap (Free=2), Offline (cached list)
- Note: Search is flow-only in V1 (as locked)

**S13 - Library: Folder Picker (Modal)**
- Route ID: `FolderPickerModal`
- Presentation: Bottom sheet / Modal
- Access: Logged-in users
- Purpose: Choose folder to move one/more flows into; create folder inline

**S14 - Library: Create Folder**
- Route ID: `CreateFolder`
- Presentation: Modal
- Access: Logged-in users
- Purpose: Create new folder for organizing flows

**S15 - Flow Detail View**
- Route ID: `FlowDetail`
- Access: Logged-in users; Guest can view demo flows
- Purpose: Central hub for a single flow: view overview, edit/open builder, start practice, share, duplicate
- Note: Practice credits usable only on saved flows; inbox items not eligible

**S16 - Flow Metadata Editor**
- Route ID: `FlowMetaEdit`
- Presentation: Modal
- Access: Logged-in users
- Purpose: Edit flow title, description, tags (flow-level), folder assignment, visibility (V1: unlisted share only)

### Flow Builder Screens

**S17 - Flow Builder Canvas**
- Route ID: `FlowBuilder`
- Access: Logged-in users (creation/editing gated for Guest)
- Purpose: Create/edit flowchart: add moves, add branches (up to 10), add optional sequence nodes, pan/zoom
- Actions: Add first move, Add next move, Add branch, Edit connection/sequence, Undo/redo (if present), Save
- Note: Interaction model owned by CH12/CH13

**S18 - Move Picker (from Builder)**
- Route ID: `MovePickerModal`
- Presentation: Bottom sheet / Full-screen modal
- Access: Logged-in users (Guest view-only)
- Purpose: Search/select move to place on canvas (from default + custom move library)
- Note: If user selected move packs, show 'Installed packs' sections

**S19 - Node Action Menu**
- Route ID: `NodeMenu`
- Presentation: Context menu / Bottom sheet
- Access: Logged-in users
- Purpose: Actions for selected node: replace move, add branch, edit node metadata, delete node, set as root

**S20 - Add Branch Sheet**
- Route ID: `AddBranchSheet`
- Presentation: Modal / Bottom sheet
- Access: Logged-in users
- Purpose: Define opponent trigger label (optional) and choose response move; creates new outgoing path from node
- States: Branch limit reached (10), Validation, Offline
- Note: Branches can branch again; merges allowed; dangling paths allowed

**S21 - Sequence Detail Editor**
- Route ID: `SequenceEditor`
- Presentation: Modal
- Access: Logged-in users
- Purpose: Edit transition details between two connected moves (optional)
- Note: Full spec owned by CH14

### Move Library Screens

**S22 - Moves Library**
- Route ID: `MovesList`
- Access: Logged-in users; Guest sees read-only default list
- Purpose: Browse all moves (default + custom); filter; search; open move detail; create custom move (gated)
- Note: Default move list ships with tags/families, no technique descriptions (locked)

**S23 - Move Detail**
- Route ID: `MoveDetail`
- Access: Logged-in users; Guest read-only
- Purpose: View single move: name, tags/family, user-added notes/media (if allowed), variants/aliases
- States: Default move (limited edit), Custom move (editable), Offline
- Note: Editing rules and revert model owned by CH11

**S24 - Create/Edit Custom Move**
- Route ID: `MoveEdit`
- Presentation: Full-screen or Modal
- Access: Logged-in users (not Guest)
- Purpose: Create custom move or edit existing custom move; progressive disclosure fields; choose family/alias/variant relationships
- Note: Ship with minimal required fields; deeper fields optional

**S25 - Revert Changes Picker**
- Route ID: `RevertPicker`
- Presentation: Modal
- Access: Logged-in users
- Purpose: Let user revert changes at different scopes: this move only, this flow only, this library section, etc.

### Sharing & Inbox Screens

**S26 - Share Flow (Unlisted)**
- Route ID: `ShareFlow`
- Presentation: Modal / Bottom sheet
- Access: Logged-in users
- Purpose: Generate and manage unlisted share link for a flow; show link + actions
- Actions: Create link, Copy link, Revoke link
- Note: Share link lifecycle and caps owned by CH17

**S27 - My Share Links**
- Route ID: `ShareLinksList`
- Access: Logged-in users
- Purpose: List active share links user has created; allow revoke; show counts/limits

**S28 - Shared Flow Viewer (Web / In-app)**
- Route ID: `SharedFlowView`
- Access: Anyone with link; limited for non-logged-in
- Purpose: View shared flow (read-only) and optionally import into inbox if logged in
- States: Logged out, Logged in, Expired link, Offline
- Note: If flow contains private uploads, show placeholder and explain not shared

**S29 - Inbox: Imports List**
- Route ID: `Inbox`
- Access: Logged-in users
- Purpose: View received imports (flows shared to you). Free cap=10; Free can view but cannot practice inbox items
- Note: Saving to library requires account; if Free at 2 saved flows cap, require delete/upgrade first

**S30 - Inbox Item Preview**
- Route ID: `InboxItemView`
- Access: Logged-in users
- Purpose: View imported flow (read-only until saved); show what will happen if saved; show conflict notices
- Note: Conflict resolution owned by CH19

**S31 - Import Conflict Resolution**
- Route ID: `ImportConflict`
- Presentation: Modal / Full-screen
- Access: Logged-in users
- Purpose: Resolve missing moves/custom payloads: choose mapping, keep in flow-only, add to library, or create new variant move
- Note: Keep it simple (as locked): no extra messaging prompts, no 'add both' complexity

### Practice Screens

**S32 - Practice Home**
- Route ID: `PracticeHome`
- Presentation: Full-screen (Tab root)
- Access: Logged-in users; practice gated for Free/Guest
- Purpose: Entry point for practice: explain value; show credits, last session, start practice CTA; upsell when locked
- States: Pro, Free with credits, Free out of credits, Guest
- Note: Practice is paywalled; Free gets 3 monthly credits usable only on saved flows

**S33 - Practice Setup**
- Route ID: `PracticeSetup`
- Access: Logged-in users (Pro or Free w/ credits)
- Purpose: Select paths (from saved flows only), order them, set timers/reps assumptions, start session
- Note: User can choose which path first/second etc; reorder required in V1

**S34 - Practice Active Session**
- Route ID: `PracticeActive`
- Access: Logged-in users (Pro or credit session)
- Purpose: Run timed drill sets; show current path; allow pause, skip, early completion, end session
- Actions: Pause/Resume, Skip set, Complete set early, End practice
- States: Running, Paused, Rest, Ended early, Completed
- Note: Early end behavior: if timer hits 0 OR user taps 'Completed Set', set completes

**S35 - Practice Pause Overlay**
- Route ID: `PracticePaused`
- Presentation: Overlay
- Purpose: Pause state UI while timer stopped; keep context; allow resume or end

**S36 - Practice Rest Screen**
- Route ID: `PracticeRest`
- Presentation: Overlay / Full-screen
- Purpose: Rest between sets; countdown; show what's next; allow skip rest or end

**S37 - Practice Session Summary**
- Route ID: `PracticeSummary`
- Presentation: Full-screen / Modal
- Purpose: Show session results with concrete accomplishments: paths drilled, sets completed, actual duration, assumed reps, streak update
- States: Completed, Interrupted, Partial completion
- Note: Must emphasize what was accomplished, not just time spent

**S38 - Practice History**
- Route ID: `PracticeHistory`
- Access: Logged-in users
- Purpose: List past sessions; filter; open detail; show streak/consistency

**S39 - Practice Log Detail**
- Route ID: `PracticeLogDetail`
- Access: Logged-in users
- Purpose: Detailed view of single practice session: per-path breakdown, status, timestamps, links back to flows

### Gameplans, Mastery & Maintenance Screens

**S40 - Gameplans Home**
- Route ID: `GameplansHome`
- Presentation: Full-screen (Tab root)
- Access: Logged-in users (Pro features likely)
- Purpose: Entry for mastery/gameplans: create gameplan, view active plans, maintenance queue, progress snapshot
- Note: Gameplan naming customizable; not just 'Gameplan 1'

**S41 - Create Gameplan (Wizard)**
- Route ID: `GameplanCreate`
- Access: Logged-in users
- Purpose: Select multiple paths/flows to master; choose goal type; generate plan
- Note: Allow selecting entire flow, multiple flows, or individual paths; user can edit later

**S42 - Gameplan Detail**
- Route ID: `GameplanDetail`
- Access: Logged-in users
- Purpose: View gameplan: included paths, mastery status per path, next drills, maintenance settings
- Note: User can downgrade mastery if trust issue (explicit control)

**S43 - Maintenance Queue**
- Route ID: `MaintenanceQueue`
- Access: Logged-in users
- Purpose: Show what needs maintenance: prioritized list with filters (most used, most recent, etc.) and search
- Note: This is the user's 'browse' feature for maintenance

**S44 - Maintenance Session**
- Route ID: `MaintenanceSession`
- Access: Logged-in users
- Purpose: Run short maintenance session similar to practice; focuses on retention; logs completion
- Note: If practice is paywalled but maintenance is included/excluded, document in CH25

### Monetization & Subscription Screens

**S45 - Upgrade Paywall**
- Route ID: `Paywall`
- Presentation: Full-screen / Modal
- Access: Logged-in users (Free) and Guests (pre-account)
- Purpose: Explain Pro value (practice, mastery, maintenance, sharing limits, uploads, etc.) and start trial/subscription
- Actions: Start 7-day trial, Subscribe $9.99/mo, Restore purchases
- States: From practice lock, From save lock, From cap reached
- Note: Copy/claims owned by CH25/CH26

**S46 - Plan Comparison**
- Route ID: `PlanCompare`
- Access: All users (from paywall/settings)
- Purpose: Side-by-side list of Guest/Free/Pro capabilities and caps; reduce confusion

**S47 - Trial/Subscription Success**
- Route ID: `PurchaseSuccess`
- Presentation: Modal
- Access: Users who purchase
- Purpose: Confirm upgrade; highlight what is now unlocked; provide next best action

**S48 - Subscription Management**
- Route ID: `ManageSubscription`
- Access: Pro users
- Purpose: Route user to iOS subscription management; explain how to cancel/renew; show current plan status

**S49 - Restore Purchases**
- Route ID: `RestorePurchases`
- Presentation: Modal
- Access: All logged-in users
- Purpose: Restore StoreKit purchases; show success/failure

### Settings, Account, Support, Legal Screens

**S50 - Settings Home**
- Route ID: `Settings`
- Presentation: Full-screen (Tab root)
- Access: Logged-in users; Guest limited
- Purpose: Account settings, app preferences, export/delete, legal, help; show plan status and upgrade button

**S51 - Account & Profile**
- Route ID: `AccountProfile`
- Access: Logged-in users
- Purpose: Edit profile (display name), view email, log out, delete account, re-run onboarding

**S52 - Notification Settings**
- Route ID: `NotifSettings`
- Access: Logged-in users
- Purpose: Control reminders (practice, maintenance), frequency, quiet hours; request OS permission

**S53 - Data Export**
- Route ID: `DataExport`
- Access: Logged-in users
- Purpose: Export flows/moves/logs; choose format; explain privacy

**S54 - Delete Account Confirmation**
- Route ID: `DeleteAccount`
- Presentation: Full-screen / Modal
- Access: Logged-in users
- Purpose: Confirm account deletion; explain what is deleted; require explicit confirmation

**S55 - Legal: Terms**
- Route ID: `Terms`
- Access: All users
- Purpose: Display Terms of Service

**S56 - Legal: Privacy Policy**
- Route ID: `Privacy`
- Access: All users
- Purpose: Display Privacy Policy

**S57 - Help / FAQ**
- Route ID: `Help`
- Access: All logged-in users
- Purpose: Basic help content and links; explain flow concept; contact support

---

## Open Questions / Ambiguities / Placeholders

### From Chapter (Page 5: "Placeholders & decisions that remain flexible")

1. **Tab Structure Decision Pending** (CH04)
   - Exact bottom tab titles, order, and default tab on first launch
   - Whether distinct Home/Dashboard tab exists, or whether Library is primary root
   - **Impact:** Affects S10 (AppTabs) and S11 (Home) implementation

2. **Practice vs Maintenance Gating** (CH25)
   - Exact line between Practice vs Maintenance availability in Free vs Pro
   - S44 note indicates need for CH25 clarification
   - **Impact:** Affects monetization strategy and user access patterns

3. **Undo/Redo in Builder** (CH12/CH13)
   - Whether undo/redo is included in V1 builder
   - If not, need to confirm minimal safe editing affordances
   - **Impact:** Affects S17 (FlowBuilder) feature set

4. **Export Formats** (CH34)
   - Exact export formats (JSON/CSV/PDF) and what data is included
   - **Impact:** Affects S53 (DataExport) functionality

5. **Magic Link vs Password**
   - S03 notes "If using magic link instead of password, replace with email-only + 'Send link'"
   - **Decision needed:** Which authentication method for email signup/login?
   - **Impact:** Affects S03, S04 implementation

6. **Email Verification Requirement**
   - S06 marked as "conditional" for new email signups
   - **Decision needed:** Is email verification mandatory or optional?
   - **Impact:** Auth flow and user onboarding

7. **Maintenance Session Gating**
   - S44 notes: "If practice is paywalled but maintenance is included/excluded, document in CH25"
   - **Decision needed:** Is maintenance a Pro-only feature or available to Free?
   - **Impact:** Monetization model and S44 access rules

### Access Rule Clarifications Needed

8. **Guest Capabilities Matrix**
   - Multiple screens note "Guest view-only" or "gated for Guest"
   - Need comprehensive Guest/Free/Pro capability matrix
   - **Cross-reference:** CH08 (Entitlements & Plan States)

9. **Free User Caps**
   - Flows: 2 (confirmed in S12)
   - Inbox: 10 (confirmed in S29)
   - Practice credits: 3 monthly (confirmed in S32)
   - **Question:** Are there other caps not mentioned in CH05?

10. **Share Link Limits**
    - S26 mentions "Rate limit reached (soft cap)" state
    - S27 mentions "show counts/limits"
    - **Specification needed:** Exact share link limits per plan tier
    - **Cross-reference:** CH17 (Sharing - Unlisted Links, Link Lifecycle)

### Technical Specifications Needed

11. **Branch Limit Justification**
    - S20 specifies maximum 10 branches per node
    - **Question:** Is this a technical constraint or UX decision?
    - **Impact:** Data model and UI design

12. **Offline Behavior Scope**
    - Multiple screens list "Offline" as key state
    - **Specification needed:** Which features work offline vs. require connection?
    - **Cross-reference:** CH28 (Offline Behavior and Sync)

13. **Video Upload Features**
    - S23 mentions "Add note/video (Pro upload)"
    - S24 mentions "Attach link/video (rules apply)"
    - **Specification needed:** Video upload limits, formats, storage rules
    - **Cross-reference:** CH29 (Data Storage and Limits)

14. **Search Scope**
    - S12 notes "Search is flow-only in V1 (as locked)"
    - **Question:** What about move search (S22)? Is that also limited?
    - Post-V1: Will search expand to moves, tags, paths?

---

## Cross-References to Other Chapters

### Navigation & Architecture
- **CH04 (Information Architecture & Navigation Map):** Defines exact tab structure, route transitions, and navigation flow
- **CH00 (Master Index & Manifest):** Source of chapter structure and naming

### Authentication & Access Control
- **CH07 (Authentication & Account System):** Auth rules affecting which screens appear
- **CH08 (Entitlements & Plan States):** Guest/Free/Pro gating rules for each screen

### Feature-Specific Chapters
- **CH09 (Default Move Library System):** Backing logic for S22, S08
- **CH10-CH11 (Moves - IDs, Families, Custom Moves):** Backing for S18, S23-S25
- **CH12-CH14 (Flow Builder):** Interaction model for S17-S21
- **CH15-CH16 (Library - Flows, Flow Detail):** Deeper specs for S12-S16
- **CH17-CH19 (Sharing & Inbox):** Full specs for S26-S31
- **CH20-CH22 (Practice Mode):** Detailed rules for S32-S39
- **CH23-CH24 (Mastery & Maintenance):** Logic for S40-S44
- **CH25 (Monetization):** Paywall copy, pricing, trial rules for S45-S49
- **CH26 (Scientific Claims):** Educational content referenced in paywall
- **CH27 (Notifications):** Settings logic for S52
- **CH30 (Warning Ladder):** Standard modal behavior for S58
- **CH31 (Error States):** Error handling for all screens
- **CH34 (Data Export & Account Deletion):** Specs for S53-S54
- **CH36 (Troubleshooting Playbook):** Support content for S57

---

## Explicit Assumptions Detected

1. **iOS-First, Portrait-Only**
   - All screens must fit iOS portrait
   - Modals and sheets must be scroll-safe
   - No horizontal layout requirements

2. **React Native with Expo**
   - Technology stack from context.md confirmed
   - Navigation patterns should align with React Navigation or Expo Router

3. **Guest Mode is Demo-Only**
   - Guests cannot save flows, customize move packs, or practice
   - Guest limitations must be communicated upfront (S02, S11)

4. **Bottom Tab Navigation**
   - S10 specifies bottom tab bar structure
   - Tab count and labels TBD in CH04

5. **Route ID Naming Convention**
   - All route IDs use PascalCase
   - Route IDs should be used as constants in code (per page 56 Replit prompt)

6. **Paywall Integration**
   - Multiple entry points to S45 (Paywall) with source tracking
   - 7-day trial + $9.99/mo subscription model
   - StoreKit integration for iOS

7. **Unlisted Sharing Only in V1**
   - No public/discoverable flows
   - No social features or community feed
   - S28 notes it can be Web or In-app viewer

8. **Practice Credits System**
   - Free users: 3 monthly credits
   - Credits only work on saved flows, not inbox items
   - Credits reset monthly (frequency not specified)

9. **Progressive Disclosure Philosophy**
   - Multiple screens note "progressive disclosure fields" (S24)
   - Keep UI lightweight, avoid overwhelming users

10. **Warning Ladder Pattern**
    - Soft warnings before hard blocks
    - Upgrade CTAs at appropriate moments
    - Standard modal (S58) for cap warnings

---

## Acceptance Criteria for CH05 Completeness

From page 5 of source document:

- [x] **Route Mapping:** Every user-facing route in app maps to exactly one CH05 entry (no orphan screens) - **58 screens documented**

- [ ] **Reachability:** Every CH05 screen can be reached through at least one documented entry point - **Requires CH04 validation**

- [ ] **Access Clarity:** For each screen, Guest/Free/Pro access is unambiguous - **Mostly clear; see Open Questions #8-10**

- [ ] **Paywall Correspondence:** All paywalls/upsells referenced in gating rules correspond to concrete screens/modals listed here - **S45 (Paywall) and S58 (WarningModal) serve this purpose**

- [ ] **Layout Constraints:** No screen requires horizontal layout that cannot fit iOS portrait; modals and sheets must be scroll-safe - **Stated requirement, implementation validation needed**

---

## Implementation Notes

### Navigation Skeleton Requirements (from page 56: Replit build prompt)

**When building navigation:**
1. Create single source of truth for route IDs matching CH05 exactly (case-sensitive)
2. Implement tab bar + stack navigation + modals/sheets
3. Create placeholder UI for each screen showing:
   - Screen code (Sxx)
   - Screen name
   - Route ID
   - Plan state (Guest/Free/Pro)
   - Primary CTA buttons

**Gating Middleware Rules:**
- Guest + account-required action → redirect to S02 (Welcome) or show Paywall
- Free + locked feature → route to S45 (Paywall) with source parameter
- Free + exceeds caps → show S58 (WarningModal) then enforce block

**QA Features:**
- Developer-only screen registry test confirming every route has registered screen
- "Screen Debug" menu (hidden gesture/dev flag) listing all screens for QA navigation

### Troubleshooting Checklist (from page 56)

- **Tap does nothing:** Confirm route ID matches CH05 exactly (case-sensitive)
- **Modal opens blank:** Verify screen component registered and receives expected params
- **Stuck in loop (e.g., Paywall → back → Paywall):** Add 'returnTo' param and respect after upgrade
- **Screens overlap on small devices:** Wrap primary layouts in vertical scroll and test iPhone SE sizes
- **Guest gating feels annoying:** Ensure Guest limitations communicated before they start building (S02/S11); use soft prompts before hard blocks

---

## Summary

CH05 provides a complete and well-structured screen inventory serving as the navigation blueprint for Handz V1. The chapter successfully:

✅ **Enumerates all 58 screens** with consistent metadata (Route ID, Access, Purpose, Actions, States)  
✅ **Establishes clear ownership boundaries** (CH05 owns screen list; other chapters own deeper behavior)  
✅ **Provides implementation guidance** (Replit prompt, troubleshooting checklist)  
✅ **Defines access control patterns** (Guest/Free/Pro gating, paywall entry points)  

**Primary blockers for implementation:**
1. CH04 navigation map (tab structure, exact flows between screens)
2. CH08 entitlements matrix (comprehensive Guest/Free/Pro capabilities)
3. CH25 monetization details (practice vs. maintenance gating, exact caps/limits)

**Recommendation:** Approve this batch and proceed with navigation skeleton implementation using placeholder screens. Deeper feature logic should wait for dependent chapters (CH07-CH08, CH12-CH14, CH20-CH25).

---

**End of PRD Batch 02A1**


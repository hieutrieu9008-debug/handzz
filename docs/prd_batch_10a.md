# PRD Batch 10A: CH35-CH36 Requirements Extraction

**Batch ID:** 10A  
**Chapters:** CH35 (QA & Acceptance Tests), CH36 (Troubleshooting Playbook)  
**Extracted:** 2026-01-05  
**Status:** Draft

---

## CH35: QA Acceptance Tests

**Doc ID:** HZ-V1-CH35_QA_Acceptance_Tests_R1  
**Revision:** R1 (2026-01-02)  
**Status:** Draft  
**Depends on:** CH00, CH05, CH07, CH08, CH09-CH22, CH25, CH28-CH31, CH34  
**Related:** CH36, CH37, CH38

### Owned Decisions
- Test suite structure
- Release gating criteria
- Severity definitions for QA sign-off (implementation tooling remains open)

### Open Questions / Placeholders
- PLACEHOLDER: Automation tooling choice (Detox/XCUITest/etc.)
- PLACEHOLDER: Exact device matrix (final iOS versions/devices)
- PLACEHOLDER: Final default values (unspecified)

---

### 35.1 How to Use This Chapter

**REQ-35.1.1:** This document defines what "done" means for V1.

**REQ-35.1.2:** Execute tests in order:
1. Run Smoke Suite first after every build (10-15 minutes)
2. Run Feature Suites relevant to what changed
3. Before submission, run Full Regression (all suites) and verify all Release Gates pass

**REQ-35.1.3:** Log every failure with:
- Screenshot/screen recording
- Reproduction steps
- Expected vs actual
- Owning chapter reference (See: HZ-V1-CH##)

**REQ-35.1.4:** When a test references another chapter's rule, do NOT reinterpret it - treat that chapter as authoritative.
- Example: Plan gating rules belong to CH08; if a test fails due to gating ambiguity, fix CH08 and bump its revision.

**REQ-35.1.5:** Acceptance format: Every test case includes (a) Preconditions, (b) Steps, (c) Expected Result, expressed as Given/When/Then plus UI copy/routing expectations.

---

### 35.2 Test Matrix (Devices, OS, Conditions)

**REQ-35.2.1:** V1 is iOS-only and portrait-only (See: CH02).

**REQ-35.2.2:** Minimum test coverage matrix:

| Category | Minimum Coverage (R1) |
|----------|----------------------|
| iOS versions | Current iOS major version minus 1 (e.g., iOS 17-18 range) |
| Devices | Small: iPhone SE (2nd/3rd gen), Standard: iPhone 14/15, Large: iPhone 14/15 Pro Max |
| Orientations | Portrait only; verify landscape is either locked or displays blocking message per CH02 |
| Connectivity | Good Wi-Fi, Good LTE/5G, Poor/Flaky (packet loss), Airplane mode |
| Performance modes | Low Power Mode ON/OFF; Background app refresh ON/OFF |
| Locales (spot-check) | en-US (default), plus one non-English locale placeholder (See: CH32) |
| Accessibility | Dynamic Type (small/large), VoiceOver ON, Reduce Motion ON |

**REQ-35.2.3:** Network simulation: During regression, run at least one full Practice session in poor network conditions and one in offline mode to validate offline rules (See: CH28).

---

### 35.3 Test Accounts, Plan States, and Seed Data

**REQ-35.3.1:** Must be able to test Guest, Free, Trial, and Pro behaviors deterministically (See: CH08). Maintain a small set of preconfigured accounts.

#### 35.3.1 Required Test Accounts

**REQ-35.3.2:** GUEST-DEVICE (no account)
- Fresh install; never signed in
- Used to validate guest restrictions and conversion prompts

**REQ-35.3.3:** FREE-NEW (free plan, empty library)
- 0 saved flows; 0 custom moves; inbox empty

**REQ-35.3.4:** FREE-LIMITED (free plan at caps)
- Saved flows = 2 (cap)
- Inbox items = 10 (cap)
- Practice credits = 0 (after using 3 monthly credits)

**REQ-35.3.5:** TRIAL (7-day trial behaves as Pro)
- Trial start timestamp controllable
- Verify conversion to paid or downgrade

**REQ-35.3.6:** PRO (active subscription)
- Has uploads enabled
- Has at least 1 uploaded private video and 3 external links

#### 35.3.2 Standard Seed Data

**REQ-35.3.7:** Default move pack:
- At least 120 essentials including defense/footwork families
- Include aliases: "Teep" and "Push Kick" as distinct canonical moves (locked decision)

**REQ-35.3.8:** Demo flows (non-counting):
- 1 simple linear combo (3 nodes)
- 1 decision flow with 1 node branching into 3 responses
- 1 complex flow with at least 20 nodes, 25 edges, merges, and dangling paths

**REQ-35.3.9:** Import/share fixtures:
- 1 shared flow containing a custom move not present in receiver library (tests conflict handling)
- 1 shared flow containing a standard move but with sender's custom notes (tests merge choices)

**REQ-35.3.10:** Practice fixtures:
- A saved flow with at least 4 drillable paths (tests selection + reorder + multi-set timer)
- A maintenance gameplan containing paths across 2 flows

---

### 35.4 Smoke Suite (Run Every Build)

**REQ-35.4.1:** Goal: catch broken launches, navigation, auth, and the main value loop in under 15 minutes.

**REQ-35.4.2:** If any Smoke case fails, stop and fix before deeper testing.

#### SMK-01: App launches to Welcome Gate
- **Given:** App freshly installed; device online
- **When:** Launch app
- **Then:** Welcome Gate is visible; no crashes; primary CTA buttons visible; portrait lock enforced
- **Steps:**
  1. Install app fresh
  2. Launch app
  3. Verify Welcome Gate renders within 3 seconds
  4. Rotate device; verify portrait lock or intentional block UX

#### SMK-02: Apple/Email sign-in routes work
- **Given:** Device online; FREE-NEW credentials available
- **When:** Sign in with Apple OR email
- **Then:** User lands on Dashboard/Library entry; session persists on relaunch
- **Steps:**
  1. Tap Sign Up / Log In
  2. Complete Apple sign in (or email sign in)
  3. Terminate app, relaunch
  4. Verify user stays logged in and returns to last relevant screen

#### SMK-03: Create minimal flow and save
- **Given:** Signed in as FREE-NEW; default moves available
- **When:** Create 2-node flow and tap Save
- **Then:** Flow is saved to library; visible in list; editable; counts toward saved flow cap
- **Steps:**
  1. From Library tap Create New Flow
  2. Add first move from default moves
  3. Add next move and connect
  4. Tap Save
  5. Return to Library and verify flow appears

#### SMK-04: Practice session completes and logs
- **Given:** Signed in as TRIAL or PRO; at least one saved flow with 2+ paths
- **When:** Start practice for at least 1 path and complete one set
- **Then:** Practice summary shows; session appears in history; streak updates if applicable
- **Steps:**
  1. Open a saved flow
  2. Tap Practice
  3. Select at least 1 path; start
  4. Let timer finish OR tap Completed Set
  5. End session; view summary; open History to verify log exists

---

### 35.5 Feature Suite: Authentication, Guest Mode, and Account Lifecycle

#### AUTH-01: Guest can explore but cannot save flows
- **Given:** Fresh install; not signed in
- **When:** Enter flow builder and attempt to save
- **Then:** User is blocked with clear CTA to create account; no local save occurs
- **Notes:** Decision lock: guests do not save flows even locally

#### AUTH-02: Guest restriction disclosure is shown upfront
- **Given:** Fresh install; not signed in
- **When:** Tap Try Without Account
- **Then:** App shows lightweight disclosure explaining guest limitations and benefits of account creation
- **Verify message includes:** cannot save flows, cannot personalize move list, cannot practice, and will lose work if you leave

#### AUTH-03: Email sign-up verification behavior
- **Given:** Device online; email not used before
- **When:** Sign up with email and follow verification flow
- **Then:** Account becomes active only after verification; unverified users are gated appropriately

#### AUTH-04: Logout returns to Welcome Gate and clears local session
- **Given:** Signed in as FREE-NEW
- **When:** User logs out from Settings
- **Then:** App returns to Welcome Gate; protected screens cannot be accessed via back navigation

---

### 35.6 Feature Suite: Navigation, Routing, and Screen Inventory

#### 35.6.1 Navigation Rules

**REQ-35.6.1:** Bottom tab selection persists across app restarts (if specified).

**REQ-35.6.2:** Modal screens dismiss to the exact previous context (not default home).

**REQ-35.6.3:** Deep links (share links) open to the correct viewer screen with correct gating.

**REQ-35.6.4:** Back button never bypasses gating (e.g., cannot return to editor after logout).

#### NAV-01: Every Screen Inventory route is reachable
- **Given:** Signed in as PRO
- **When:** Navigate to each screen at least once
- **Then:** Each screen renders without crash; header titles match; back works
- **Screens to test:** Settings, Move Library, Move Detail, Create Flow, Flow Detail, Practice Setup, Practice Active, History, Inbox, Paywall

---

### 35.7 Feature Suite: Moves (Default Library, Aliases, Custom Moves, Revert)

#### MOVE-01: Default move list is present for new user
- **Given:** Signed in as FREE-NEW
- **When:** Open move selector
- **Then:** Default moves are visible, searchable, filterable; user can select moves without creating any

#### MOVE-02: Aliases - Teep and Push Kick exist separately
- **Given:** Signed in; default move pack loaded
- **When:** Search for teep and push kick
- **Then:** Both appear as distinct canonical moves; selecting either inserts that exact label
- **Notes:** Locked decision: keep both as distinct canonical entries

#### MOVE-03: Create custom move with minimal fields
- **Given:** Signed in as FREE-NEW
- **When:** Create a custom move with just a name + tags
- **Then:** Move saves and is selectable in flow builder

#### MOVE-04: Revert behavior is scoped (move-only, not whole library)
- **Given:** Signed in as PRO; has edited a custom move
- **When:** Use revert controls
- **Then:** Only the selected move reverts; other moves unchanged

---

### 35.8 Feature Suite: Flow Builder (Core Interactions)

**Covers:** pan/zoom, sideways flow, adding nodes, branching up to 10, merges, dangling paths, replacing nodes, and reordering rules.

#### FLOW-01: Pan/Zoom works without breaking node selection
- **Given:** Signed in as PRO; open a complex flow (20+ nodes)
- **When:** Pan and zoom around canvas; then tap nodes and edges
- **Then:** Canvas pans/zooms smoothly; node taps still open correct menus; no accidental drags

#### FLOW-02: Branching limit - up to 10 outgoing branches
- **Owner:** CH12 + CH30
- **Given:** Signed in; open flow builder
- **When:** Attempt to create 11th branch from same move
- **Then:** App blocks 11th branch with warning; first 10 persist

#### FLOW-03: Merges allow multiple incoming paths
- **Given:** Signed in; in flow builder
- **When:** Create two different paths that merge into a single move node
- **Then:** Node displays as shared destination; incoming edges preserved; no data loss

#### FLOW-04: Dangling paths allowed
- **Given:** Signed in; in flow builder
- **When:** Delete a downstream node leaving an upstream node without continuation
- **Then:** Flow remains valid; visually indicates terminal end; no crash
- **Notes:** User confirmed dangling paths are OK

---

### 35.9 Feature Suite: Sequence Detail Editor

**REQ-35.9.1:** Validates optional sequence details between moves (the 'how you got from A to B' layer). Sequences are not required.

#### SEQ-01: Sequence detail opens from edge control
- **Given:** Signed in; flow has edge A->B
- **When:** Tap the edge/sequence control
- **Then:** Sequence Detail Editor opens; saves and persists

#### SEQ-02: Sequence details are optional and do not block practice
- **Given:** Signed in; edges without details exist
- **When:** Start practice on a path using edges with no details
- **Then:** Practice proceeds normally; optional details hidden/blank per spec

---

### 35.10 Feature Suite: Library, Sharing, Inbox, Imports

#### LIB-01: Flow-only search (V1)
- **Given:** Signed in; user has at least 5 flows
- **When:** Search by flow name
- **Then:** Results update; move search is not present in this UI

#### SHARE-01: Create unlisted share link and revoke it
- **Given:** Signed in as PRO; flow exists
- **When:** Create unlisted link then revoke
- **Then:** Link opens viewer before revoke; after revoke shows revoked UX

#### INBOX-01: Free inbox cap = 10; view-only; cannot practice
- **Given:** Signed in as FREE-LIMITED; inbox has 10 items
- **When:** Receive/import another flow link
- **Then:** App blocks adding 11th item; user can still view existing; cannot practice inbox flows

#### IMPORT-01: Import conflict resolution choices
- **Given:** Signed in as PRO; receive flow with sender custom move details
- **When:** Accept import and choose handling option
- **Then:** Receiver is prompted to keep details flow-only OR add as separate move; changes reversible

---

### 35.11 Feature Suite: Practice Mode (Setup, Active, Logging, Credits)

**REQ-35.11.1:** Practice is paywalled; Free receives 3 monthly practice credits usable only on saved flows (not inbox). Practice completes via timer end or Completed button. Actual duration is logged.

#### PRAC-01: Practice setup - select paths across flows + reorder
- **Given:** Signed in as PRO; has 2 flows with multiple paths
- **When:** Choose paths from multiple flows and reorder them
- **Then:** Order persists into active session; user can set timer and assumed reps

#### PRAC-02: Early end behavior (Completed Set)
- **Given:** Signed in as PRO; active session running
- **When:** Tap Completed Set before timer ends
- **Then:** Set ends immediately; logs show actual duration

#### PRAC-03: Free credits apply only to saved flows (not inbox)
- **Given:** Signed in as FREE-NEW; has 1 saved flow, 1 inbox flow, and 1+ credit
- **When:** Attempt practice on saved vs inbox
- **Then:** Saved flow consumes credit; inbox remains blocked

#### PRAC-04: Interrupted session saves as interrupted
- **Given:** Signed in as PRO; active session
- **When:** End practice before finishing all paths
- **Then:** Session saved as Interrupted with partial progress

---

### 35.12 Feature Suite: Monetization (Trial, Paywalls, Restore)

**REQ-35.12.1:** Validates plan gating, trial behavior, upgrade/downgrade flows, and messaging. Pricing target: $9.99/month with 7-day trial.

#### PAY-01: Paywall triggers at correct moment (Practice)
- **Given:** Signed in as FREE-NEW with 0 credits
- **When:** Attempt to start practice on a saved flow
- **Then:** Paywall appears; no session starts; user can dismiss safely

#### PAY-02: Trial behaves as Pro; restore purchases works
- **Given:** Signed in as FREE-NEW eligible for trial
- **When:** Start trial then use Pro-only feature
- **Then:** Practice works without credits; uploads available; restore works after reinstall

---

### 35.13 Feature Suite: Offline, Storage, Safety/Abuse

#### OFF-01: Offline - view saved flows; editing draft/sync behavior
- **Given:** Signed in as PRO; saved flow cached; device in airplane mode
- **When:** Open flow, make edits, attempt save
- **Then:** App either queues draft sync or blocks with clear message; no silent data loss

#### STOR-01: Uploads private-only; links shareable
- **Given:** Signed in as PRO; flow has uploaded video on one move and external link on another
- **When:** Share flow to another user/device
- **Then:** Receiver can view link-based media; uploaded media marked unavailable/not shared

#### SAFE-01: Warning ladder triggers for caps/limits
- **Given:** Signed in as FREE-LIMITED; at caps
- **When:** Attempt actions beyond caps
- **Then:** Soft warnings then hard block with upgrade CTA; messaging consistent

---

### 35.14 Feature Suite: Export, Account Deletion, Accessibility, Analytics

#### EXP-01: Export flows and logs succeeds and matches data model
- **Given:** Signed in as PRO; has 2 saved flows and 2 logs
- **When:** Export data
- **Then:** Export succeeds; includes expected objects/fields; no missing required fields

#### DEL-01: Account deletion path works
- **Given:** Signed in
- **When:** Initiate account deletion
- **Then:** App confirms; deletes; returns to Welcome; cannot sign in again

#### A11Y-01: VoiceOver labels and focus order on key loop
- **Given:** VoiceOver ON
- **When:** Navigate welcome -> create flow -> practice -> summary
- **Then:** All interactive elements labeled; focus order logical

#### ANL-01: Core analytics events fire once with correct properties
- **Given:** Analytics visible in debug logs; signed in as Free
- **When:** Perform core actions
- **Then:** Events recorded once; include plan state; exclude sensitive content

---

### 35.15 Performance, Stability, and Security Test Suites

#### 35.15.1 Performance Stress Tests

**PERF-01:** Large flow rendering
- Load flow with 100 nodes and 150 edges; pan/zoom; no freezes > 250ms during interaction
- Open/close canvas 20 times; no progressive slowdown or memory leak symptoms

**PERF-02:** Practice timer stability
- Run 30-minute session; timer drift minimal; background/foreground does not corrupt state
- Lock screen 2 minutes; return; session state correct

**PERF-03:** Lists and search
- Library with 200 flows: search responds quickly after typing stops
- Move selector with 500+ moves: scrolling remains smooth

#### 35.15.2 Security & Abuse Tests

**SEC-01:** Share token safety
- Revoked tokens never resolve content
- Spot-check rate limiting / generic errors for invalid tokens

**SEC-02:** Media privacy
- Uploaded media never accessible to non-owner (P0 if violated)
- External links displayed safely (no auto-open)

**SEC-03:** Entitlement bypass attempts
- Free cannot start practice via deep link or hidden route
- Guest cannot access saved-flow editor via back stack

---

### 35.16 Bug Severity, Triage, and Release Gates

#### Severity Definitions

| Severity | Definition | Release Impact |
|----------|------------|----------------|
| P0 (Blocker) | Crash, data loss, security/privacy breach, purchase failure, cannot sign in, cannot save core content | Must fix before submission |
| P1 (Critical) | Core loop broken for a major plan state; severe performance issues | Must fix before submission |
| P2 (Major) | Important feature glitch with workaround; inconsistent copy; minor data mismatch | Fix preferred; ship only with sign-off |
| P3 (Minor) | Cosmetic issues, typos, minor layout spacing | May ship; batch for next revision |

#### 35.16.1 Release Gates (Checklist)

**Gate A - Smoke Suite:**
- All SMK-* cases pass on Standard and Large devices

**Gate B - Core Authoring:**
- Create/edit/save flows works on Free and Pro
- Branching up to 10 works; merges work; dangling paths do not crash; sequences optional

**Gate C - Practice & Logging:**
- Practice sessions complete; early end works; interrupted logs correct
- Free credits gating works; inbox practice blocked for Free

**Gate D - Monetization:**
- Trial start works; restore works; paywall copy accurate

**Gate E - Safety & Compliance:**
- Warning ladder triggers correctly; no privacy leaks via sharing
- Export and account deletion work

**Gate F - Accessibility Minimum:**
- VoiceOver labels present on primary CTAs; Dynamic Type does not break key screens

**Gate G - No Open P0/P1:**
- P0 = 0; P1 = 0; P2 only with explicit sign-off

---

### 35.17 Replit Build Prompt for This Chapter (CH35)

```
You are implementing HZ-V1-CH35 (QA Acceptance Tests) for the Handz V1 PRD Bundle.

Goal: Create a complete QA test suite artifact set (documents + optional automation scaffolding) that matches CH35 exactly.

Inputs: CH00 + CH35. Treat cross-references to other chapters as dependencies.

Deliverables:
1) A 'QA' folder containing:
- smoke_suite.md (SMK-01..SMK-04)
- feature_suites.md (AUTH/NAV/MOVE/FLOW/SEQ/LIB/SHARE/INBOX/IMPORT/PRAC/PAY/OFF/STOR/SAFE/EXP/DEL/A11Y/ANL)
- regression_checklist.md (Release Gates A-G)
- bug_report_template.md (Owner chapter, severity P0-P3, expected vs actual, repro steps)
2) Optional: automation scaffolding placeholders with TODOs referencing test case IDs.

Rules:
- Do not change product behavior; only encode the tests.
- Every test must include Preconditions, Steps, Expected Result, and Owner chapter reference.
- If any test requires a missing product rule, add a PRD_PLACEHOLDER block and stop.

Output: Commit the QA folder with commit message: 'Add CH35 QA acceptance tests'.
```

---

### 35.18 Troubleshooting Notes for QA (CH35)

**Flow Builder feels laggy or taps misfire:**
- Check gesture handler precedence (pan/zoom vs drag)
- Check node re-render frequency; ensure state updates don't re-render entire canvas

**Practice timer drift or incorrect completion:**
- Verify background/foreground handling; ensure timer logs actual duration
- Check early-complete path (Completed Set) updates both UI and log correctly

**Free user can practice inbox item:**
- Verify entitlement checks include BOTH plan state and item source (saved vs inbox)
- Ensure deep links cannot bypass gating

**Share link shows private uploads:**
- Treat as P0 privacy bug
- Uploads must never resolve for non-owner; links are the only shareable media
- Check storage ACLs and API responses for viewer endpoints

**Restore purchases inconsistent:**
- Confirm restore updates entitlements immediately; clear cached plan state; re-fetch receipt if needed

---

## CH36: Troubleshooting Playbook

**Doc ID:** HZ-V1-CH36_Troubleshooting_Playbook_R1  
**Revision:** R1 (2026-01-02)  
**Status:** Draft  
**Depends on:** CH00 Master Index (anchor), CH31 Error States (copy + severity), CH35 QA Acceptance Tests (test language + coverage)  
**Related:** CH07, CH08, CH09-11, CH12-14, CH15-16, CH17-19, CH20-22, CH23-24, CH27, CH28, CH29, CH30, CH33, CH34

### Owned Decisions
- Debugging and troubleshooting workflow
- Required diagnostic signals
- Canonical triage ladder for V1

### Open Questions / Placeholders
- PLACEHOLDER: Exact analytics vendor (Owner: CH33)
- PLACEHOLDER: Crash reporting vendor (Owner: CH33)
- PLACEHOLDER: In-app dev diagnostics entry gesture (Owner: CH04/CH31)

---

### 36.1 Purpose

**REQ-36.1.1:** This chapter is the operational playbook for diagnosing, reproducing, and fixing issues in Handz V1. Written so a builder (including a vibe-coding agent) can identify root cause without guessing, and can confirm fix with repeatable regression checks.

**REQ-36.1.2:** Non-negotiable rule: troubleshoot the product rule first, then the code.

**REQ-36.1.3:** If you find a contradiction between app behavior and the PRD bundle, fix the owning chapter (new revision) before patching implementation.

---

### 36.2 How to Use This Playbook

**REQ-36.2.1:** Troubleshooting workflow:
- **Step 0 - Capture context:** device, iOS version, app version/build, account state (Guest/Free/Trial/Pro), network state (online/offline/poor), and exact screen route
- **Step 1 - Reproduce:** attempt to reproduce in smallest possible scenario
- **Step 2 - Classify:** crash, hard blocker, soft blocker, data integrity issue, paywall/entitlement mismatch, sync conflict, or UX/copy mismatch (see CH31)
- **Step 3 - Localize:** decide which subsystem owns it (Auth, Moves, Flow Builder, Sharing, Inbox, Practice, Entitlements, Offline/Sync, Storage/Uploads, Notifications, Analytics)
- **Step 4 - Diagnose with signals:** use diagnostic checklist for that subsystem
- **Step 5 - Fix and prevent:** apply fix that also prevents recurrence
- **Step 6 - Regression:** run regression checklist for that subsystem and add/extend tests in CH35

---

### 36.3 Bug Report Template

**REQ-36.3.1:** Use this exact template when reporting issues:

```
Title: [short symptom] (e.g., "Practice timer ends early when phone locks")
Severity: Crash | Blocker | Major | Minor | Cosmetic (see CH31)
Account state: Guest | Free | Trial | Pro
Device/iOS: [model], iOS [version]
App build: [version], [build number], [TestFlight/App Store/dev]
Network: Offline | Poor | Normal
Route/screen: [tab/screen], starting from launch
Preconditions: [what must already exist? e.g., flow with 10 branches, 2 saved flows used]
Steps to reproduce:
1) ...
2) ...
Expected: ...
Actual: ...
Frequency: Always | Often | Sometimes | Once
Logs/screenshots: [attach]
Data impact: None | Lost draft | Duplicated items | Wrong entitlement | Wrong practice log
Workaround: [if any]
```

---

### 36.4 Triage Ladder (What to Check First)

**REQ-36.4.1:** Priority order:
1. **Crashes and data loss first.** If users can lose flows, practice logs, or moves, treat as highest priority
2. **Entitlement mismatches second.** If Pro/Trial features behave like Free (or vice versa), fix gating before anything else (see CH08/CH25)
3. **Sync integrity third.** If offline edits overwrite newer changes, fix conflict rules (see CH28)
4. **UX and copy last.** Cosmetic issues can wait, but must still be fixed before App Store submission

---

### 36.5 Required Diagnostics in V1 Builds

**REQ-36.5.1:** Handz V1 must ship with enough diagnostics to troubleshoot real user issues without needing private developer tools. This does NOT mean exposing sensitive data to users.

#### 36.5.1 Minimum Diagnostics (Must-Have)

**REQ-36.5.2:** Global error boundary: catches unhandled UI exceptions and shows recovery UI with 'Report issue' action (See: CH31)

**REQ-36.5.3:** Network request logging: in dev builds, log request path, status, latency, and error type. In prod, log only aggregate/anonymous metrics unless user consents

**REQ-36.5.4:** Local persistence audit: ability to show whether a record is 'local-only', 'synced', or 'sync-failed' (See: CH28)

**REQ-36.5.5:** Entitlement snapshot: single struct showing current plan state (Guest/Free/Trial/Pro), trial expiry, and feature gates currently active (See: CH08)

**REQ-36.5.6:** Storage snapshot: current local media usage and server media usage vs the 2GB cap; link vs upload distinction (See: CH29)

**REQ-36.5.7:** Practice snapshot: current practice credit balance for Free, and whether current flow is 'saved' vs 'inbox' (See: CH20)

**REQ-36.5.8:** Share-link snapshot: number of share links created today, and any throttle/abuse flags (See: CH17/CH30)

**REQ-36.5.9:** Bug report export: one-tap 'Copy diagnostic bundle' that copies sanitized text block into clipboard, for user to paste into support (no secrets)

#### 36.5.2 Diagnostics Must NOT Include

**REQ-36.5.10:** Diagnostics must NOT include:
- Authentication tokens, raw payment receipts, or personally identifying info beyond user ID/handle (unless explicitly consented)
- Private move notes or private video contents in bug report unless user explicitly includes them

---

### 36.6 Golden Rule for Troubleshooting in Production

**REQ-36.6.1:** If you cannot reproduce an issue locally, treat the user's report as a data point and improve observability: add a signal, log, or state snapshot so the next occurrence becomes diagnosable. Do not guess.

---

### 36.7 Common Failure Modes and Quick Routing

**REQ-36.7.1:** Use this as a 'where do I look' index:

| Symptom | Section | Chapter Reference |
|---------|---------|------------------|
| App won't launch / crashes on open | S1 (Crash and Launch) | - |
| Sign-in doesn't work, stuck on onboarding, can't save | S2 (Auth and Account) | CH07 |
| Free/Trial/Pro gates behave wrong, practice blocked unexpectedly | S3 (Entitlements and Paywalls) | CH08/CH25 |
| Moves missing, duplicates, aliases wrong (teep vs push kick) | S4 (Moves System) | CH09-11 |
| Flow builder glitches, can't connect nodes, pan/zoom broken, performance | S5 (Flow Builder) | CH12-14 |
| Share link fails, receiver can't open, revoke doesn't work | S6 (Sharing) | CH17 |
| Inbox imports don't show, cap wrong, save-to-library conflicts | S7 (Inbox and Import Conflicts) | CH18-19 |
| Practice timer wrong, history wrong, streak wrong | S8 (Practice) | CH20-22 |
| Offline edits disappear or overwrite | S9 (Offline and Sync) | CH28 |
| Uploads exceed cap, links vs uploads confusion, can't share videos | S10 (Storage and Media) | CH29 |
| Notifications not firing / wrong time zone | S11 (Notifications) | CH27 |
| Warnings/anti-abuse ladder misfires | S12 (Safety/Abuse) | CH30 |
| Export/deletion broken | S13 (Export and Deletion) | CH34 |

---

### 36.8 Subsystem Troubleshooting Guides

#### S1. Crash and Launch

**Symptoms:**
- App crashes immediately after tapping the icon
- App opens to blank screen or infinite spinner
- Crash occurs only after updating from older build
- Crash occurs only for accounts with many flows/moves
- App becomes unresponsive during heavy canvas interactions

**Diagnosis Checklist:**
- Confirm build type: dev vs TestFlight vs App Store
- Check crash reporting console for stack trace, crash frequency, affected OS versions
- Attempt cold start, then warm start
- If crash after update: verify local migrations ran successfully
- If crash only with large datasets: reproduce with synthetic 'large' user
- Confirm network is not required for first paint (See: CH28)
- Check for infinite loops: repeated navigation redirects, repeated auth state toggles

**Likely Fixes:**
- Add global error boundary around root navigator
- Guard JSON parsing and migrations with try/catch
- Make launch path idempotent
- Defer heavy work: do not hydrate entire flow canvas at launch
- Add timeouts for network-dependent calls

**Prevention/Hardening:**
- Add lightweight 'safe mode' path if app crashes twice in a row
- Add schema versioning for local caches
- Add performance budgets: max nodes rendered in thumbnails
- Add automated smoke test in CH35

---

#### S2. Auth and Account (Guest, Free, Pro)

**Symptoms:**
- User cannot sign up with Google or email
- User signs up but gets stuck on verification
- Guest user starts building flow but later learns they cannot save
- After login, user data is missing
- Log out/log in cycles cause duplicate demo flows

**Diagnosis Checklist:**
- Confirm exact auth method used
- Confirm whether user is in Guest mode or has authenticated user ID
- Check for route loops
- Verify account conversion flows
- Check uniqueness rules for username/handle
- Test bad networks

**Likely Fixes:**
- Make auth state the single source of truth
- Add clear preflight messaging for Guest users
- Ensure Guest mode cannot create persistent local flow records
- Ensure demo content is not duplicated
- When auth fails, show CH31 error state with retry action

---

#### S3. Entitlements, Paywalls, Trials, and Feature Gating

**Symptoms:**
- Pro user is treated as Free
- Free user can practice without credits
- Trial started but does not unlock Pro gates
- Restore purchases does nothing
- Paywall triggers too aggressively

**Diagnosis Checklist:**
- Capture entitlement snapshot
- Verify feature gate evaluation order: Guest gate -> Free caps -> Trial/Pro overrides
- Check device time vs server time
- Verify practice credits apply only to saved flows and NOT to inbox items
- Simulate edge cases: user upgrades during active practice session
- Confirm paywall copy matches actual gate (CH31)

**Likely Fixes:**
- Centralize gates in one module
- Add 'refresh entitlements' action
- Implement restore purchases correctly
- Fix gate messaging order
- Enforce credit decrement atomically

---

#### S4. Moves System (Defaults, Aliases, Families, Variants, Customization)

**Symptoms:**
- Default moves missing after onboarding or reinstall
- User sees duplicate moves unexpectedly
- Alias confusion: selecting 'Front Kick' shows as 'Teep'
- Edits to a move unexpectedly change flows that used that move
- Shared flows bring in custom moves and cause conflicts

**Diagnosis Checklist:**
- Confirm whether user is on default move pack or custom-selected pack
- Inspect move identifiers: each move must have stable canonical ID
- Check whether duplicates share same canonical ID (bug) or are intentionally separate
- Verify family/variant metadata
- Test edit scope: library-wide or flow-local
- For shared imports: confirm conflict resolution UI appears

**Likely Fixes:**
- Enforce canonical ID uniqueness
- Treat aliases as separate 'display names' linked to canonical move
- Implement clear scope choice for edits
- Add 'View move family' panel
- For imports: provide three safe options when conflicts occur

---

#### S5. Flow Builder (Canvas, Nodes, Branches, Merges, Reorder)

**Symptoms:**
- Cannot connect a move to another move
- Branches disappear, or connecting new branch overwrites existing one
- Merge node drops some incoming paths
- Reordering fails
- Pan/zoom jittery; canvas resets position
- Large flows become slow, crash, or freeze

**Diagnosis Checklist:**
- Confirm layout mode (top-to-bottom vs left-to-right) and zoom level
- Check node identity: each node needs stable node ID separate from move canonical ID
- Verify edge model: edges must allow 1-to-many outgoing and many-to-1 incoming
- Check 'max 10 branches' enforcement
- Test optional sequence nodes
- For reorder: confirm whether reorder affects only visual layout or actual execution order
- Check persistence

**Likely Fixes:**
- Separate model from view
- Add validation before save
- Implement deterministic ordering for outgoing branches
- Debounce saves and make them idempotent
- For performance: virtualize rendering, collapse subtrees
- Provide 'zoom to fit' and 'center on selected' action

**Addendum - Clarifying Terms:**
- **Dangling path:** node with outgoing connector with no next node attached yet
- **Summary node:** special non-move node that collapses set of outgoing branches into single labeled hub (optional in V1; if implemented, must be purely a view feature)

---

#### S6. Sharing (Unlisted Links, Duplicate Flow, Revoke)

**Symptoms:**
- Share link opens to error or blank screen
- Receiver opens link but sees missing moves or broken paths
- Revoke link does not invalidate it
- User hits daily share link limit unexpectedly
- Shared flow includes uploaded videos that receiver cannot access

**Diagnosis Checklist:**
- Confirm whether sender shared flow or gameplan
- Check whether sender included custom moves or custom move notes
- Verify shared object is immutable (snapshot at share time)
- If receiver sees missing media: check whether sender used uploads vs links
- Inspect rate limits
- If revoke fails: verify share link token is checked server-side

**Likely Fixes:**
- Ensure share links resolve server-side to share record with active flag
- Implement import preflight on open
- If receiver lacks some moves, map by canonical ID or alias mapping rules
- Add explicit share messaging about private uploads
- Make 'Duplicate into my library' idempotent

---

#### S7. Inbox and Import Conflicts (Free Inbox Cap, Save-to-Library)

**Symptoms:**
- Imported flow does not appear in inbox after opening share link
- Inbox shows more than Free cap (should be 10) or blocks too early
- Free user cannot save imported flow even when they have capacity
- Saving imported flow overwrites existing moves or notes silently
- Inbox item becomes corrupted after sender updates their content

**Diagnosis Checklist:**
- Confirm whether user is Free/Pro and whether inbox cap rules are applied
- Verify inbox items are immutable snapshots at import time
- Check save-to-library flow runs import conflict UI
- Confirm Free users can view inbox items but cannot practice them
- Check for duplicate imports

**Likely Fixes:**
- Enforce inbox caps at insert time
- Implement idempotent import by share-link token
- When Free user is at max saved flows, saving from inbox must show upgrade prompt
- Implement safe conflict resolution: never silently overwrite
- Store imported payloads with schema version

---

#### S8. Practice Mode (Drills, Timer, Credits, Interruptions, History)

**Symptoms:**
- Practice session cannot start even though user should be eligible
- Timer ends early, pauses unexpectedly, or continues while app is backgrounded
- Practice history logs wrong duration, wrong path order, or wrong status
- User selected paths but drill uses different paths
- Credits decrement incorrectly

**Diagnosis Checklist:**
- Confirm eligibility: account type, credit balance, flow saved vs inbox
- Confirm practice configuration: selected paths list, order, timer per path, assumed reps
- Test background behavior
- Check session state machine: NotStarted -> Running -> Paused -> Completed/Interrupted
- Verify idempotent session IDs
- Confirm actual duration logging

**Likely Fixes:**
- Follow fitness tracker convention for interrupted sessions
- If app goes to background, choose consistent rule (pause vs continue)
- Make credit decrement atomic and tied to session lifecycle
- Ensure path selection order is explicit and persisted
- Add clear recovery UI if session fails to start

---

#### S9. Offline and Sync (Drafts, Conflicts, Retries)

**Symptoms:**
- Edits made offline disappear after reconnecting
- Older data overwrites newer data from another device
- Flow shows 'syncing' forever
- User cannot open flow while offline even though it was previously viewed
- Duplicate flows appear after reconnecting

**Diagnosis Checklist:**
- Confirm user's network state at edit time and at sync time
- Check record sync metadata: lastModifiedAt, lastSyncedAt, localDirty flag, conflict flag
- Confirm conflict policy
- Check whether media uploads were queued offline
- Confirm retries: exponential backoff, manual 'retry sync' action

**Likely Fixes:**
- Implement per-record sync states with clear UI: Synced, Pending, Failed, Conflict
- For flow graphs, avoid silent merges; use conflict UI
- Separate critical and non-critical sync
- Make create operations idempotent

---

#### S10. Storage and Media (Links vs Uploads, 2GB Cap, Sharing Rules)

**Symptoms:**
- User cannot upload video even though they are under cap
- Upload succeeds but later disappears or fails to play
- Storage meter incorrect
- User shares flow and expects uploaded videos to transfer
- Video thumbnails cause slowdowns

**Diagnosis Checklist:**
- Confirm user's storage usage: local cache, remote usage, 2GB cap
- Check whether upload failed due to file type, size, network timeout, or permission denial
- Confirm whether media is attached as link or upload
- Verify media access rules
- Inspect caching

**Likely Fixes:**
- Implement clear upload error messages
- Make storage meter authoritative
- On share: show warning if flow contains uploads
- Lazy-load thumbnails and paginate media lists

---

#### S11. Notifications and Scheduling (Local Time, Reminders)

**Symptoms:**
- Notifications never arrive even after enabling
- Notifications arrive at wrong time (time zone drift)
- User changes reminder time but old reminders still fire
- Permission prompt never shows or shows repeatedly
- Streak/consistency metrics do not match reminders

**Diagnosis Checklist:**
- Confirm notification permission status
- Confirm device time zone and whether app uses local time for scheduling
- Check whether reminders are scheduled as repeating events
- If reminders depend on server state, confirm plan generation and local scheduling are in sync
- Test with device time changes

**Likely Fixes:**
- Always provide 'Test notification' action in settings
- When user edits reminder, cancel previous scheduled notifications
- Use local time scheduling with clear UI
- If permissions denied, show in-app card explaining how to enable

---

#### S12. Safety, Abuse, and Warning Ladder (Soft Caps)

**Symptoms:**
- User is blocked from creating branches or share links even though behavior is normal
- Warning ladder messages show too early, too often, or with confusing copy
- Abusive behavior is not detected
- Legitimate users doing 2-hour planning session are throttled incorrectly

**Diagnosis Checklist:**
- Confirm which rule triggered: branch soft cap, share link cap, import inbox cap, upload cap, or suspicious behavior flag
- Inspect counters: per-day counts, per-hour bursts, rolling windows
- Check whether user is Pro/Trial vs Free
- Review warning ladder state
- Check copy source matches CH31 severity

**Likely Fixes:**
- Implement warning ladder with memory
- Prefer frictionless soft caps
- Provide escape hatch for legitimate users
- Tune thresholds

---

#### S13. Export, Data Portability, and Account Deletion

**Symptoms:**
- Export produces empty file or missing branches
- Export includes private uploads unexpectedly (privacy bug)
- Account deletion leaves data behind or breaks re-registration
- User requests copy of data but export is incomplete

**Diagnosis Checklist:**
- Confirm export type
- Verify exports map canonical IDs correctly and include schema version
- Check privacy rules
- For deletion: confirm soft-delete vs hard-delete policy

**Likely Fixes:**
- Implement export serialization from canonical graph model
- Add export validation: re-import in sandbox and compare graphs
- For deletion: revoke share links, delete uploads, remove user data references
- Provide clear user messaging for deletion

---

#### S14. App Store Readiness (Paywall Copy, Scientific Claims, Compliance)

**Symptoms:**
- App Store reviewer flags misleading claims
- Subscription terms unclear or missing restore purchases
- Paywall shown before user can understand value
- Privacy labels mismatch

**Diagnosis Checklist:**
- Audit all in-app marketing copy, especially any 'scientific leverage' claims
- Confirm paywall contains: price, billing period, trial length, auto-renew statement, restore purchases link
- Verify upgrade path is consistent
- Confirm privacy settings

**Likely Fixes:**
- Use cautious language
- Add in-app 'Methodology' page
- Ensure restore purchases works and is discoverable
- Delay hard upsell until after user experiences core concept

---

### 36.9 Vibe-Coding Troubleshooting Workflow (Replit Agent Prompts)

**Prompt T1 - Diagnose a bug before coding:**
```
You are implementing Handz V1. Before changing code, follow this checklist:
1) Restate the product rule from the PRD that governs this behavior (cite the chapter/section).
2) Restate the reported bug using the Bug Report Template (CH36).
3) Produce a minimal reproduction plan (smallest dataset and steps).
4) List the top 3 likely root causes and what evidence would confirm each.
5) Add or identify the diagnostic signals needed (logs/state snapshots).
Only after steps 1-5, propose the smallest safe code change.
Then list the regression checklist you will run (CH36 + CH35).
```

**Prompt T2 - Fix with guardrails:**
```
Implement the fix with these constraints:
- Do NOT change product behavior unless the PRD is updated first.
- Centralize the rule in the correct module (gating, graph model, import resolver, etc.).
- Add a unit/integration test for the bug (CH35 style).
- Add/extend an error state if the failure can happen again (CH31).
- Add a sanitized diagnostic snapshot for this subsystem (CH36 'Minimum diagnostics').
Output: (a) what changed, (b) why it fixes root cause, (c) what tests you ran, (d) any new edge cases created.
```

**Prompt T3 - Create a diagnostic bundle copy:**
```
Add a 'Copy Diagnostic Bundle' action in Settings that copies a sanitized text block:
- App version/build
- Device/iOS
- Account state (Guest/Free/Trial/Pro)
- Entitlement snapshot
- Storage snapshot (local/remote usage vs 2GB cap)
- Practice snapshot (credits for Free, flow origin saved vs inbox)
- Last 20 app logs (sanitized, no tokens)
- Current route/screen
Ensure privacy: no personal text notes, no upload URLs, no auth tokens.
```

---

### 36.10 Acceptance Criteria for CH36

**AC36-1:** Bug report template exists and is used consistently by the team/agent.

**AC36-2:** App implements minimum diagnostics: error boundary, entitlement snapshot, storage snapshot, practice snapshot, share-link snapshot, and copy diagnostic bundle.

**AC36-3:** For each critical subsystem (Auth, Entitlements, Flow Builder, Sharing, Inbox, Practice, Offline/Sync), the playbook includes symptoms, diagnosis, fixes, prevention, and regression checks.

**AC36-4:** Regression checklists are mirrored into CH35 test cases before App Store submission.

**AC36-5:** No diagnostic bundle includes secrets or private user content without consent.

---

## Cross-Chapter References (This Batch)

| Reference | Description |
|-----------|-------------|
| CH02 | Portrait-only constraint |
| CH04/CH05 | Screen inventory, navigation |
| CH07 | Authentication |
| CH08 | Entitlements, plan states, gating |
| CH09-11 | Moves system |
| CH12-14 | Flow Builder |
| CH15-16 | Library |
| CH17 | Sharing |
| CH18-19 | Inbox, Import conflicts |
| CH20-22 | Practice mode |
| CH23-24 | Mastery/Maintenance |
| CH25 | Paywall |
| CH27 | Notifications |
| CH28 | Offline & Sync |
| CH29 | Storage & Limits |
| CH30 | Safety/Abuse |
| CH31 | Error States |
| CH32 | Localization (placeholder) |
| CH33 | Analytics |
| CH34 | Export/Deletion |

---

## Assumptions (Preserved)

1. V1 ships iOS-only, portrait-only
2. Free plan limits: 2 saved flows, 10 inbox items, 3 monthly practice credits
3. Pricing target: $9.99/month with 7-day trial
4. "Teep" and "Push Kick" remain distinct canonical moves (locked decision)
5. Guests cannot save flows even locally (locked decision)
6. Dangling paths are allowed (user confirmed)
7. Uploads are private-only; only links are shareable
8. 2GB storage cap for uploads
9. Maximum 10 outgoing branches per node

## Placeholders (Preserved)

1. PLACEHOLDER: Automation tooling choice (Detox/XCUITest/etc.)
2. PLACEHOLDER: Exact device matrix (final iOS versions/devices)
3. PLACEHOLDER: Final default values (unspecified)
4. PLACEHOLDER: Exact analytics vendor (Owner: CH33)
5. PLACEHOLDER: Crash reporting vendor (Owner: CH33)
6. PLACEHOLDER: In-app dev diagnostics entry gesture (Owner: CH04/CH31)
7. PLACEHOLDER: Non-English locale for spot-check (See: CH32)
8. PLACEHOLDER: Soft-delete vs hard-delete policy for account deletion

## Edge Cases (Preserved)

1. User upgrades during active practice session
2. User downgrades from Pro to Free
3. User reinstalls and restores purchases
4. User changes Apple ID
5. Offline edits on two devices then reconnect (conflict)
6. Import flow with custom move not present in receiver library
7. Import flow with standard move but different sender notes
8. Device time manipulation (trial expiry, credit refresh)
9. DST boundary and time zone changes for notifications
10. Crash after update from older build (migration failures)
11. Large datasets (hundreds of moves, flows with many nodes)

---

**Batch 10A Complete. Awaiting approval to proceed.**

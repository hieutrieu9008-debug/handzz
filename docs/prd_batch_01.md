# PRD Batch 01: Foundation & Scope (CH00-CH04)

> **Batch processed:** 2026-01-05  
> **Chapters:** 5  
> **Status:** Complete

---

## CH00: Master Index & Manifest

**Overview:**

CH00 serves as the anchor document for the entire Handz V1 PRD bundle (Bundle ID: HZ-V1). It establishes non-negotiable rules for maintaining coherence across all 38 chapters, including stable IDs, mandatory cross-references, required front-matter, and revision control. Every chapter must reference CH00 for global locks and placeholders. The document provides a complete chapter map (CH01-CH38), decision log, placeholder registry, and workflow guidance for multi-chat PDF generation.

**Key Requirements:**

- **Document Structure & Standards:**
  - File naming: `HZ-V1-CH##_<Short_Title>_R#.pdf` (3-7 word Title Case with underscores)
  - Required front-matter: Doc ID, Revision, Status, Depends on, Related, Supersedes, Owned Decisions, Open Questions/Placeholders
  - Cross-reference format: `See: HZ-V1-CH12 §3.4 (Branch Limits)`
  - One chapter per PDF; stable chapter numbers; revisions increment (R1 → R2 → R3)
  - No detail loss; chapters can be long; do not summarize away edge cases or states

- **Global Locks (Non-Negotiable Until Revised in CH00):**
  - Platform: iOS-only for V1; portrait-only
  - Plan states: Guest / Free / Pro / Trial (Trial behaves as Pro)
  - Practice paywall: Practice is paywalled; Free receives 3 monthly practice credits usable only on saved flows
  - Saved flows cap (Free): 2 saved flows
  - Inbox cap (Free): 10 items; Free can view but cannot practice inbox items
  - Branch limit: up to 10 outgoing branches from a move
  - Uploads: Pro only; 2GB total cap; private-only; not shared; links are shareable
  - Pricing direction: $9.99/month with 7-day trial (subject to later market check)

- **Chapter Map (38 Chapters Total):**
  - Foundation & Scope: CH01-CH04
  - Design & Auth: CH05-CH08
  - Move Library System: CH09-CH11
  - Flow Builder: CH12-CH16
  - Sharing & Collaboration: CH17-CH19
  - Practice Mode: CH20-CH22
  - Progress & Monetization: CH23-CH26
  - System Features: CH27-CH32
  - Analytics & Data Management: CH33-CH34
  - QA & Deployment: CH35-CH38

- **Workflow & Governance:**
  - Branching workflow: Each new chat must paste CH00 rules and generate only one chapter PDF
  - Acceptance standard: Every chapter includes Given/When/Then acceptance tests
  - Conflict resolution: Update owning chapter and bump revision; do not patch code around spec conflicts
  - Replit/Vibe-Coding: Always attach CH00 + implementing chapter; treat cross-references as dependencies

**Open Questions/Ambiguities:**

- PLACEHOLDER: Exact node cap per plan (if keeping node caps) - owner TBD
- PLACEHOLDER: Exact default-move list finalization (research-driven) - owner CH09/CH10
- PLACEHOLDER: Exact share link caps for Pro (soft/hard) and anti-abuse thresholds - owner CH17/CH30
- PLACEHOLDER: Exact notification defaults (off/on; frequency) - owner CH27
- PLACEHOLDER: Exact analytics event list + privacy language - owner CH33
- PLACEHOLDER: Scientific-claims copy and which studies (must be cited; results-may-vary language) - owner CH26

**Cross-References:**

- All 38 chapters depend on CH00 for global locks, naming standards, and placeholder registry
- CH00 §6 (Placeholder Registry) lists all TBD items with owner chapters
- CH00 §5 (Decision Log) records all finalized global locks

**Assumptions Detected:**

- Assumes multi-chat/multi-session PDF generation workflow where chapters are created separately
- Assumes Replit or similar AI coding environment will be used for implementation
- Assumes strict revision control discipline (bump CH00 to R2 if any lock changes)

---

## CH01: Product Definition and V1 Scope

**Overview:**

CH01 defines what Handz V1 is: a mobile tool for strikers to map decision-making as flowcharts ("flows") and drill selected paths with guided timers. It establishes the product promise ("Map it, Drill it, Keep it, Share it"), target users (strikers, coaches, hobbyists), success criteria, and explicit V1 scope boundaries. The chapter locks what is shippable for V1 and what is intentionally excluded (non-goals), ensuring scope governance and preventing feature creep.

**Key Requirements:**

- **Product Core:**
  - Core problem solved: forgetting sequences, decision overload, practice structure, progress visibility
  - Product promise: Turn striking knowledge into decision trees; drill paths with guided timers; organize and search flows; share unlisted flows
  - V1 launch posture: iOS-only, portrait-only, Guest/Free/Pro/Trial plans, paywalled Practice Mode
  - Target users: Primary = consistent strikers; Secondary = coaches sharing gameplans; Tertiary = hobbyists; Non-target = community-first users expecting social feeds

- **V1 Ship Scope (7 Capability Groups):**
  - **A. Foundations:** Welcome/onboarding; Apple/Google/Email sign-up + Guest mode; Design system applied consistently
  - **B. Knowledge Model:** Default move library (flexible across striking arts); Custom moves + editing + revert; Aliases/families/variants support
  - **C. Flow Builder:** Sideways layout + pan/zoom; reorder/replace root; branches up to 10; merges; dangling paths; optional sequence details
  - **D. Library & Organization:** Flow folders; flow-only search (v1 constraint); Flow detail view with edit/share/duplicate/export entry points
  - **E. Sharing & Inbox:** Unlisted links with lifecycle (create/view/revoke); Inbox with caps and view-only rules for Free; Import conflict resolution
  - **F. Practice, Logs, Gameplans:** Practice mode (paywalled) with path selection, ordering, timers, early-end; Logging/history with actual duration; Gameplans + mastery + maintenance (MVP level)
  - **G. Ship Readiness:** Offline behavior defined; Storage/limits enforced (2GB cap); Warning ladder + soft caps; Error states + accessibility readiness; App Store compliance (export & deletion)

- **Explicit V1 Non-Goals:**
  - No communities, gym codes, public feeds, follower systems, or social discovery
  - No public flows by default (unlisted sharing only)
  - No AI video analysis / auto-extraction from video
  - No authoritative technique instruction library
  - No wearables, sensors, or strike-count tracking
  - No desktop/tablet-first experiences (portrait-only on iOS)

- **Product Principles:**
  - Minimal but not generic; modern, addictive-to-use UI; fast interactions; no clutter
  - Progressive disclosure: beginners see simple choices; power users can go deep
  - Complexity is optional: every feature has basic and advanced paths
  - No technique wars: default library avoids arguable technique prescriptions
  - Trust & clarity: plan restrictions communicated before walls; actions reversible when possible
  - Respect training context: many users have gloves on; avoid frequent typing mid-practice

- **Success Criteria (Product-Level Metrics):**
  - Activation: % who view demo flow and understand what a flow is
  - Creation: % who create or duplicate a flow into library (requires account)
  - Engagement: # returning days in first 14 days; # flows viewed/edited
  - Practice adoption: % who start practice session and complete at least one set
  - Conversion: trial start rate; trial-to-paid conversion; upsell CTR
  - Retention: 30-day retention for users with ≥1 saved flow

**Open Questions/Ambiguities:**

- Scientific-claims specifics (owner CH26) - vague reference to "what we can truthfully promise" without specifics
- Default move list finalization (owner CH09/CH10) - not locked yet
- Final upsell copy (owner CH25/CH26) - TBD

**Cross-References:**

- Depends on: CH00 (global locks)
- Related to: CH02 (non-goals), CH03 (glossary), CH04 (navigation), CH05 (screen inventory), CH06 (design system), CH07 (auth), CH08 (entitlements), CH09 (moves), CH12 (flow builder), CH20 (practice), CH25 (monetization), CH30 (safety)
- Scope governance: if new idea affects V1 scope, must update CH00 lock or create placeholder in owning chapter

**Assumptions Detected:**

- Assumes users are willing to sign up to save work (Guest mode is for exploration only)
- Assumes "flows" concept is unfamiliar and requires onboarding explanation
- Assumes Free plan drives adoption, but meaningful value is paywalled

---

## CH02: Non-Goals, Assumptions, and Constraints

**Overview:**

CH02 defines V1 platform/form-factor constraints, explicit non-goals, assumptions about users/devices/usage, and a constraints catalog that must be enforced (not just documented). It exists to remove guesswork for builders and reduce bugs from ambiguous expectations. Constraints are categorized as hard requirements with enforcement patterns and server-side validation expectations. The chapter also includes a placeholder registry for unknowns that impact build decisions.

**Key Requirements:**

- **V1 Non-Goals (Explicitly Out of Scope):**
  - **Platforms:** No Android; no landscape orientation; no iPad/tablet optimization; no web app as product surface
  - **Social/Community:** No public feeds, comments, likes, follower graph; no gym codes, team spaces; no public profiles; no in-app messaging/DMs; no moderation tooling
  - **AI/Automation:** No automatic move extraction from video; no pose detection; no opponent video analysis; no auto-generated flows from prompts
  - **Advanced Content:** No embedded technique encyclopedia; no required sequence nodes (optional only); no custom scripting for conditions
  - **Collaboration:** No real-time collaborative editing; no full library sharing (unlisted links only); no complex export formats (video montage, Notion sync, Drive integration)
  - **Hardware:** No Apple Watch app; no sensor integrations

- **V1 Assumptions (What We Presume is True):**
  - **User/Usage:** Most users are strikers (boxing/kickboxing/Muay Thai/karate/TKD); users want both extremes (quick notes and obsessive detail); typical editing sessions may be ~2 hours; practice sessions may happen with gloves on (avoid frequent precise tapping); users willing to sign up to save work (Guest is exploration only)
  - **Vocabulary:** Many users don't know what a "flow" is on launch (onboarding must explain); move naming varies by gym/style (aliases matter); users may want both names present (teep and push kick)
  - **Device/Environment:** Users have iPhone with minimum iOS version (placeholder: iOS 17 temporary); intermittent internet (core authoring should not catastrophically fail); users may be in gyms with poor reception (practice mode should not require network during active session); users may deny camera/photo permissions (must gracefully degrade)
  - **Business:** Monetization uses StoreKit subscriptions (iOS-first); Free exists to drive adoption, meaningful value paywalled; unlisted sharing is growth loop

- **V1 Constraints Catalog (Must Be Enforced):**
  - **Platform & Layout (Hard):** iOS-only; portrait-only; one-handed use bias (primary actions in reachable zones, large hit targets)
  - **Account & Guest (Hard):** Saving flows requires account; Guests cannot save flows locally or in cloud; Guest capabilities limited to demo browsing; Login methods: Apple, Google, Email (must be supported)
  - **Entitlements & Caps (Hard):** Plan states: Guest/Free/Pro/Trial; Practice paywalled (Free gets 3 monthly credits for saved flows only); Free saved flows cap: 2; Free inbox cap: 10 (can view but cannot practice inbox items); Branching: up to 10 outgoing branches per move; Uploads: Pro-only, 2GB cap, private-only, not shared (links shareable)
  - **Content Model (Hard):** No authoritative technique descriptions shipped; Aliases can be separate canon moves when community uses both terms; Moves can belong to multiple tags; flows can mix arts
  - **Flow Builder Interaction (Hard):** Builder must support sideways flow with pan/zoom; single move can branch to up to 10 next moves; branches can branch recursively; merges can have multiple incoming paths; dangling paths allowed; root move must be replaceable
  - **Practice (Hard):** Practice must support selecting paths from different flowcharts and ordering them; actual duration logging; interruptions saved as interrupted; must be usable without constant phone touching (glove-friendly controls)
  - **Sharing (Hard):** Unlisted-only in V1 (no public directory); if flow includes uploaded videos, recipients must be told uploads don't travel (only links shareable); importing must not accidentally overwrite recipient's canonical move definitions without explicit choice
  - **Data, Privacy, Safety (Hard):** Data must be exportable; account deletable (App Store compliant); safety/abuse escalation uses warning ladder; share links must be revocable; anti-abuse thresholds apply even to Pro; no health/medical claims without controlled language and disclaimers

- **Enforcement Expectations:**
  - Centralized config: define constraints/caps in one place (constants + feature-gate helpers); do not hardcode in UI
  - Server-side validation: even if UI blocks, backend must also reject (e.g., creating 3rd saved flow on Free)
  - Clear user messaging: when blocking, show reason + next step (upgrade, delete, sign up)
  - Consistent copy tokens: use shared copy strings so same rule phrased identically across app
  - Examples: Guest taps "Save Flow" → show sign-up gate, no local draft; Free tries to save flow #3 → block and route to manage/delete/upgrade; Free opens inbox item → allow view, hide/disable Practice with paywall messaging; User rotates device → UI remains portrait; User creates branch #11 → block and show soft warning

**Open Questions/Ambiguities:**

- PLACEHOLDER: Minimum iOS version - Options: iOS 16/17/18 - Default: iOS 17 (temporary) - Decide-by: before first TestFlight
- PLACEHOLDER: iPad behavior - Options: allow compatibility / explicitly block / support iPad portrait only - Default: allow compatibility (temporary)
- PLACEHOLDER: Localization - Options: English-only / English + Spanish - Default: English-only (temporary)
- PLACEHOLDER: Accessibility baseline - Options: minimum WCAG checks vs full pass - Default: baseline (temporary) - Details owned by CH32
- PLACEHOLDER: Offline authoring guarantees - Options: no offline / drafts-only / full offline-first - Default: drafts-only (temporary) - See CH28

**Cross-References:**

- Depends on: CH00 (Master Index), CH01 (Product Definition & Scope)
- Related to: CH04 (Navigation), CH07 (Auth & Account), CH08 (Entitlements), CH12 (Flow Builder), CH20-CH22 (Practice), CH25 (Monetization), CH28-CH31 (Offline/Sync, Data Limits, Safety, Errors)
- Enforcement details: CH07 (guest rules), CH08 (plan gating), CH12 (builder constraints), CH17 (sharing constraints), CH18-CH19 (import/inbox), CH20-CH22 (practice constraints), CH28 (offline), CH29 (storage), CH30 (warning ladder), CH34 (export/deletion)

**Assumptions Detected:**

- Assumes constraints must be enforced server-side even if UI blocks
- Assumes Free plan caps are intentionally restrictive to drive conversion
- Assumes practice sessions happen in gyms with poor connectivity

---

## CH03: Core Concepts & Glossary

**Overview:**

CH03 defines the canonical meaning of key terms used throughout the Handz V1 PRD. All other chapters must use these terms consistently. When a term appears with an initial capital (Flow, Path, Gameplan), it refers to the definition in this chapter. The chapter provides a concept map showing how five core object families relate (Moves, Flows, Practice, Mastery/Maintenance, Sharing), detailed glossary definitions grouped by domain, terminology conventions, and modeling rules to avoid vocabulary drift.

**Key Requirements:**

- **Concept Map (5 Core Object Families):**
  - **Moves:** reusable units (Jab, Teep, Slip) placed into Flows
  - **Flows:** structured decision maps built from Moves (nodes) connected by Paths (edges)
  - **Practice:** timed drilling of selected Paths, logged into History
  - **Mastery/Maintenance:** optional layer turning Paths into Gameplans with goals and upkeep
  - **Sharing:** unlisted links creating Inbox Imports, which can be saved into recipient's Library
  - High-level relationship: Move → Flow → Paths → Practice Session → Practice Logs → Mastery/Maintenance

- **Glossary Definitions (Key Terms):**
  - **Account & Plans:** User, Guest (no account, can explore but not save), Free (signed-in with caps: 2 saved flows, 10 inbox, practice paywalled except 3 monthly credits), Pro (subscription, removes/expands caps), Trial (time-limited, behaves as Pro), Entitlement (boolean feature-right from plan state)
  - **Moves:** Move (reusable action label), Default Move (built-in, shipped with app, broadly applicable), Custom Move (user-created, private notes/media), Canonical ID (stable identifier across imports/shares), Alias (alternate name for same concept), Move Family (grouping like "Kick family"), Variant (distinct move derived from base, e.g., Check Hook from Hook), Tag (user-facing label, many-to-many), Attributes (structured fields like stance/side/target), Technique Description (narrative explanation; V1 ships with zero by default), Media Attachment (private upload [Pro-only, capped] or link [shareable])
  - **Flow Builder:** Flow (user-created decision structure), Canvas (interactive space with sideways layout + pan/zoom), Node (unit on Canvas: Move Nodes and optional Sequence Nodes), Move Node (references a Move), Sequence Node / Sequence Detail (optional transition detail between Moves), Edge (directed connection), Path (specific route through Flow; what users select for Practice), Branch (single Move with multiple outgoing edges; up to 10), Merge (multiple incoming edges to single Move), Dangling Path (unfinished branch; allowed for drafting), Root Move (starting node; replaceable), Draft vs Saved Flow (Draft not counted toward caps until explicitly saved), Flow Direction (layout orientation; sideways primary for large branching)
  - **Practice Mode:** Practice Mode (guided drill with timers, rest, logging; paywalled), Practice Setup (pre-session: choose paths, order, timers, rep assumptions), Practice Session (single run; can complete, end early, or save as interrupted), Set (timed drill block for selected Path), Rep (Assumed) (target count; V1 does not auto-count), Actual Duration (real elapsed time including pauses), Early End (ending before timer expires), Interruption / Save as Interrupted (stopping mid-session with partial progress saved), Practice Credit (limited-use for Free; 3 monthly, usable only on saved flows), Practice History (log view of past sessions)
  - **Mastery, Gameplans, Maintenance:** Gameplan (user-defined collection of Paths from one or multiple Flows), Mastery (state model indicating how learned a Path/Gameplan is; allows manual adjustments), Mastery Plan (generated schedule to move Path/Gameplan toward goal; uses averages; includes disclaimers), Maintenance (recurring light practice to keep mastered Paths fresh; prevents overload), Maintenance Overload (risk where too many Paths need upkeep; system prevents via prioritization), Spaced Repetition (learning principle; claims language owned by CH26)
  - **Sharing, Inbox, Imports:** Unlisted Share Link (shareable URL for view/import; can be revoked), Inbox (receiving queue; Free cap: 10; Free can view but not practice inbox items), Import (accepting shared Flow into account; may create Saved Flow; may bring custom move data), Import Conflict Resolution (mapping process when shared Flow references Moves recipient doesn't have or customized differently), Duplicate Into Account (copy Flow into Library as Saved Flow)
  - **Limits, Safety, Warnings:** Soft Cap (limit enforced by warnings + UX friction; can be exceeded temporarily or with acknowledgement), Hard Limit / Block (strict limit preventing action; must provide clear messaging and recovery path), Warning Ladder (escalation system standardizing warnings, cooldowns, blocks across features)

- **Terminology and Naming Conventions (Normative):**
  - Use Move (reusable unit), Flow (canvas artifact), Path (route through Flow)
  - Use Gameplan only for user-curated set of Paths (possibly across Flows)
  - Use Practice for guided drill feature; avoid "training" in UI copy (unless marketing)
  - Capitalize defined terms when used as nouns mapping to data objects (Move, Flow, Path, Gameplan, Inbox)
  - Avoid overloaded words like "combo" in product UI; Flow is umbrella term, Paths/branches provide nuance

- **Modeling Rules (Avoid Terminology Drift):**
  - Canonicals may be intentionally separate even if some users think they're the same (Teep vs Push Kick)
  - Families are for discoverability, not forcing correctness; users can select multiple Moves within family during onboarding
  - Attributes should not explode Move list; if distinction is primarily lead/rear or stance, prefer attributes; if execution difference meaningful and commonly named, allow distinct Move or Variant
  - Imports preserve creator intent by default; imported Flows should look/behave like sender's version unless recipient explicitly remaps during conflict resolution
  - Never delete Canonical IDs; deprecated items hidden from default selection but remain resolvable for legacy content

**Open Questions/Ambiguities:**

- None in this chapter; any product-number placeholders live in CH00 §6 and their owning chapters

**Cross-References:**

- Depends on: CH01 (Product Definition), CH02 (Constraints), CH08 (Entitlements)
- Related to: CH04, CH05, CH09-CH14 (Moves & Flow Builder), CH17-CH24 (Sharing, Practice, Mastery), CH28-CH31 (Offline, Storage, Safety, Errors)
- Placeholder ownership: CH00 §6 (Placeholder Registry), CH25 (pricing), CH26 (scientific claims), CH30 (abuse thresholds)
- If another chapter needs new term or changed meaning, update CH03 in new revision, then update dependent chapters

**Assumptions Detected:**

- Assumes terminology drift is a risk and must be actively managed via single source of truth
- Assumes users may use different vocabulary for same technique (aliases essential)
- Assumes some users want fine-grained distinctions (variants) while others want simplicity (families)

---

## CH04: Information Architecture and Navigation Map

**Overview:**

CH04 defines the global navigation model for Handz V1 (iOS portrait): top-level app states, tab structure, route registry, modal registry, deep links, and back behavior. It is written to minimize guesswork for implementation and QA. Page-by-page UI content is owned by CH05; this chapter owns routing and navigation rules. The chapter establishes three top-level states (Signed Out, Guest, Signed In), a 4-tab bottom-bar structure (Library, Practice, Moves, Settings), comprehensive route IDs, modal presentation patterns, and behavior for unsaved work protection, deep links, and edge cases.

**Key Requirements:**

- **App State Machine (3 Top-Level States):**
  - **State A - Signed Out:** user not authenticated; can open Welcome Gate and view public/unlisted shared flows (view-only)
  - **State B - Guest:** temporary session with limited exploration; guests cannot save flows (even locally) and cannot create custom moves; can explore demo content and flow builder in non-saving sandbox
  - **State C - Signed In:** full app shell with tabs; entitlements (Free/Pro/Trial) only affect gated routes and paywall intercepts
  - State transitions: Signed Out → Guest (tap "Try without account"); Signed Out → Signed In (complete sign up/log in); Guest → Signed In (create account, optionally re-run Move Preferences onboarding); Signed In → Signed Out (logout or delete account)

- **Top-Level Navigation Shell (Signed In State):**
  - Bottom tab bar with 4 tabs; each tab owns its own stack; modals can be presented over any tab
  - **Tab 1 - Library:** saved flows, folders, search (flow-only search in v1), inbox entry point, flow detail, create/edit flows (screens: CH15-CH19)
  - **Tab 2 - Practice:** practice hub, setup, active session, history, mastery/gameplans, maintenance (screens: CH20-CH24)
  - **Tab 3 - Moves:** default move library, user moves, editing, tags/families/aliases management (screens: CH09-CH11)
  - **Tab 4 - Settings:** account, subscription, notifications, help, export, delete (screens: CH25, CH27, CH34, CH36)
  - Default landing: first-time user lands on Library tab with Demo Flow + guided overlay; returning user lands on last-used tab/screen; deep link launch takes precedence

- **Route Registry (Authoritative - 60+ Routes):**
  - **Auth & Onboarding:** WelcomeGate, SignUp, LogIn, ForgotPassword, EmailVerifyPending, MovePreferences (onboarding), TutorialOverlay
  - **App Shell & Tabs:** Tabs (root), Tab/Library, Tab/Practice, Tab/Moves, Tab/Settings
  - **Library Stack:** Library/Home, Folder, FlowDetail, FlowBuilder, FlowBuilderSandbox (Guest), Inbox, InboxItem, ImportConflict
  - **Practice Stack (Paywalled):** Practice/Home, Setup, Active, Summary, History, Gameplans, Maintenance, Gate (paywall intercept)
  - **Moves Stack:** Moves/Home, MoveDetail, CreateMove, EditMove, AliasPicker, FamilyPicker
  - **Settings, Subscription, System:** Settings/Home, Account, Subscription, Notifications, Privacy, Help; Paywall/Upgrade (full modal); System/ShareSheet, MediaPicker, PermissionGate
  - Access patterns: routes marked SO (Signed Out), G (Guest), F (Free), P (Pro), T (Trial); presentation: Push (stack), Sheet (iOS page sheet), Full (full-screen modal), AS (action sheet), System (system picker/share)

- **Modal Registry:**
  - Standard types: Sheet (preferred for pickers/conflict resolution), Full (paywalls/immersive flows), Action Sheet (destructive confirmations), System (iOS share/media/permissions)
  - Core modals: MovePicker, CreateMoveInline, NodeActions, SequenceEditor, ImportConflict, UpgradePaywall, SoftWarning
  - Dismiss rules: swipe-down enabled unless unsaved edits; if unsaved, show "Unsaved Changes" AS with Keep Editing / Discard / Save Draft; underlying screen must remain in exact scroll/zoom state after dismiss

- **Back Behavior and Exit Rules:**
  - Within tab stack: back pops one screen; from tab root: back exits app (standard iOS)
  - Switching tabs preserves each tab's stack and scroll position (unless memory pressure)
  - Deep link: exiting returns to most logical tab root
  - **Unsaved Work Protection:** Flow Builder (saved flow): leaving triggers Unsaved Changes AS (Save/Discard/Keep Editing); Flow Builder (sandbox): AS offers Discard Sandbox / Keep Editing / Create Account to Save (primary CTA); Move Create/Edit: leaving triggers Unsaved Changes AS, includes "Revert to Default" if editing default move override; Practice Setup: confirm discard if configured; Active Practice: back triggers pause + confirmation (Continue / End & Save as Interrupted / Discard Session)
  - **Deletions & Destructive Actions:** all require confirmation AS with clear consequences; deleting flow: warn if referenced by Gameplan/Maintenance, offer "Remove from lists" or Cancel; deleting custom move: warn if used in flows, offer "Replace with…" (Move Picker) or Cancel

- **Deep Links and Share Links:**
  - Link types: Unlisted Flow Link (view-only FlowDetail variant with embedded link-media), Internal App Links (notifications), Store/Upgrade Links
  - Unlisted flow link behavior: if app installed → open ShareLink/FlowView (view-only); if not installed → web fallback (placeholder); view-only screen shows flow title, creator name, last updated, simplified viewer; primary CTA: "Open in Handz" (signed in) or "Create account to save" (signed out/guest); if flow includes uploaded video, show badge "Private upload - not shared via link" (links/embedded web media shareable)
  - Deep link return path: if opened while Signed Out, closing returns to Welcome Gate; if Signed In, closing returns to Library tab root; if imported from share view, navigate to imported FlowDetail in Library

- **Edge Cases Affecting Navigation:**
  - **Offline/Sync:** if offline on launch, open to last cached screen with non-blocking "Offline" banner; opening share link offline → offline error with retry, offer "Save link to open later"; starting Practice offline → allow if data cached, else show blocking error with retry
  - **Gating & Limits Intercepts:** Free taps "Start Practice" with 0 credits → open Paywall/Upgrade (Full); Free tries to save 3rd flow → intercept with "Flow Limit Reached" sheet (Upgrade / Delete / Cancel); Free tries to practice Inbox item → show sheet explaining practice requires saved flow + Pro/credits, offer Import to Library (if slots available) or Upgrade; Inbox at cap (10) and new import arrives → soft warning, keep newest in "Pending (not stored)" until user clears space
  - **Link Revocation/Invalid Token:** if share token invalid or revoked → show "This link is no longer available" with CTA to Library (signed in) or Welcome Gate (signed out); if token valid but flow deleted → same as revoked

**Open Questions/Ambiguities:**

- Tab icon set + exact labels - not locked
- Whether to include distinct "Home" dashboard vs using Library as home - default in chapter: Library is home
- Whether Inbox is standalone tab or Library header entry - default in chapter: Library header entry

**Cross-References:**

- Depends on: CH01 (Product Definition), CH02 (Constraints), CH03 (Glossary)
- Related to: CH05 (Screen Inventory - owns per-screen UI), CH06 (Design System), CH07 (Auth conversion prompts), CH08 (Plan gating rules), CH12 (Flow builder interaction), CH15 (Library), CH16 (Flow Detail), CH17 (Sharing), CH18 (Inbox), CH20 (Practice), CH21 (Active session), CH22 (History), CH23 (Mastery), CH24 (Maintenance), CH25 (Monetization), CH28 (Offline), CH29 (Storage), CH30 (Warning ladder), CH31 (Error/empty states), CH32 (Accessibility)
- CH04 only defines where gating intercepts happen and what route opens next; detailed gating logic owned by CH08

**Assumptions Detected:**

- Assumes iOS native navigation patterns (UINavigationController stack, UITabBarController)
- Assumes users may open app from deep links while signed out (need graceful return paths)
- Assumes tab stack preservation is expected behavior (users get frustrated if tabs reset)

---

## Batch 01 Summary

**Requirements Extracted:** 150+ key requirements

- User-facing: ~60 (product definition, flows, practice, sharing, navigation)
- Technical/backend: ~50 (auth, entitlements, constraints, offline, storage, caps)
- System/architectural: ~40 (navigation patterns, state machine, route registry, modal system, cross-references)

**Ambiguities Flagged:** 12 total

- TBD/placeholders: 6 (exact node cap, default-move list, share link caps, notification defaults, analytics events, scientific-claims copy)
- Vague language: 2 ("may", "should" used in pricing direction, iPad behavior)
- Missing specifications: 3 (minimum iOS version, localization, accessibility baseline, offline authoring guarantees)
- Conflicts: 1 (CH09 has two files with slightly different names - "Default_Move_Library_System" vs "Moves_Default_Library_System" - need to clarify which is authoritative)

**Critical Findings:**

- **Locked Global Constraints (Non-Negotiable):** iOS-only, portrait-only, Guest/Free/Pro/Trial plan states, Practice paywalled (Free gets 3 monthly credits), Free caps (2 saved flows, 10 inbox), branch limit (10 per move), uploads (Pro-only, 2GB, private), pricing ($9.99/month, 7-day trial)
- **V1 Scope Boundaries Extremely Clear:** 38 chapters, 7 capability groups for ship readiness; extensive non-goals list prevents scope creep (no Android, no landscape, no social features, no AI video analysis, no wearables)
- **Terminology Glossary is Comprehensive:** 50+ defined terms with strict capitalization rules; modeling rules prevent drift; all chapters must reference CH03 for canonical definitions
- **Navigation is Fully Specified:** 60+ routes with access patterns, 3-state machine (Signed Out/Guest/Signed In), 4-tab structure, modal registry, back behavior, deep links, edge cases - minimal implementation ambiguity
- **Cross-Reference Discipline Required:** CH00 establishes strict rules for cross-referencing between chapters; conflicts resolved by updating owning chapter and bumping revision (not patching code)
- **Enforcement Mindset:** CH02 emphasizes constraints must be enforced server-side even if UI blocks; centralized config required; no silent loopholes

**Cross-Chapter Dependencies:**

- **CH00 → All Chapters:** Global locks, placeholder registry, naming standards, revision control
- **CH01 → CH02, CH03, CH08, CH25:** Product scope boundaries feed constraints, glossary, entitlements, monetization
- **CH02 → CH07, CH08, CH28, CH29, CH30:** Platform/form-factor constraints feed auth, entitlements, offline, storage, safety
- **CH03 → CH09-CH24:** Glossary definitions feed all feature chapters (moves, flows, practice, mastery, sharing)
- **CH04 → CH05, CH12, CH15-CH24, CH25, CH28, CH31:** Navigation structure feeds screen inventory, all feature flows, monetization, offline, errors
- **Notable Missing Dependencies:** CH04 references "Demo Flow" but no chapter yet defines what's in it; CH00 lists CH09 twice (possible duplicate file); CH01 references "7-day trial" but CH25 (Monetization) not yet reviewed

**Recommended Focus for Batch 02:**

Based on dependency analysis, next batch should cover **Design, Auth, and Entitlements (CH05-CH08)** to establish:
- Screen inventory (CH05) - referenced by CH04 navigation
- Design system (CH06) - needed before UI implementation
- Authentication & Account system (CH07) - foundational for all plan states
- Entitlements & Plan States (CH08) - enforces all caps and gating rules referenced in CH00-CH04

This will complete the "foundation layer" before diving into feature chapters (Moves, Flows, Practice).

---

**Next Steps:**

Awaiting your approval before proceeding to Batch 02 (CH05-CH08).

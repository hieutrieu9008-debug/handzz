# PRD Batch 10B — CH37-CH38

## Chapter Overview

**CH37 (Vibe Coding Prompt Pack):** A prompt library for AI-assisted development (Replit/agentic coders). Provides copy-paste prompts for chapter-by-chapter implementation, troubleshooting templates, and execution guidelines. Not a feature spec.

**CH38 (Development Milestones & Release Checklist):** Operational plan converting feature specs into shippable iOS app. Defines milestone gates (M0-M7), build order, App Store submission checklist, rollback/hotfix procedures, and release readiness criteria.

---

## CH37 — Vibe Coding Prompt Pack

### Document Metadata
- **Doc ID:** HZ-V1-CH37_Vibe_Coding_Prompt_Pack_R1
- **Status:** Draft
- **Depends on:** CH00 (anchor), CH04 (Nav Map), CH05 (Screen Inventory)
- **Related:** CH35, CH36, CH38
- **Owned Decisions:** Prompt standards and build-order guidance for Replit vibe-coding execution

### Key Requirements

#### 1. Usage Model
- Attach CH00 + exactly one chapter PDF to Replit per implementation pass
- Implement one chapter at a time (not "whole app")
- Cross-references are dependencies; if missing, create stub or stop and request
- Missing details must be written to "PRD Assumptions" comment block and paused until confirmed (per CH00 Section 8)

#### 2. Output Policy
Agent must respond with:
1. What it built
2. Where in code it lives
3. How to test it
4. What it did NOT build due to missing specs

#### 3. Global Prompt Rules (Every Chat)
- Treat attached PDFs as only source of truth
- Do not invent product behavior
- Implement only requested chapter
- Write "Conflict Note" and stop if conflicts with other chapters detected
- Do not summarize requirements away
- Include edge cases, error states, empty states, loading states, UI copy exactly as specified
- Present A/B/C options with tradeoffs if choices required; pick default only if PRD explicitly allows
- Use iOS-first patterns, portrait-only assumptions, plan-state gating per CH00

#### 4. Prompt 0 — Bootstrap Requirements
Creates minimal running iOS app shell:
- React Native iOS-first project (Expo or bare RN)
- Record framework choice in PRD Assumptions with rationale
- Placeholder screens: Home, Library, Practice, Settings
- Bottom tab navigation (named, consistent routes)
- Global theme tokens stub (colors, typography) for CH06
- Plan-state stub: Guest/Free/Pro/Trial enums
- Single entitlement check location (even if hardcoded initially)
- Dev-only debug toggle to switch plan state
- Simple local logging utility with chapter tags (e.g., [CH07] Auth)
- `/docs` folder tracking implemented chapters + PRD Assumptions

**Stop conditions:** Do NOT implement auth, flow builder, practice logic, or monetization

#### 5. Recommended Build Order (9 Phases)

| Phase | Description | Chapters |
|-------|-------------|----------|
| A | Foundation & UX System | CH01-CH06 |
| B | Accounts & Entitlements | CH07-CH08 |
| C | Moves System | CH09-CH11 |
| D | Flow Builder (core creation) | CH12-CH14 |
| E | Library & Sharing Funnel | CH15-CH19 |
| F | Practice, Mastery, Maintenance | CH20-CH24 |
| G | Monetization & Claims | CH25-CH26 |
| H | Reliability, Safety, Compliance | CH27-CH34 |
| I | Ship Readiness | CH35-CH36, CH38 |

#### 6. Standard Chapter Implementation Prompt (SCIP) Template
Hard requirements per chapter:
1. Build UI and logic exactly as specified
2. Routing/navigation must match CH04/CH05 screen names
3. Plan-state gates (Guest/Free/Pro/Trial) per CH08/CH25
4. Loading/empty/error states with exact UI copy when provided
5. Test coverage: Given/When/Then acceptance tests as runnable or manual checklist in `/docs/tests/CH##.md`
6. Update `/docs/implemented_chapters.md` with "How to test" section

**Stop conditions:**
- Missing dependency chapter: create stub + TODO, list under "Blocked by"
- Missing detail: write to PRD Assumptions, DO NOT guess behavior

#### 7. Chapter-Specific Constraints (Selected)
- **CH06:** Implement tokens + components; no hardcoded colors; provide reusable buttons, inputs, cards, chips, toasts, modals, bottom sheets
- **CH07:** Apple/Google/Email auth; enforce guest limitations and conversion prompts; no local saving for guest
- **CH08:** Centralize gating; all features call entitlement checks; include dev override toggle
- **CH10:** Canonical IDs and alias mapping; push kick vs teep coexist canonically; no forced merging; move families/variants
- **CH12:** Pan/zoom sideways builder, reorder, replace root, branching up to 10, merges, dangling paths allowed; optimize performance
- **CH17:** Unlisted links only; create/view/revoke; enforce link caps (soft); view-only links
- **CH18:** Free inbox cap 10; free can view but cannot practice; save-to-library enforces 2 flows cap
- **CH20:** Practice paywalled; free gets 3 monthly credits usable only on saved flows; path selection + ordering
- **CH21:** Early end behavior (actual duration); interruptions save as interrupted
- **CH25:** StoreKit subscriptions iOS; 7-day trial; $9.99; upsell placements/copy as specified; keep flexible via constants
- **CH29:** 2GB Pro upload cap; uploads private only and not shareable; link attachments shareable
- **CH30:** Warning ladder, soft warnings, anti-abuse limits; do not block normal usage; log triggers
- **CH34:** Export + account deletion flows with confirmations; App Store compliance language

#### 8. Troubleshooting Prompts Library
Templates for:
- **Bug reproduction/fix:** Minimal scope, add/extend tests, explain root cause in 3-6 bullets
- **Performance regression:** Instrumentation, memoization, virtualized lists, timer optimization
- **Navigation bugs:** Fix using existing nav system; do not rename routes unless PRD requires
- **Entitlement gating:** Centralize gating (single source of truth); UI explains why when blocked; test matrix for plan states
- **Schema change:** Backward-compatible migration first; versioning; fallback reads; no field deletion without explicit PRD approval

#### 9. Definition of Done (Per Chapter)
- App builds/runs on iOS simulator; no crashes on touched navigation paths
- All new screens have loading/empty/error states with appropriate UI copy
- All new data writes are idempotent (no duplicates on retry)
- Entitlement gates respected for all features
- Test checklist at `/docs/tests/CH##.md` executable by non-coder
- `/docs/implemented_chapters.md` updated with status, how to test, known issues, assumptions

---

## CH38 — Development Milestones & Release Checklist

### Document Metadata
- **Doc ID:** HZ-V1-CH38_Development_Milestones_Release_Checklist_R1
- **Status:** Draft (Ready for review)
- **Depends on:** CH00, CH35, CH25, CH07, CH08
- **Related:** CH04, CH05, CH06, CH12-CH22, CH27-CH34
- **Owned Decisions:** Milestone definitions, readiness gates, build order, App Store submission artifacts, release rollback plan

### Open Questions / Placeholders
- Exact target dates
- Exact App Store metadata copy
- Final pricing validation checkpoints
- Legal policy URLs

### Key Requirements

#### 1. Milestone Model (M0-M7)
Gates are passed only when ALL acceptance criteria met. Failed gate = return to previous gate; do not pile new features on failing gate.

| Stage | Purpose | Exit Criteria Summary |
|-------|---------|----------------------|
| M0 | Repo & Tooling Ready | App compiles; navigation shell; CI runs; crash reporting wired |
| M1 | Core Data Ready | Auth + plan states; create/edit moves/flows (draft); offline drafts |
| M2 | Flow Builder Ready | Pan/zoom; branching up to 10; reorder; sequence details; save/load; free caps |
| M3 | Practice Ready (Paywalled) | Setup; active session; interruptions; logging; credits; view-only for inbox flows |
| M4 | Sharing + Inbox Ready | Unlisted links; inbox cap; import conflict rules; save-to-library gating |
| M5 | Monetization + Paywalls Ready | Trial; $9.99; restore purchases; entitlement sync; upsell surfaces; receipt validation |
| M6 | App Store Ready (RC) | Privacy/compliance; performance; accessibility baseline; QA pass; marketing assets |
| M7 | Launch + Monitoring | Production rollout plan; dashboards; incident playbook; hotfix pipeline |

#### 2. Build Order by Dependency (13 Steps)
0. Bundle setup: CH00 + CH38; create `/prd` folder
1. App shell + navigation (CH04, CH05, CH06)
2. Auth + plan states (CH07, CH08, CH25)
3. Core data models: Moves, flows, nodes/edges, sequences, paths, gameplans + CRUD with local-first drafts + sync (CH09-CH14, CH28, CH29)
4. Flow builder (CH12-CH14)
5. Library + detail views (CH15-CH16, CH08)
6. Sharing + inbox (CH17-CH19)
7. Practice mode (CH20-CH22, CH25)
8. Mastery + maintenance (CH23-CH24, CH27)
9. Safety/abuse + warnings (CH30)
10. Error states + offline (CH28, CH31)
11. Monetization hardening (CH25)
12. QA + release prep (CH32-CH36)

#### 3. M0 — Repo & Tooling Ready
- Repo structure: `/app` (source), `/docs` (dev notes), `/prd` (PDFs), `/scripts` (build helpers)
- iOS simulator + physical device builds succeed
- Tab scaffolding exists (even if placeholder screens)
- Remote config toggle: kill-switch flag to disable Practice Mode server-side
- Crash reporting: logs captured (tool choice outside PRD)
- Analytics stub: event logger wrapper with no-op in dev mode
- App icon + launch screen placeholders with approved Handz logo assets

#### 4. M1 — Core Data Ready
- Auth: Apple + Google + Email; session persists across relaunch
- Plan states: Guest/Free/Trial/Pro determinable via single helper (no duplicated gating logic)
- Guest restrictions: browse only; cannot save flows even locally; must see account requirement before building
- Moves: default pack loads; custom moves; aliases/families/variants framework exists
- Flows: create draft; edit title; add/remove nodes; discard; save to library (account required)
- Free caps: max 2 saved flows; UI explains cap + offers upgrade
- Offline: network drop during edit preserves local changes; sync on reconnect; clear status indicator

#### 5. M2 — Flow Builder Ready
- Pan/zoom smooth; sideways layout supports both mental models (top-to-bottom, left-to-right)
- Branching: any move node up to 10 outgoing branches; branches can branch; merges with multiple incoming; dangling paths allowed
- Reordering: reorder nodes/paths; replace root; replace any move; preserve edges when sensible; prompt when destructive
- Sequence detail: optional sequence node; summary node option; details editor saves reliably
- Validation: prevent impossible states; show repair UI (not silent failure) for invalid states
- Performance: stress test 75+ nodes with many branches without crash; frame drops acceptable but usable
- Autosave: drafts autosave; undo/redo policy consistent and tested

#### 6. M3 — Practice Ready (Paywalled)
- Setup: path selection across flows, ordering, timers, assumed reps
- Eligibility: Pro/Trial OR monthly credits; credits usable only on saved flows (not inbox)
- Active session: timer, pause, early end (workout-tracker norms), save as interrupted when appropriate
- Logging: actual duration; per-path completion counts; streak uses local time; one completed set = one day
- History: view past sessions; interruptions distinguishable from completes
- Free restrictions: cannot practice inbox-only flows; view only

#### 7. M4 — Sharing + Inbox Ready
- Unlisted links: create/revoke; view-only experience; lifecycle rules enforced
- Inbox: free cap 10; imports above cap show clear block + CTA
- Save-to-library: requires account + respects saved-flow cap; cap hit = delete or upgrade
- Import conflicts: missing moves/custom payloads trigger conflict resolution; user chooses how to handle
- Contradiction check: importing many flows cannot grant unlimited free practice (remains paywalled/credits-limited)

#### 8. M5 — Monetization + Paywalls Ready
- StoreKit: $9.99/month subscription; 7-day trial
- Restore purchases: works reliably after reinstall/new device
- Entitlements: trial behaves as Pro; single source of truth gating
- Paywalls: placements per CH25; upsell copy matches CH26 claims pages; results-may-vary language
- Downgrade: Pro ends = user keeps data but loses gated functionality; UX explains changes
- Receipt validation: minimal approach; failures degrade gracefully (no lockout on transient network)

#### 9. M6 — App Store Ready (Release Candidate)
- QA pass: CH35 scripts on latest iOS; all critical tests pass; known issues logged with severity
- Performance: acceptable cold start; no frequent crashes; memory stable on large flows
- Accessibility baseline: tap targets, dynamic type where feasible, VoiceOver labels for critical controls
- Privacy + compliance: privacy policy links; account deletion; data export; tracking disclosures match analytics
- App Store assets: icon (1024px), screenshots, preview video (optional), subtitle, keywords, description, support URL
- Legal: Terms + Privacy; UGC rules documented; reporting route if required
- Support: in-app "Contact support" path or email; troubleshooting doc (CH36) published
- Safety: warning ladder implemented; abuse limits enforced with soft warnings

#### 10. M7 — Launch + Monitoring
- Monitoring dashboard: crashes, purchase failures, share-link errors, sync conflicts, onboarding funnel metrics
- Incident playbook: triage steps, severity levels, rollback plan (Practice kill-switch), hotfix release procedure
- Release notes: v1.0 notes prepared; known limitations stated (iOS-only, portrait-only)
- Post-launch cadence: 48-72h patch window reserved; backlog grooming for v1.0.1

#### 11. App Store Submission Checklist

##### 11.1 Pre-Submission Artifacts
- Finalized bundle identifiers (name, bundle id, versioning scheme)
- App icon: 1024x1024; no alpha channel; readable at small size
- Screenshot set for required device sizes matching current UI
- Support URL: live; describes account, restoring purchases, sharing, practice, common errors
- Privacy policy URL: live; matches actual data behavior
- Test account credentials for App Review (if gated content)
- IAPs created in App Store Connect and linked to build

##### 11.2 App Store Connect Metadata
- **App name:** Handz
- **Subtitle:** (locked in CH00/branding chapter; TBD)
- **Keywords:** 100 characters; avoid competitor trademarks; include striking terms + combo + drills + gameplan
- **Description:** First 3 lines = benefit (faster decisions, smarter sparring); then features; then safety + results-may-vary
- **Promotional text:** Short value prop (optional)
- **Category:** Health & Fitness (or Sports) — finalize before submission
- **Age rating:** Set per content; include UGC considerations
- **Privacy nutrition label:** Must match actual SDK usage
- **App review notes:** Explain Guest limitations, demo flows access, paywall mechanics, test account

##### 11.3 Build Validation Before Upload
- Version numbers: increment build number; marketing version matches release (e.g., 1.0.0)
- Clean install test: fresh device/simulator; no stuck onboarding loops; guest restrictions messaging works
- Purchase test: trial start, cancel, renew, restore, offline edge-cases (defer gracefully)
- Core flow tests: create move; build flow with branches; save; share link; import to inbox; practice (gated)
- Limits tests: free flows capped at 2; inbox at 10; video storage at 2GB; soft warnings trigger
- Deletion/export: path reachable; data export works
- Crash sweep: major flows clean; crash logs empty

##### 11.4 Submission + Review Workflow
- Upload via Xcode/Transporter; processing passes
- Attach build to app version and subscription IAP
- Complete compliance questions (encryption, tracking)
- Submit for review; record timestamp + build number
- Rejection: capture reason verbatim, map to owning chapter, patch, resubmit with clear notes

#### 12. Release Readiness Regression Set (Minimum)
- Auth: sign in Apple; sign out; sign back in; session persists
- Guest: browse demo; cannot save; sees account CTA before building
- Flows: create; add 3 nodes; 2 branches; merge; save; reopen; edit; delete
- Caps: free = 2 flows; third save = upgrade path; no bypass via inbox
- Sharing: generate unlisted link; open on another device; import; inbox increments; cap at 10 enforced
- Practice: Pro user starts, pauses, resumes, ends early; summary; history; streak updates
- Paywall: free user taps Practice = paywall; trial start works; entitlement updates instantly
- Offline: airplane mode during edit; local draft persists; reconnect sync completes without data loss
- Crash: no crashes in above flows

#### 13. Rollback & Hotfix Plan
- **Remote kill-switch:** Disable Practice Mode entry points (optionally sharing) server-side
- **Server-side gating:** Plan state decisions server-authoritative where possible
- **Hotfix pipeline:** Patch build within 24 hours; minimal regression set required before upload
- **Data safety:** No destructive migrations on client start; backwards-compatible schema for V1.x
- **User messaging:** In-app banner for known issues + mitigation steps

#### 14. Risks & Mitigation (V1-Specific)
| Risk | Mitigation |
|------|------------|
| Complex flow builder bugs (corrupted graphs) | Validate graph invariants on every save; repair UI; autosave versions |
| Monetization regressions (StoreKit edge-cases, App Review rejection) | Purchase tests on every RC; consistent paywall copy; prominent restore |
| UGC safety (unexpected custom data in imports) | Sanitize inputs; cap payload sizes; inbox cap; soft warnings ladder |
| Offline sync conflicts (inconsistent states) | Single plan-state helper; deterministic conflict resolution |
| Performance (large graph lag) | Virtualization, memoization, max-render strategy; warn user when heavy |

#### 15. PROMPT 38 (Replit Build Prompt)
Creates:
- `/docs/release/` folder with: RELEASE_CHECKLIST.md, REGRESSION_MINIMUM.md, ROLLBACK_PLAYBOOK.md, APP_STORE_METADATA_TEMPLATE.md
- `/scripts/verify_limits.ts`: validates hard locks (free saved flows=2, inbox cap=10, video=2GB, credits not usable on inbox)
- `/scripts/smoke_test_checklist.md`: command-line runnable regression steps
- In-app hidden "Release Readiness" debug panel (dev builds only): plan state, caps, graph invariant check, copy diagnostics button
- CI: builds iOS, runs unit tests, runs verify_limits

#### 16. Troubleshooting Diagnostics (Operational)
- App won't launch: check M0; disable modules via flags; inspect crash log; confirm env vars
- Lost flows: verify offline/sync (CH28); check if Guest; verify account state; conflict resolution logs
- Free users practicing without paying: regression in plan-state helper; run verify_limits; confirm gating calls
- Paywall purchase fails: StoreKit product IDs match App Store Connect; signing; restore; receipt parsing; "Try again" path
- Share links fail to import: payload size limits; move-family ID mapping; inbox cap; server compatibility
- Flow builder weird on large flows: virtualization; validate graph; pan/zoom gesture conflicts; hit targets
- App Review rejection: map to checklist item; patch owning chapter; update review notes

#### 17. Change Management
- Every chapter PDF versioned (R1, R2...) with changelog, rationale, impacted chapters
- Locked decision constants (caps, pricing, gating) live in one code file
- Cap/pricing changes must update: CH00 locks, CH25 monetization, CH38 verify_limits + checklist
- Additive features = new chapters where possible; rewrites only if old spec is wrong

#### 18. Gate Sign-Off Template
- Gate: M__
- Date: ____
- Build number: ____
- Tester(s): ____
- What was tested: link to checklist or run log
- Known issues: list + severity + owner
- Decision: PASS / FAIL
- Notes: ____

---

## Cross-References

| Chapter | References To | References From |
|---------|--------------|-----------------|
| CH37 | CH00, CH04, CH05, CH06, CH07, CH08, CH09-CH14, CH15-CH22, CH23-CH27, CH28-CH34, CH35, CH36, CH38 | — |
| CH38 | CH00, CH02, CH04, CH05, CH06, CH07, CH08, CH12-CH22, CH25, CH26, CH27, CH28, CH29, CH30, CH31, CH32, CH33, CH34, CH35, CH36 | CH37 |

---

## Ambiguities & Placeholders

### CH37
- None explicitly marked; states "if future tooling constraints discovered, record as PLACEHOLDERs owned by CH37"

### CH38
1. **Exact target dates** — not specified
2. **Exact App Store metadata copy** — not finalized
3. **Final pricing validation checkpoints** — not defined
4. **Legal policy URLs** — not provided
5. **App subtitle** — "locked in CH00/branding chapter; update here when finalized"
6. **Category** — "Health & Fitness (or Sports) — finalize before submission"

---

## Assumptions Detected

### CH37
- Replit (or equivalent agentic coder) is the primary implementation tool
- React Native with Expo preferred for iOS build/export
- Prompts are reusable across different AI coding tools
- PRD bundle is the single source of truth; prompts are execution layer

### CH38
- Milestone gates are strict (no partial passes)
- iOS simulator + physical device must both build successfully
- App Store category likely Health & Fitness
- StoreKit 2 or equivalent modern subscription approach assumed
- Server-side plan state authority preferred where possible
- Kill-switch for Practice Mode is a required safety mechanism
- 24-hour hotfix turnaround capability assumed

---

## End of Batch 10B

**Chapters Covered:** CH37, CH38
**Next Batch:** None (final batch - all 39 chapters complete)

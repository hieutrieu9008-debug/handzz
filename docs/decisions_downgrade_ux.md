# Decision: Downgrade UX (Pro → Free)

**Date:** 2026-01-06  
**Status:** PRD-STRICT EXTRACTION  
**Mode:** No assumptions, no decisions, no invented values  
**Resolves:** ME-E01, related downgrade blockers

---

## LOCKED (Verbatim PRD Only)

### Downgraded State Definition — CH08 (L331)
| State | Definition | Source |
|-------|------------|--------|
| Downgraded | User was Pro/Trial, now Free. App must preserve existing data but enforce Free gates going forward (e.g., practice locked, flow cap behavior). | CH08 L331 |

### Trial End Behavior — CH08 (L324, L465)
| Rule | Behavior | Source |
|------|----------|--------|
| Trial end | Trial ends automatically; user becomes Pro (paid) if converted, otherwise downgrades to Free | CH08 L324 |
| Cancelled during trial | Pro entitlements remain active until trial end; then downgrade to Free automatically | CH25 L590 |

### Downgrade Enforcement Principle — CH08 (L466)
| Rule | Specification | Source |
|------|---------------|--------|
| Data preservation | If user downgrades to Free and has more than 2 saved flows, **do not delete data** | CH08 L466 |
| Cap enforcement | Enforce cap by making flows beyond cap **read-only** until upgrade or deletion | CH08 L466 |
| UX owner | CH15/CH25 | CH08 L466 |

### Downgrade Flow (Pro → Free) — CH25 (L740-743)
| Rule | Specification | Source |
|------|---------------|--------|
| Trigger | On entitlement refresh if Pro expired: immediately revert to Free caps/gates | CH25 L741 |
| Over-cap flows | If exceeds Free caps (>2 saved flows): do NOT delete; mark extra flows as "Locked (Pro)" view-only until upgrade or delete | CH25 L742 |
| Mid-session | If mid-session when downgrade: allow session to finish; mark future practice as gated | CH25 L743 |

### Free Plan Caps (Enforced on Downgrade) — CH08 (L379-381)
| Cap | Value | Source |
|-----|-------|--------|
| Saved flows | 2 | CH08 L379 (LOCKED) |
| Inbox items | 10 | CH08 L381 (LOCKED) |

### Free Entitlements (Post-Downgrade) — CH08 §4 (L390-437)
| Capability | Free | Source |
|------------|------|--------|
| Save flows | Yes (cap 2) | CH08 L394 |
| Practice saved flows | Yes (credits only) | CH08 L424 |
| Practice inbox items | No | CH08 L425 |
| Monthly practice credits | 3/month | CH08 L426 |
| Upload video | No | CH08 L435 |
| Create share links | Limited (placeholder) | CH08 L414 |

### Acceptance Test: Downgrade Behavior — CH08 (L520)
| Test | Given | When | Then | Source |
|------|-------|------|------|--------|
| Downgrade behavior | I was Pro and saved 10 flows | My plan becomes Free | My data remains intact and app enforces Free cap without deleting flows | CH08 L520 |

### Analytics Event: Downgrade Detected — CH08 (L508)
| Event | Parameters | Source |
|-------|------------|--------|
| downgrade_detected | previousPlan, newPlan, hasOverCapFlows | CH08 L508 |

### Downgrade and Mastery — CH23 (L157)
| Rule | Behavior | Source |
|------|----------|--------|
| If user downgrades | Surface helpful action "Want a quick review session now?" | CH23 L157 |

### Global Downgrade Principle — CH10b (L213)
| Principle | Specification | Source |
|-----------|---------------|--------|
| Pro ends | User keeps data but loses gated functionality; UX explains changes | CH10b L213 |

### Library Cap Enforcement at 2 Flows — CH15 (L878-882)
| Scenario | Behavior | Source |
|----------|----------|--------|
| Free at cap (2 flows) | Lists 2 flows normally | CH15 L879 |
| "+ New Flow" | Triggers hard-cap flow (CH30) | CH15 L880 |
| Helper text | "Free plan: 2 saved flows." | CH15 L881 |
| Updates | Dynamically on delete | CH15 L882 |

### Library 2-Flow Cap Enforcement — CH15 (L904-907)
| Rule | Specification | Source |
|------|---------------|--------|
| Triggers | Create, duplicate, save inbox item, save demo-as-copy, convert draft | CH15 L905 |
| At cap | CH30 hard block: Upgrade / Manage (delete) / Not now | CH15 L906 |
| Manage | Library Home in Manage Mode with helper "Delete one to save a new flow." | CH15 L907 |

### Plan Gating Copy — CH08 (L476)
| Gate Type | Copy | Source |
|-----------|------|--------|
| Flow cap gate (Free cap=2) | "You've reached the Free limit (2 saved flows)." Options: "Upgrade to Pro" and "Manage flows". | CH08 L476 |

### Practice Credit Gate Copy — CH08 (L477-478)
| Gate Type | Copy | Source |
|-----------|------|--------|
| Practice credit gate (credits remain) | "This practice uses 1 credit." | CH08 L477 |
| Practice credit gate (exhausted) | "Practice is a Pro feature." | CH08 L478 |

### Inbox Practice Gate Copy — CH08 (L479)
| Gate Type | Copy | Source |
|-----------|------|--------|
| Inbox practice gate (Free) | "Save this flow to your library to practice it." (then apply flow cap rules) | CH08 L479 |

### Upload Gate Copy — CH08 (L480)
| Gate Type | Copy | Source |
|-----------|------|--------|
| Upload gate (Free) | "Uploads are Pro-only. Use links to share videos." | CH08 L480 |

### Pro (grace) State — CH08 (L330)
| State | Definition | Source |
|-------|------------|--------|
| Pro (grace) | Subscription was active recently but cannot be verified due to network/StoreKit failure. Allow Pro features for short grace window (owned by CH28). Display non-blocking banner: "Can't verify subscription right now. Some Pro features may pause if this continues." | CH08 L330 |

### Degraded Mode: Entitlement Unverifiable — CH31 (L1000)
| Scenario | Behavior | Source |
|----------|----------|--------|
| Entitlements cannot be verified | Default to safer side (Free behavior) but show banner "Can't verify subscription — some Pro features may be locked until you reconnect." | CH31 L1000 |

---

## UNRESOLVED (PRD Provides Options, No Selection Made)

### Which Flows Become "Locked (Pro)" on Downgrade
- **PRD Reference:** CH25 L742 says "mark extra flows as 'Locked (Pro)' view-only"
- **PRD silent** on selection criteria (most recent 2 remain unlocked? oldest? user choice?)
- **Impact:** UX for determining which flows user can still edit

### Offline Grace Period Before Downgrade Effect
| Option | Source |
|--------|--------|
| 12h | CH25 L768 |
| 24h | CH25 L768 |
| 48h | CH25 L768 |

**PRD default suggestion:** 24h  
**Status:** PLACEHOLDER — Owner: CH28/CH08

---

## OPEN QUESTIONS (PRD Silent or Ambiguous)

### OQ-DG01: Which 2 flows remain editable after downgrade?
- **PRD Reference:** CH25 L742 says "mark extra flows as Locked (Pro)"
- **PRD Reference:** CH08 L466 says "read-only until upgrade or deletion"
- **PRD silent** on which flows are "extra" vs "kept"
- **Options:** Most recent 2 / Oldest 2 / User selects / All locked until managed
- **Impact:** Core downgrade UX

### OQ-DG02: What does "Locked (Pro)" flow look like?
- **PRD Reference:** CH25 L742 introduces "Locked (Pro)" label
- **PRD silent** on exact visual treatment (badge? overlay? grayed out?)
- **Impact:** Visual design

### OQ-DG03: Can user view/practice locked flows?
- **PRD Reference:** CH25 L742 says "view-only"
- **PRD Reference:** CH08 L466 says "read-only"
- **PRD silent** on whether practice is allowed for locked flows
- **Impact:** Value retention for downgraded users

### OQ-DG04: Can user share locked flows?
- **PRD Reference:** PRD silent on share behavior for locked flows
- **Options:** Block sharing / Allow sharing / Allow with warning
- **Impact:** Viral funnel for downgraded users

### OQ-DG05: Can user duplicate locked flows?
- **PRD Reference:** PRD silent on duplicate behavior
- **Implication:** Duplicating would create new flow, hitting cap
- **Impact:** User workflow

### OQ-DG06: What happens to active share links for locked flows?
- **PRD Reference:** PRD silent on share link status post-downgrade
- **Options:** Links stay active / Links revoked / Links stay but cannot create new
- **Impact:** Recipients of shared flows

### OQ-DG07: What happens to inbox items over 10 on downgrade?
- **PRD Reference:** CH08 L381 locks inbox cap at 10
- **PRD Reference:** CH08 L466 says "preserve data"
- **PRD silent** on inbox behavior when Pro had >10 items
- **Implication:** Similar to flows — preserve but block new?

### OQ-DG08: What happens to uploaded media on downgrade?
- **PRD Reference:** CH08 L435 says Free cannot upload
- **PRD Reference:** CH29 says uploads are Pro-only, 2GB cap
- **PRD silent** on whether existing uploads are preserved or deleted
- **Impact:** Data loss concern

### OQ-DG09: What happens to mastery/maintenance plans on downgrade?
- **PRD Reference:** CH23 L157 says "surface helpful action"
- **PRD silent** on whether plans are preserved, paused, or deleted
- **Impact:** User's planning work

### OQ-DG10: Is there a "downgrade warning" before trial/subscription ends?
- **PRD Reference:** CH27 describes N-BILLING-TRIAL-END notification (48h before)
- **PRD silent** on in-app downgrade warning experience
- **Impact:** User preparation

### OQ-DG11: What is the exact moment downgrade takes effect?
- **PRD Reference:** CH25 L741 says "immediately revert to Free caps/gates"
- **PRD silent** on exact timing (midnight? instant? grace?)
- **Impact:** Session continuity

### OQ-DG12: What happens to practice credits on downgrade?
- **PRD Reference:** Free gets 3 credits/month (CH08 L445)
- **PRD silent** on whether credits reset or carry over from Pro
- **Impact:** Day-1 Free experience after Pro

### OQ-DG13: How does user manage locked flows?
- **PRD Reference:** CH08 L466 says "deletion" is one path
- **PRD Reference:** CH15 L907 says "Manage Mode"
- **PRD silent** on exact UX for choosing which flows to keep
- **Impact:** Critical user flow

### OQ-DG14: Can user export locked flows?
- **PRD Reference:** CH34 describes export
- **PRD silent** on whether locked flows are exportable
- **Impact:** Data portability

---

## Downgrade Behavior Summary (PRD-Specified)

| Aspect | Before Downgrade (Pro) | After Downgrade (Free) | Source |
|--------|------------------------|------------------------|--------|
| Saved flows | Unlimited | 2 (extras "Locked (Pro)") | CH08 L466, CH25 L742 |
| Practice | Unlimited | 3 credits/month, saved flows only | CH08 L424-426 |
| Inbox practice | Yes | No | CH08 L425 |
| Uploads | 2GB cap | Not allowed | CH08 L435 |
| Share links | Higher limits | Limited (placeholder) | CH08 L414 |
| Data | All preserved | All preserved (LOCKED rule) | CH08 L466, CH10b L213 |

---

## PRD References

- CH08 L324: Trial definition and end behavior
- CH08 L330: Pro (grace) state
- CH08 L331: Downgraded state definition
- CH08 L379-381: Free caps (saved flows, inbox)
- CH08 L390-437: Free entitlements matrix
- CH08 L466: Downgrade enforcement principle
- CH08 L476-480: Gating copy
- CH08 L508: downgrade_detected analytics event
- CH08 L520: Acceptance test
- CH10b L213: Global downgrade principle
- CH15 L878-882: Library cap enforcement at 2 flows
- CH15 L904-907: Library 2-flow cap triggers
- CH23 L157: Downgrade and mastery
- CH25 L590: Trial cancellation behavior
- CH25 L740-743: Downgrade flow
- CH25 L768: Offline grace window placeholder
- CH31 L1000: Degraded mode

---

## RESOLVED DECISIONS

### DG-D01: Which Flows Remain Editable on Downgrade — LOCKED
| Decision | Selected Option |
|----------|-----------------|
| Flow selection on downgrade | **A) Most Recent 2** — automatically keep 2 most recently modified |

**Resolution Date:** 2026-01-06

**Selected Behavior:**
- On downgrade from Pro to Free with >2 saved flows:
  - The 2 most recently modified flows remain editable
  - All other flows become read-only "Locked (Pro)"
- Selection is automatic based on `updatedAt` timestamp
- No user intervention required at moment of downgrade

**Rationale:**
- Preserves flows user is most likely actively working on
- Minimizes disruption at an already negative UX moment
- Avoids forcing a management decision during downgrade
- Aligns with PRD intent to enforce caps via locking, not deletion (CH08 L466)

**User Path to Manage:**
- User can later delete flows to bring count to ≤2
- Deleting a locked flow makes room for another to become editable
- Or upgrade to Pro to unlock all

**Source:** CH08 L466, CH25 L742 interpreted with "most recent" selection.

---

### DG-D02: Visual Treatment for "Locked (Pro)" Flows — LOCKED
| Decision | Selected Option |
|----------|-----------------|
| Visual treatment | **D) Badge + Subtle Dim** |

**Resolution Date:** 2026-01-06

**Selected Behavior:**
- Locked flows display with:
  - Visible "Locked (Pro)" badge (lock icon + "Pro" label)
  - Subtle dimming of card (e.g., 70-80% opacity)
- Content remains visible and recognizable
- Treatment consistent across Library, Search, and Recents

**Rationale:**
- Clearly communicates restricted state without shaming
- Preserves content visibility and sense of ownership
- Avoids punitive or disabled-looking treatments
- Aligns with PRD guidance: "Explain plan gating without shaming" (CH30 L925)

**Visual Specification:**
| Element | Treatment |
|---------|-----------|
| Card opacity | ~80% (subtle dim) |
| Badge | Lock icon + "Pro" text in corner |
| Thumbnail | Visible but slightly dimmed |
| Title | Readable |
| Tap action | Opens flow in view-only mode |

**Source:** CH25 L742 "Locked (Pro)" label; CH30 L925 no-shame guidance.

---

### DG-D03: Practice Allowed for Locked Flows — LOCKED
| Decision | Selected Option |
|----------|-----------------|
| Practice for locked flows | **A) Yes with Credits** — locked flows are practicable |

**Resolution Date:** 2026-01-06

**Selected Behavior:**
- Locked flows CAN be practiced using credits
- Practice entry points remain visible on locked flow cards
- Editing, duplication, and modification remain blocked
- Practice available both online and offline (per existing offline rules)

**Rationale:**
- Preserves user value and respects ownership of created flows
- Aligns with PRD framing of locked = read-only (not disabled)
- Keeps practice credits meaningful across user's full library
- Avoids punitive downgrade behavior

**What "Locked (Pro)" Means:**
| Action | Allowed? |
|--------|----------|
| View flow | Yes |
| Practice with credits | Yes |
| Edit flow | No |
| Duplicate flow | No |
| Delete flow | Yes (frees slot) |
| Export flow | TBD (DG-related) |
| Share flow | TBD (DG-D04) |

**Source:** CH08 L466 "read-only" interpreted as edit-restricted, not action-disabled.

---

### DG-D04: Share Links Allowed for Locked Flows — LOCKED
| Decision | Selected Option |
|----------|-----------------|
| Share links for locked flows | **B) No (new blocked)** — cannot create new; can revoke existing |

**Resolution Date:** 2026-01-06

**Selected Behavior:**
- Existing share links for locked flows remain active
- Users may revoke existing links at any time
- Creation of new share links on locked flows is blocked
- Share links for the 2 editable flows remain fully functional

**Rationale:**
- Preserves user control by allowing revocation
- Prevents expansion of reach from over-cap flows
- Avoids retroactive punishment or breaking existing shares
- Provides clear Pro upgrade lever

**Blocked Action UX:**
- On tap "Share" for locked flow: show explanation
- Copy: "Share links for this flow require Pro. Upgrade to share, or manage your existing links."
- Actions: [Upgrade] [Manage Links] [Cancel]

**Summary of Locked Flow Actions:**
| Action | Allowed? |
|--------|----------|
| View flow | Yes |
| Practice with credits | Yes |
| Edit flow | No |
| Duplicate flow | No |
| Delete flow | Yes |
| Create share link | No |
| Revoke existing link | Yes |
| Export flow | TBD |

**Source:** CH08 L414 "Limited" share for Free; locked flows further restricted.

---

### DG-D05: Inbox Over-Cap Behavior on Downgrade — LOCKED
| Decision | Selected Option |
|----------|-----------------|
| Inbox over-cap on downgrade | **A) Preserve All** — keep all items; block new imports until under cap |

**Resolution Date:** 2026-01-06

**Selected Behavior:**
- All existing inbox items remain viewable after downgrade
- Free users cannot add new inbox items while over cap (10)
- Users may delete or save items to Library to get back under cap
- No forced deletion or blocking modal at downgrade moment

**Rationale:**
- Aligns with core PRD principle: "do not delete data" (CH08 L466)
- Consistent with flow downgrade behavior (preserve, lock, block new)
- Avoids punitive or high-friction downgrade UX
- Gives users control over when/how to manage excess

**At-Cap Behavior:**
| Scenario | Behavior |
|----------|----------|
| Downgrade with 15 inbox items | All 15 preserved; new imports blocked |
| User tries to import new | "Inbox full (15/10). Delete items or upgrade." |
| User deletes 6 items | Now at 9/10; can import again |

**Source:** CH08 L466 preservation principle; CH08 L381 inbox cap = 10.

---

### DG-D06: Uploaded Media on Downgrade — LOCKED
| Decision | Selected Option |
|----------|-----------------|
| Uploaded media on downgrade | **A) Preserve** — uploads preserved; viewable but no new uploads |

**Resolution Date:** 2026-01-06

**Selected Behavior:**
- All previously uploaded media remains accessible and viewable
- Free users cannot upload new media while downgraded
- Editing or replacing uploads remains blocked until upgrade
- Media references in flows remain intact

**Rationale:**
- Aligns with core PRD principle: "do not delete data" (CH08 L466)
- Preserves flow integrity and avoids breaking media references
- Maintains user trust and reduces friction on re-upgrade
- Does not weaken Pro value — Free still cannot upload new

**Media State After Downgrade:**
| Action | Allowed? |
|--------|----------|
| View existing uploads | Yes |
| Play existing videos | Yes |
| Upload new media | No |
| Replace existing upload | No |
| Delete existing upload | Yes (frees storage if re-upgrades) |

**Storage Note:**
- Preserved uploads continue counting toward user's 2GB cap
- If user re-upgrades, existing uploads are immediately accessible
- Storage meter shows "X of 2GB used" even when Free

**Source:** CH08 L466 preservation; CH08 L435 Free cannot upload.

---

### DG-D07: Mastery/Maintenance Plans on Downgrade — LOCKED
| Decision | Selected Option |
|----------|-----------------|
| Mastery/maintenance plans on downgrade | **A) Preserve** — plans preserved; continue working with credits |

**Resolution Date:** 2026-01-06

**Selected Behavior:**
- All existing gameplans and maintenance schedules remain intact
- Free users may continue following plans using available credits
- Plans referencing locked flows remain functional (per DG-D03)
- Editing or creating new plans subject to MS-D01 (pending)

**Rationale:**
- Aligns with core PRD principle: "do not delete data" (CH08 L466)
- Preserves continuity of training intent after downgrade
- Consistent with allowing practice on locked flows (DG-D03)
- Avoids unnecessary complexity of a "paused" state

**Plan State After Downgrade:**
| Action | Allowed? |
|--------|----------|
| View gameplans | Yes |
| Practice from gameplan | Yes (with credits) |
| Edit gameplan | TBD (MS-D01) |
| Create new gameplan | TBD (MS-D01) |
| Receive reminders | TBD (MS-D06) |

**Dependency:** MS-D06 (Free maintenance notifications) determines reminder behavior.

**Source:** CH08 L466 preservation; CH23 L157 downgrade helpful action.

---

### DG-D08: Downgrade Warning UX — LOCKED
| Decision | Selected Option |
|----------|-----------------|
| Downgrade warning UX | **C) Banner + Modal** — modal on first open, then persistent banner |

**Resolution Date:** 2026-01-06

**Selected Behavior:**
- One-time modal shown on first app open after downgrade
- After modal dismissal, persistent banner remains visible
- Banner includes concise summary and upgrade CTA
- Banner dismissible but reappears on app restart until user upgrades or manages

**Modal Content:**
| Element | Content |
|---------|---------|
| Title | "Your plan has changed" |
| Body | "You're now on the Free plan. You can still view all your flows, practice with credits, and keep your data. Some features are now limited." |
| What changed | • Editing limited to 2 flows • 3 practice credits/month • New uploads disabled |
| Primary CTA | "Upgrade to Pro" |
| Secondary | "Got it" (dismiss) |

**Banner Content:**
| Element | Content |
|---------|---------|
| Text | "Free plan active. Some features limited." |
| Action | "Upgrade" → Paywall |
| Dismiss | X (dismissible, reappears on restart) |

**Rationale:**
- Ensures users clearly understand what changed
- Prevents silent or confusing downgrade behavior
- Provides clear, respectful upgrade path without nagging
- Aligns with PRD transparency principles

**Source:** CH25 L741 downgrade trigger; modal/banner pattern from CH31 degraded mode.

---

### DG-D09: Credit State on Downgrade — LOCKED
| Decision | Selected Option |
|----------|-----------------|
| Credit state on downgrade | **A) Reset to 3** — fresh 3 credits |

**Resolution Date:** 2026-01-06

**Selected Behavior:**
- On downgrade, user's Free credit balance is set to 3
- Credits then follow normal calendar-month reset rules (CS-D03)
- No proration or restoration of past balances

**Rationale:**
- Aligns with Free plan mental model: "Free = 3 credits/month"
- Avoids tracking historical or legacy credit state
- Prevents late-month downgrade penalty
- Keeps downgrade behavior predictable and simple

**Example Scenarios:**
| Scenario | Credits After Downgrade |
|----------|------------------------|
| User downgrades on Jan 15 | 3 credits (reset on Feb 1) |
| User downgrades on Jan 30 | 3 credits (reset on Feb 1) |
| User was Pro for 2 years | 3 credits (no history lookup) |

**Source:** CH08 L445 "3 credits/month"; CS-D03 calendar reset.

---

### DG-D10: Flow Management UX on Downgrade — LOCKED
| Decision | Selected Option |
|----------|-----------------|
| Flow management UX on downgrade | **C) Manage Mode Prompt** — optional CTA in downgrade modal |

**Resolution Date:** 2026-01-06

**Selected Behavior:**
- On downgrade, most recent 2 flows remain editable (per DG-D01)
- Downgrade modal includes optional CTA: "Manage your flows"
- CTA opens Library in Manage Mode (CH15 L907)
- User can delete flows to bring total under Free cap
- No forced selection or blocking interaction

**Rationale:**
- Preserves low-friction auto-selection from DG-D01
- Avoids forcing decisions during downgrade moment
- Clearly surfaces user control without blocking usage
- Aligns with existing Library Manage Mode

**Downgrade Modal (Updated):**
| Element | Content |
|---------|---------|
| Title | "Your plan has changed" |
| Body | "You're now on the Free plan..." |
| Primary CTA | "Upgrade to Pro" |
| Secondary CTA | "Manage your flows" → Library Manage Mode |
| Tertiary | "Got it" (dismiss) |

**Manage Mode Behavior:**
- Shows all flows with locked/unlocked status
- Delete action available on any flow
- Deleting a locked flow may promote another to editable
- Helper text: "Delete flows to edit more. Free plan allows 2."

**Source:** CH15 L907 Manage Mode; DG-D01 auto-selection.

---

## Blocking Decisions Required

| ID | Decision | Options | Owner | Status |
|----|----------|---------|-------|--------|
| DG-D01 | Which flows remain editable on downgrade | Most recent 2 / Oldest 2 / User choice / All locked | PM/CH15 | **LOCKED: Most Recent 2** |
| DG-D02 | Visual treatment for "Locked (Pro)" flows | Badge / Overlay / Grayed / TBD | Design/CH15 | **LOCKED: Badge + Subtle Dim** |
| DG-D03 | Practice allowed for locked flows | Yes with credits / No | PM | **LOCKED: Yes with Credits** |
| DG-D04 | Share links allowed for locked flows | Yes / No / Existing only | PM/CH17 | **LOCKED: No new; revoke OK** |
| DG-D05 | Inbox over-cap behavior on downgrade | Preserve all / User must delete | PM/CH18 | **LOCKED: Preserve All** |
| DG-D06 | Uploaded media on downgrade | Preserve / Delete / Preserve but no new | PM/CH29 | **LOCKED: Preserve** |
| DG-D07 | Mastery/maintenance plans on downgrade | Preserve / Pause / Delete | PM/CH23-24 | **LOCKED: Preserve** |
| DG-D08 | Downgrade warning UX | Banner / Modal / None | PM/CH25 | **LOCKED: Banner + Modal** |
| DG-D09 | Credit state on downgrade | Reset to 3 / Carry unused / TBD | PM/CH08 | **LOCKED: Reset to 3** |
| DG-D10 | Flow management UX on downgrade | Auto-select / User selects / TBD | PM/CH15 | **LOCKED: Manage Mode Prompt** |

---

**End of PRD-Strict Extraction**

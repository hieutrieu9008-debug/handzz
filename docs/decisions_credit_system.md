# Decision: Credit & Reservation System

**Date:** 2026-01-06  
**Status:** PRD-STRICT EXTRACTION  
**Mode:** No assumptions, no decisions, no invented values  
**Resolves:** PM-C01, ME-C01, PM-B05, PM-E01

---

## Source Contradiction

The PRD contains conflicting statements on credit consumption timing:

| Source | Statement | Model |
|--------|-----------|-------|
| CH08 §5 (L446) | "Starting a Practice session on a saved flow consumes 1 credit" | Immediate Spend |
| CH20 L193-194 | "Credits consumed at moment user starts (tap Start), not when configuring" | Immediate Spend |
| CH22 L624-630 | "At session start: create credit reservation...At session end: if completedSetCount >= 1, consume...If completedSetCount == 0: release reservation; no credit consumed" | Reservation Model |

**PRD does NOT resolve this contradiction.** Owner must decide which model to adopt.

---

## LOCKED (Verbatim PRD Only)

### Credit Grant — CH08 §5 (L445)
| Rule | Value | Source |
|------|-------|--------|
| Credits per month | 3 | CH08 L445 |
| Plan eligibility | Free only | CH08 L445 |

### Credit Spend Scope — CH08 §5 (L446-447)
| Rule | Value | Source |
|------|-------|--------|
| Credits usable on | Saved flows only | CH08 L446 |
| Credits usable on inbox items | No | CH08 L446 |
| Credit covers | Entire session regardless of duration or number of paths selected | CH08 L447 |

### Credit Exhaustion — CH08 §5 (L448)
| Rule | Behavior | Source |
|------|----------|--------|
| When credits = 0 | Attempting to start practice triggers Paywall Gate (CH25) | CH08 L448 |

### Credit Anti-Exploit — CH08 §5 (L450)
| Rule | Behavior | Source |
|------|----------|--------|
| Transfer | Credits cannot be transferred | CH08 L450 |
| Reinstall exploit | Reinstalling does not reset credits | CH08 L450 |
| Binding | Credits are tied to account | CH08 L450 |

### Credit UI Surfaces — CH08 §5.1 (L452-457)
| Surface | Display | Source |
|---------|---------|--------|
| Practice tab header | Shows remaining credits (Free only) | CH08 L453 |
| Flow detail screen | Practice CTA shows either "Practice (1 credit)" or "Practice (Pro)" | CH08 L454 |
| Paywall | Includes short explanation that Pro removes credit limits | CH08 L455 |
| Settings > Subscription | Shows current plan and next renewal date (Pro/Trial) or upgrade CTA (Free) | CH08 L456 |

### Credit Confirm Modal — CH20 L213-218
| Field | Value | Source |
|-------|-------|--------|
| Title | "Use a practice credit?" | CH20 L214 |
| Body | "This session will use 1 credit. Credits reset each month." | CH20 L215 |
| Primary button | "Use credit & start" | CH20 L216 |
| Secondary button | "Cancel" | CH20 L217 |
| Link | "Upgrade for unlimited" -> PAYWALL (CH25) | CH20 L218 |

### Reservation Model (if adopted) — CH22 L624-630
| Rule | Behavior | Source |
|------|----------|--------|
| At session start | Create "credit reservation" attached to sessionId | CH22 L625 |
| At session end, completedSetCount >= 1 | Consume (creditConsumed=true) | CH22 L626 |
| At session end, completedSetCount == 0 | Release reservation; no credit consumed | CH22 L627 |
| App crash with reservation | On launch offer "Resume session" or "Discard"; Discard releases | CH22 L628 |

### Qualifying Practice Day — CH22 L600-604
| Rule | Value | Source |
|------|-------|--------|
| Qualifies if | completedSetCount >= 1 in any session | CH22 L601 |
| Date determination | Local-date from device timezone (deviceTimezoneAtStart) | CH22 L602 |
| Multiple sessions same day | Count as 1 practice day | CH22 L603 |

### Session Status Model — CH22 L537-541
| Status | Meaning | Source |
|--------|---------|--------|
| completed | Session ran through planned drill items, ended normally | CH22 L539 |
| ended_early | User intentionally ended via "End Practice"; may or may not have completed sets | CH22 L540 |
| interrupted | Session didn't end cleanly (app killed, crash, OS kill) | CH22 L541 |

### Plan Practice Eligibility — CH08 §4.4 (L421-428)
| Plan | Practice Capability | Source |
|------|---------------------|--------|
| Guest | No (cannot practice saved flows) | CH08 L424 |
| Free | Yes (credits only) | CH08 L424 |
| Pro/Trial | Yes (unlimited) | CH08 L424 |

### Plan Inbox Practice Eligibility — CH08 §4.4 (L425)
| Plan | Practice Inbox Items | Source |
|------|---------------------|--------|
| Guest | No | CH08 L425 |
| Free | No | CH08 L425 |
| Pro/Trial | Yes | CH08 L425 |

### Free vs Pro Practice Behavior — CH08 §4.4 (L426-428)
| Capability | Free | Pro/Trial | Source |
|------------|------|-----------|--------|
| Monthly practice credits | 3/month | N/A | CH08 L426 |
| Practice session length | No restriction | No restriction | CH08 L427 |
| Logging (actual duration) | Yes (credit sessions) | Yes | CH08 L428 |

---

## RESOLVED DECISIONS

### CS-D01: Credit Spend Model — LOCKED
| Decision | Selected Option |
|----------|-----------------|
| Credit spend timing | **B) Reservation Model** |

**Resolution Date:** 2026-01-06

**Selected Behavior:**
- Credit reserved on session start (tap Start)
- Credit consumed only if `completedSetCount >= 1`
- Credit released (not consumed) if `completedSetCount == 0`

**Rationale:**
- Aligns with PRD principles around fairness and avoiding anxiety-inducing penalties
- Prevents accidental credit loss on false starts, crashes, or interruptions
- Consistent with mastery model that rewards meaningful engagement
- Exploit risk to be handled via warning ladder and cooldown mechanisms (CH30), not credit spending

**Dependencies:**
- CS-D02 (reservation expiry window) — required to handle orphaned reservations ✓ RESOLVED
- Warning ladder thresholds (CH30) — required for exploit prevention

**Source Resolution:** Adopts CH22 L624-630 over CH08/CH20 immediate spend model.

---

### CS-D02: Reservation Expiry Window — LOCKED
| Decision | Selected Option |
|----------|-----------------|
| Reservation expiry window | **B) 12 hours** |

**Resolution Date:** 2026-01-06

**Selected Behavior:**
- Credit reservation auto-releases after 12 hours if session not completed or explicitly discarded
- On app launch with orphaned reservation: offer "Resume session" or "Discard"
- Discard action releases reservation immediately

**Rationale:**
- Balances user forgiveness with exploit containment
- Supports realistic mobile interruption and crash-recovery patterns
- Avoids punitive behavior of 1h window while preventing day-long credit parking

**Separation of Concerns:**
| Concept | Duration | Purpose |
|---------|----------|---------|
| Session interruption | 5 min background | Marks session as "interrupted" status |
| Reservation expiry | 12 hours | Releases unconsumed credit |
| Offline grace period | TBD (separate decision) | Pro entitlement verification |

**Source:** CH22 L716 placeholder default confirmed.

---

### CS-D03: Credit Reset Timing — LOCKED
| Decision | Selected Option |
|----------|-----------------|
| Credit reset timing | **A) Calendar Month** |

**Resolution Date:** 2026-01-06

**Selected Behavior:**
- All Free users' credits reset to 3 on the 1st of each month
- Reset occurs at midnight in the user's local time zone (deviceTimezoneAtStart)
- Late-month signups receive prorated first cycle (accepted tradeoff)

**Rationale:**
- Matches common user expectations for "monthly" limits
- Simplest and most predictable mental model
- Lowest support and UX burden (no personalized reset confusion)
- Aligns with existing copy ("Credits reset each month")
- Easier to message consistently across pricing, onboarding, and upgrade flows

**UI Copy Requirement:**
- All references should state "Credits refresh on the 1st" or "Credits reset monthly on the 1st"
- Avoid vague "monthly" without specifying the 1st

**Source:** CH08 L445 "monthly" interpreted as calendar month; CH25 L637 local timezone confirmed.

---

### CS-D04: Offline Practice for Free — LOCKED
| Decision | Selected Option |
|----------|-----------------|
| Offline practice for Free | **B) Allow with Risk** — bounded offline practice with reconciliation |

**Resolution Date:** 2026-01-06

**Selected Behavior:**
- Free users CAN start practice offline if:
  1. Content is cached locally
  2. Entitlements were recently validated (window defined by OFF-D04)
- Credits are optimistically consumed client-side
- On next sync, server is authoritative
- If server credits < offline usage: resolve conservatively (no negative credits)

**Rationale:**
- Aligns with real-world gym usage where connectivity is unreliable
- Consistent with PRD guidance (CH28 L363, CH25 L747)
- Preserves user trust and perceived value of Free credits
- Risk is acceptable and containable with strict validation windows

**Guardrails:**
| Rule | Behavior |
|------|----------|
| Content requirement | Flow must be cached locally |
| Validation window | Entitlements must be recently validated (OFF-D04 defines window) |
| Client consumption | Optimistic — decrement local credit count |
| Server authority | On sync, server credit count is truth |
| Desync resolution | If server < client consumed: no negative credits; surface explanation |
| Repeated desyncs | Warning ladder integration (CH30) for abuse patterns |

**Desync UX:**
- Non-punitive copy: "Your credits synced differently than expected. You have {X} credits remaining."
- Do NOT accuse user of exploit
- Provide path to upgrade or wait for next reset

**Dependencies:**
- OFF-D04: "Recently validated" entitlement window (TBD)
- Sync reconciliation UX and messaging
- Warning ladder (CH30) for repeated desync patterns

**Source:** CH28 L363, CH25 L747 interpreted as allowing bounded offline practice.

---

## UNRESOLVED (PRD Provides Options, No Selection Made)

### Credit Reservation Expiry Window — CH22 L629
| Option | Source |
|--------|--------|
| 1h | CH22 L716 (placeholder options) |
| 12h | CH22 L716 (placeholder default) |
| 24h | CH22 L716 (placeholder options) |

**PRD placeholder default is 12h but marked as TBD.**

### Practice Credit Reset Timing — CH08 L602-604
| Option | Implication |
|--------|-------------|
| Calendar month | All users reset on same day (e.g., 1st of month) |
| 30 days from signup | Each user has different reset date |

**PRD says "reset monthly" (CH08 L445) but does not specify which model.**

---

## OPEN QUESTIONS (PRD Silent or Ambiguous)

### OQ-CS01: Which credit model to adopt?
- **Contradiction:** CH08/CH20 (immediate spend) vs CH22 (reservation/release)
- **Impact:** Core credit lifecycle, refund behavior, analytics
- **Owner:** PM decision required

### OQ-CS02: What happens if user starts session, completes 0 sets, and session ends normally?
- **If Immediate Spend:** Credit already consumed, lost
- **If Reservation Model:** Credit released, user gets it back
- **PRD Reference:** CH22 L627 implies reservation model ("release reservation; no credit consumed")

### OQ-CS03: Can Free user start offline practice?
- **PRD Reference:** CH08 silent on offline credit verification
- **Risk:** Without server verification, credits could be exploited
- **Related:** CH28 owns offline behavior but does not specify credit handling

### OQ-CS04: What happens if Free user goes offline mid-session?
- **PRD Reference:** CH20 L275-278 discusses server confirmation on start
- **PRD Reference:** CH22 L650 mentions "if fail, non-blocking toast + local pending log for retry"
- **PRD does not specify:** Credit state when offline mid-session

### OQ-CS05: What if credit state desyncs (client shows 1, server shows 0)?
- **PRD Reference:** CH20 L271 "Credits desync (client=1, server=0): On Start, server is truth; if denied: Credits unavailable + paywall; do not start"
- **Question:** What about mid-session desync?

### OQ-CS06: What if user has active reservation and tries to start second session?
- **PRD silent** on concurrent session prevention
- **Implication:** Potential exploit if multiple reservations allowed

### OQ-CS07: What if user upgrades to Pro mid-session (Free with active reservation)?
- **PRD silent** on plan change during active session
- **Options:** Release reservation (Pro doesn't need), consume anyway, other

### OQ-CS08: What if user downgrades to Free mid-session (was Pro)?
- **PRD silent** on mid-session downgrade
- **PRD Reference:** CH08 L466 says "apply restrictions next time" for entitlement changes

### OQ-CS09: Credit display when reservation is active
- **PRD silent** on how to show credits during active session
- **Options:** Show as spent, show as "in use", show original count

### OQ-CS10: Monthly reset behavior during active session
- **PRD silent** on what happens if monthly reset occurs mid-session
- **Example:** User has 0 credits, midnight hits (new month), session in progress

---

## Data Model (PRD-Specified Fields Only)

### From CH22 L678-687 — practiceSession
```
sessionId, userId, createdAt, sessionStartAt, sessionEndAt, deviceTimezoneAtStart
status (completed | ended_early | interrupted)
plannedTotalSeconds, actualDurationMs, totalPausedMs
completedSetCount, skippedSetCount, assumedRepsTotal
qualifiesForStreak, streakDayLocal (YYYY-MM-DD)
creditConsumed, creditReservationId
startedFrom (practice_tab | flow_detail | gameplan)
planStateAtRun (guest | free | pro | trial)
hasOrphans, items[] (summary array)
```

### From CH08 §2 L346-359 — Entitlement Snapshot
```
planState: enum (guest | free | trial | pro | pro_grace)
practiceCredits: integer (Free only; resets monthly)
canPractice: boolean
canPracticeInboxItems: boolean
```

---

## Analytics Events (PRD-Specified Only)

### From CH08 §9 L507
| Event | Parameters | Source |
|-------|------------|--------|
| credit_spent | flowId, sessionId | CH08 L507 |

### From CH08 §9 L497
| Event | Parameters | Source |
|-------|------------|--------|
| entitlement_snapshot_loaded | planState, creditsRemaining, caps | CH08 L497 |

---

## PRD References

- CH08 §5 (L439-457): Practice Credits (Free)
- CH08 §4.4 (L421-428): Practice Mode entitlements
- CH08 §2 (L346-359): Entitlement snapshot fields
- CH08 §9 (L492-508): Analytics events
- CH20 L193-194: Credit spend timing (Step 4)
- CH20 L213-218: CREDIT_CONFIRM modal
- CH20 L271: Credits desync handling
- CH20 L275-278: Security (credit confirmation)
- CH22 L537-541: Session status model
- CH22 L600-604: Qualifying practice day
- CH22 L624-630: Credit reserve/consume model
- CH22 L678-687: practiceSession data model
- CH22 L716: Reservation expiry placeholder

---

## Blocking Decisions Required

| ID | Decision | Options | Owner | Status |
|----|----------|---------|-------|--------|
| CS-D01 | Credit spend model | Immediate (CH08/CH20) vs Reservation (CH22) | PM | **LOCKED: Reservation** |
| CS-D02 | Reservation expiry window | 1h / 12h / 24h | PM | **LOCKED: 12h** |
| CS-D03 | Credit reset timing | Calendar month / 30-day rolling | PM | **LOCKED: Calendar Month** |
| CS-D04 | Offline practice for Free | Block / Allow with risk | PM + Eng | **LOCKED: Allow with Risk** |

---

**End of PRD-Strict Extraction**

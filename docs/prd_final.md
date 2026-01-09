# Handz V1 PRD — Final Compilation

**Date:** 2026-01-06  
**Status:** FINAL — ALL DECISIONS LOCKED  
**Version:** prd-freeze-v1

---

## PURPOSE & V1 SCOPE

This document consolidates all Handz V1 Product Requirements and locked decisions from the PRD process. It serves as the single source of truth for implementation of the Handz V1 mobile application.

**V1 Scope:** Martial arts technique training application with:
- Practice mode with timer-based drills
- Flow builder for creating custom combinations
- Mastery and maintenance scheduling system  
- Sharing and inbox capabilities
- Subscription-based monetization (Free/Pro)
- Offline support with sync capabilities

---

## LOCKED DECISIONS BY THEME

### Credit System (4/4 LOCKED)

| ID | Decision | Resolution |
|----|----------|------------|
| CS-D01 | Credit spend model | Reservation Model (consume only if completedSetCount ≥ 1) |
| CS-D02 | Credit reservation expiry window | 12 hours |
| CS-D03 | Credit reset timing | Calendar Month (1st of month, local timezone) |
| CS-D04 | Offline practice for Free | Allow with Risk (bounded, reconcile on sync) |

**Key Rules:**
- Free users receive 3 credits per calendar month (resets 1st)
- Credits reserved on session start, consumed only if meaningful completion
- No negative credits possible
- Reservation expires after 12 hours if session not completed

### Offline & Sync (7/7 LOCKED)

| ID | Decision | Resolution |
|----|----------|------------|
| OFF-D01 | Pro offline grace period | 24 hours |
| OFF-D02 | Grace period expiry UX | Banner + Block (persistent explanation) |
| OFF-D03 | Free offline practice | Allow (via CS-D04) |
| OFF-D04 | "Recently validated" window | 12 hours |
| OFF-D05 | Offline cache budget | 10 flows OR 100 MB (hybrid) |
| OFF-D06 | Sync queue soft cap | 200 operations |
| OFF-D07 | Streak handling offline | Credit on Sync (backdate to session date) |

**Key Rules:**
- Pro users get 24h grace period with banner then block
- Free users get 12h validation window for offline practice
- Hybrid cache: 10 flows OR 100MB (whichever hit first)
- Streaks credit on sync, not during offline period
- Queue limited to 200 pending operations

### Downgrade UX (10/10 LOCKED)

| ID | Decision | Resolution |
|----|----------|------------|
| DG-D01 | Which flows remain editable | Most Recent 2 (by updatedAt) |
| DG-D02 | Visual treatment for locked flows | Badge + Subtle Dim |
| DG-D03 | Practice on locked flows | Yes with Credits |
| DG-D04 | Share links on locked flows | No new; existing remain; revoke OK |
| DG-D05 | Inbox over-cap on downgrade | Preserve All; block new imports |
| DG-D06 | Uploaded media on downgrade | Preserve; viewable; no new uploads |
| DG-D07 | Mastery plans on downgrade | Preserve; continue usable with credits |
| DG-D08 | Downgrade warning UX | Modal on first open + persistent banner |
| DG-D09 | Credit state on downgrade | Reset to 3 credits |
| DG-D10 | Flow management UX | Optional "Manage Mode" prompt |

**Key Rules:**
- Never delete user data during downgrade
- Most recent 2 flows remain editable, others locked
- Locked flows practiceable with credits (not free)
- All inbox/media/mastery data preserved
- Credits reset to 3 on downgrade
- Clear modal + banner UX for user awareness

### Mastery Scope (3/3 LOCKED)

| ID | Decision | Resolution |
|----|----------|------------|
| MS-D01 | Free can create Gameplans | Yes, unlimited |
| MS-D02 | Free can view Mastery Hub | Full access, with clear Pro gating language |
| MS-D03 | Free can view Maintenance Hub | Full access, clearly labeled as Pro-powered |

**Key Rules:**
- Free users can plan and organize without limits
- Full visibility into mastery states and maintenance needs
- Execution gated by credits/plan tiers
- Consistent "show value, gate execution" philosophy

### Sharing Limits (13/13 LOCKED)

| ID | Decision | Resolution |
|----|----------|------------|
| SL-D01 | Free daily share link creation | 10 per 24h |
| SL-D02 | Pro daily share link creation | 50 per 24h |
| SL-D03 | Free active link cap | 25 |
| SL-D04 | Pro active link cap | 250 |
| SL-D05 | Per-flow active link cap | None (unlimited) |
| SL-D06 | Pro inbox cap | 200 |
| SL-D07 | Snapshot payload size limit | 512 KB |
| SL-D08 | Imported flow node limit | 300 |
| SL-D09 | Active share links per flow | Unlimited |
| SL-D10 | Share link creation rate limit | 20 per minute |
| SL-D11 | Import save rate limit | 30 per minute |
| SL-D12 | L1 warning threshold | 80% of cap |
| SL-D13 | Import exceeds node limit behavior | Block entirely (no truncation) |

**Key Rules:**
- Rolling 24h windows for daily limits
- Burst protection with rate limits per minute
- Clear progressive warning ladder (80% L1 threshold)
- No per-flow caps, only overall totals
- Block over truncate for large imports

### Warning Ladder / Abuse Controls (13/13 LOCKED)

| ID | Decision | Resolution |
|----|----------|------------|
| WL-D01 | Ladder structure | Progressive escalation (L0 → L5) |
| WL-D02 | Cooldown escalation | Linear: 15 → 30 → 45 minutes |
| WL-D03 | Cooldown reset window | 48 hours |
| WL-D04 | Cooldown trigger | 3 rapid attempts after L2 |
| WL-D05 | Temporary suspension duration | 24 hours |
| WL-D06 | Suspension trigger | 5 escalations within 7 days |
| WL-D07 | Share creation burst limit | 10 per minute |
| WL-D08 | Share open rate limit | 100 per minute per IP/device |
| WL-D09 | Import L1 threshold | 8 imports per hour |
| WL-D10 | Import L2 threshold | 15 imports/hour → confirmation |
| WL-D11 | Import L3 threshold | 30 imports/hour → 30-min cooldown |
| WL-D12 | Import L4 suspension | 24-hour suspension |
| WL-D13 | Abuse philosophy | Prefer cooldowns over permanent bans |

**Key Rules:**
- Progressive escalation from L0 (normal) to L5 (security block)
- Linear cooldown increases: 15min → 30min → 45min for L3
- 48-hour reset window for clean behavior
- Per-vector escalation (doesn't affect other features)
- Favor temporary cooldowns over permanent bans

### Mastery Algorithms (12/12 LOCKED)

| ID | Decision | Resolution |
|----|----------|------------|
| MA-D01 | Sessions to reach "On Track" | 3 sessions |
| MA-D02 | Sessions to reach "Maintained" | 6 sessions |
| MA-D03 | Days before "Needs Review" | 14 days |
| MA-D04 | Days before "Stale" | 30 days |
| MA-D05 | Stale recovery penalty | -2 sessions |
| MA-D06 | Light cadence | 14 days |
| MA-D07 | Standard cadence | 7 days |
| MA-D08 | Aggressive cadence | 3 days |
| MA-D09 | Max maintenance interval | 30 days |
| MA-D10 | Light intensity profile | 1x/week, low sets, short duration |
| MA-D11 | Standard intensity profile | 2-3x/week, moderate sets |
| MA-D12 | Aggressive intensity profile | 4-5x/week, high intensity |

**State Transition Logic:**
```
Not Started → In Progress: 1st session
In Progress → On Track: After 3 sessions
On Track → Maintained: After 6 sessions
On Track/Maintained → Needs Review: After 14 days
Needs Review → Stale: After 30 days
Stale → In Progress: Practice with -2 session penalty
```

**Cadence Rules:**
- Light: Every 14 days (hobbyists)
- Standard: Every 7 days (most users)
- Aggressive: Every 3 days (fighters)
- Maximum interval: 30 days (safety ceiling)

### Export Formats (6/6 LOCKED)

| ID | Decision | Resolution |
|----|----------|------------|
| EX-D01 | V1 export formats | PDF, JSON |
| EX-D02 | Free export access | PDF only, watermarked |
| EX-D03 | Pro export access | PDF + JSON, no watermark |
| EX-D04 | Export rate limits | 3/day soft, 10/day hard |
| EX-D05 | Export size limit | 10 MB |
| EX-D06 | Watermark policy | Free only |

**Key Rules:**
- JSON includes full data package (CSV + metadata)
- PDF suitable for sharing/printing
- Free gets watermarked PDFs (Pro gets clean)
- Rate limits prevent abuse: 3/day with confirmation, 10/day hard
- 10MB size limit (media excluded from count)

### StoreKit / Subscription (8/8 LOCKED)

| ID | Decision | Resolution |
|----|----------|------------|
| SK-D01 | Billing grace period | 3 days |
| SK-D02 | Grace period UX | Banner with explanation |
| SK-D03 | Subscription validation frequency | Launch + every 24h |
| SK-D04 | Offline subscription handling | Respect 24h Pro grace |
| SK-D05 | Family Sharing support | Not supported in V1 |
| SK-D06 | Promotional offers | Not supported in V1 |
| SK-D07 | Upgrades/downgrades handling | Immediate entitlement change |
| SK-D08 | Refund handling | Handled by Apple; reflect on next sync |

**Key Rules:**
- 3-day billing grace period during Apple's retry window
- Banner + persistent explanation during grace
- Daily subscription validation with caching
- 24-hour offline Pro grace (matches OFF-D01)
- No Family Sharing or promo codes in V1
- Immediate entitlement changes on upgrade/downgrade
- Refunds processed entirely by Apple systems

---

## SYSTEM RULES SUMMARY

### Credit System Rules
- 3 credits/month for Free (resets 1st, local timezone)
- Reservation model prevents accidental credit loss
- Credits usable only on saved flows, not inbox items
- Server authoritative on credit counts

### Offline Behavior Rules
- Pro: 24h grace with banner → block
- Free: 12h validation window for offline practice
- Hybrid cache budget: 10 flows OR 100MB
- Streaks credit on sync with backdating
- Sync queue limited to 200 operations

### Entitlement Rules
- Free: 2 saved flows, 10 inbox items, unlimited gameplans
- Pro: Unlimited practice, uploads, sharing
- Trial: Full Pro access during 7-day period
- Clear "show value, gate execution" philosophy

### Data Preservation Rules
- Never delete user data on downgrade or plan changes
- Preserve all: flows, inbox items, media, mastery data
- Convert restrictions to view-only when appropriate
- Maintain data integrity across account lifecycle

### Abuse Prevention Rules
- Progressive warning ladder (L0-L5) per feature
- Rate limits on creation and consumption
- Burst protection with temporary cooldowns
- Favor temporary measures over permanent bans
- Transparent UX with clear explanations and recovery paths

---

## IMPLEMENTATION REFERENCES

All detailed specifications and data models are available in individual decision documents:
- `docs/decisions_*.md` (theme-specific decisions)
- `docs/_needs_approval/backlog.md` (complete summary)
- Original PRD PDFs (CH00-CH38) for detailed specifications

---

**Status:** Complete and Ready for Implementation  
**Owner:** Hieu  
**Completion Date:** 2026-01-06  
**Tag:** prd-freeze-v1
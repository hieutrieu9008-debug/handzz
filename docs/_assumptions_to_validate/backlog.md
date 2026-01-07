# Assumptions Backlog — Needs Validation

Items below were introduced without PRD support.
Each must be confirmed, removed, or converted to explicit design question.

---

## From: decisions_sharing_limits.md

| ID | Assumption | PRD Gap | Risk if Wrong | Status |
|----|------------|---------|---------------|--------|
| SL-A01 | Edge limit: 500 edges | PRD only specifies node limit | May block valid flows or allow too many | PENDING |
| SL-A02 | Pro inbox soft warning at 180 | Free threshold (8/10) specified; Pro extrapolated to 90% | May feel too early or too late | PENDING |
| SL-A03 | L1 warning at 80% threshold (8 Free, 40 Pro) | PRD says "approaching" without number | May annoy or miss users | PENDING |
| SL-A04 | L3 cooldown: 15 minutes | PRD gives 15-60 range | May be too short or too long | PENDING |
| SL-A05 | L3 trigger: 3+ attempts after L2 | PRD says "rapid repetition" without count | False positives or misses | PENDING |
| SL-A06 | L4 trigger: 5+ times after L3 | Not specified | May be too aggressive or lenient | PENDING |
| SL-A07 | Import L1: 8/hour | Not specified | May block legitimate bulk imports | PENDING |
| SL-A08 | Import L2: 15/hour, confirmation required | PRD says "burst" without threshold | May be too aggressive | PENDING |
| SL-A09 | Import L3: 30/hour, 30-minute cooldown | Not specified | May disrupt coaches | PENDING |
| SL-A10 | Import L4: 24-hour suspension | Not specified | May be too punitive | PENDING |
| SL-A11 | Share link creation rate: 10/minute | PRD says "rate limit" without number | May block power users or miss bots | PENDING |
| SL-A12 | Share link opens rate: 100/minute/IP | PRD says "rate limit" without number | May block popular content | PENDING |
| SL-A13 | Inbox deletion rate: 20/minute | Not mentioned | Unclear if needed | PENDING |
| SL-A14 | Link revocation rate: 20/minute | Not mentioned | Unclear if needed | PENDING |
| SL-A15 | Queue excess requests (vs block) for non-abusive overages | Behavior invented | May confuse users if delayed | PENDING |
| SL-A16 | Revocation latency < 1 second | PRD says "immediate" | Interpretation may differ | PENDING |
| SL-A17 | Import-delete cycle L2 threshold: 5 cycles | Not specified | May miss or over-flag abuse | PENDING |
| SL-A18 | All modal/error copy text | PRD provides some templates; specific copy invented | May not match brand voice | PENDING |
| SL-A19 | UI treatment (grayed buttons, disabled states) | Not specified | May not match design system | PENDING |
| SL-A20 | Rationales for all limits | All invented | Reasoning may not hold | PENDING |
| SL-A21 | Configuration schema structure | Not in PRD | May not match actual architecture | PENDING |
| SL-A22 | "Learn more" FAQ link on error screens | Not specified | May not exist | PENDING |

---

## From: decisions_credit_system.md

| ID | Assumption | PRD Gap | Risk if Wrong | Status |
|----|------------|---------|---------------|--------|
| CS-A01 | 12h expiry is sufficient for overnight interruptions | PRD offers 1h/12h/24h | May lose user credits unfairly | PENDING |
| CS-A02 | Monthly reset uses calendar month, not 30-day rolling | ME-B06 is TBD | Different behavior for users | PENDING |
| CS-A03 | One reservation per user at a time is acceptable | Not specified | May block legitimate concurrent use | PENDING |
| CS-A04 | Offline mid-session sync conflicts are rare | Not specified | May need conflict UI | PENDING |
| CS-A05 | UX copy for credit confirmation modals | Templates exist but specific copy invented | May not match brand | PENDING |
| CS-A06 | Analytics event names | Not in PRD | May not match event taxonomy | PENDING |
| CS-A07 | Data model schemas (creditReservation, userCredits) | Structure invented | May not match architecture | PENDING |

---

## From: decisions_mastery_scope.md

| ID | Assumption | PRD Gap | Risk if Wrong | Status |
|----|------------|---------|---------------|--------|
| MS-A01 | Free users will create gameplans even without unlimited practice | Not validated | Low engagement if wrong | PENDING |
| MS-A02 | Seeing "due" items without ability to practice creates conversion urgency | Not validated | May just frustrate users | PENDING |
| MS-A03 | 1 credit per session (regardless of items) is understood by users | Not validated | Confusion about value | PENDING |
| MS-A04 | Maintenance notifications to Free users drive engagement, not churn | Not validated | Users may disable or churn | PENDING |
| MS-A05 | Snooze is acceptable escape valve (won't be abused indefinitely) | Not validated | Mastery becomes meaningless | PENDING |
| MS-A06 | UX copy for 0-credit maintenance paywall | Invented | May not match brand | PENDING |
| MS-A07 | Downgrade banner copy | Invented | May not match brand | PENDING |
| MS-A08 | Notification names (N-MAINT-DUE-TODAY, etc.) | Invented | May not match event taxonomy | PENDING |

---

## From: decisions_mastery_algorithms.md

| ID | Assumption | PRD Gap | Risk if Wrong | Status |
|----|------------|---------|---------------|--------|
| MA-A01 | 14 days is appropriate "Needs Review" threshold | PRD says TBD | May need adjustment for different sports | PENDING |
| MA-A02 | 3 sessions is meaningful "On Track" milestone | PRD says TBD | May feel too easy or too hard | PENDING |
| MA-A03 | -2 session penalty for Stale recovery feels fair | Invented | May discourage returning users | PENDING |
| MA-A04 | Auto cadence algorithm produces reasonable results | Algorithm invented | May need tuning | PENDING |
| MA-A05 | Urgency score sorting feels intuitive | Algorithm invented | May confuse users | PENDING |
| MA-A06 | Intensity profiles map to real training patterns | Values invented | May not match actual use | PENDING |
| MA-A07 | Decay evaluation at local midnight | Not specified | Timezone edge cases | PENDING |
| MA-A08 | Multiplier curve (0.5x, 1.0x, 1.25x, 1.5x) for next_review | Invented | May produce poor intervals | PENDING |
| MA-A09 | Urgency score modifiers (+10 high, -10 low, etc.) | Invented | May not produce intuitive sort | PENDING |
| MA-A10 | UI badge colors (Gray, Blue, Green, Yellow, Orange) | Not specified | May not match design system | PENDING |
| MA-A11 | State machine pseudocode | Invented | May not match implementation | PENDING |
| MA-A12 | Data model schemas (MasteryRecord, MaintenanceItem updates) | Structure invented | May not match architecture | PENDING |

---

## Summary

| Source File | Pending Assumptions |
|-------------|---------------------|
| decisions_sharing_limits.md | 22 |
| decisions_credit_system.md | 7 |
| decisions_mastery_scope.md | 8 |
| decisions_mastery_algorithms.md | 12 |
| **Total** | **49** |

---

Owner: Hieu

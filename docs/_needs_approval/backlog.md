# Decisions Backlog — COMPLETE

**Status:** ALL DECISIONS LOCKED  
**Date:** 2026-01-06

---

## LOCKED DECISIONS SUMMARY

### Credit System (4/4 LOCKED)
| ID | Item | Resolution | Status |
|----|------|------------|--------|
| CS-D01 | Credit spend model | Reservation Model (consume only if completedSetCount ≥ 1) | **LOCKED** |
| CS-D02 | Credit reservation expiry window | 12 hours | **LOCKED** |
| CS-D03 | Credit reset timing | Calendar Month (1st of month, local timezone) | **LOCKED** |
| CS-D04 | Offline practice for Free | Allow with Risk (bounded, reconcile on sync) | **LOCKED** |

### Offline & Sync (7/7 LOCKED)
| ID | Item | Resolution | Status |
|----|------|------------|--------|
| OFF-D01 | Pro offline grace period | 24 hours | **LOCKED** |
| OFF-D02 | Grace period expiry UX | Banner + Block (persistent explanation) | **LOCKED** |
| OFF-D03 | Free offline practice | Allow (via CS-D04) | **LOCKED** |
| OFF-D04 | "Recently validated" window | 12 hours | **LOCKED** |
| OFF-D05 | Offline cache budget | 10 flows OR 100 MB (hybrid) | **LOCKED** |
| OFF-D06 | Sync queue soft cap | 200 operations | **LOCKED** |
| OFF-D07 | Streak handling offline | Credit on Sync (backdate to session date) | **LOCKED** |

### Downgrade UX (10/10 LOCKED)
| ID | Item | Resolution | Status |
|----|------|------------|--------|
| DG-D01 | Which flows remain editable | Most Recent 2 (by updatedAt) | **LOCKED** |
| DG-D02 | Visual treatment for locked flows | Badge + Subtle Dim | **LOCKED** |
| DG-D03 | Practice on locked flows | Yes with Credits | **LOCKED** |
| DG-D04 | Share links on locked flows | No new; existing remain; revoke OK | **LOCKED** |
| DG-D05 | Inbox over-cap on downgrade | Preserve All; block new imports | **LOCKED** |
| DG-D06 | Uploaded media on downgrade | Preserve; viewable; no new uploads | **LOCKED** |
| DG-D07 | Mastery plans on downgrade | Preserve; continue usable with credits | **LOCKED** |
| DG-D08 | Downgrade warning UX | Modal on first open + persistent banner | **LOCKED** |
| DG-D09 | Credit state on downgrade | Reset to 3 credits | **LOCKED** |
| DG-D10 | Flow management UX | Optional "Manage Mode" prompt | **LOCKED** |

### Mastery Scope (3/3 LOCKED)
| ID | Item | Resolution | Status |
|----|------|------------|--------|
| MS-D01 | Free can create Gameplans | Yes, unlimited | **LOCKED** |
| MS-D02 | Free can view Mastery Hub | Full access, with clear Pro gating language | **LOCKED** |
| MS-D03 | Free can view Maintenance Hub | Full access, clearly labeled as Pro-powered | **LOCKED** |

### Sharing Limits (13/13 LOCKED)
| ID | Item | Resolution | Status |
|----|------|------------|--------|
| SL-D01 | Free daily share link creation | 10 per 24h | **LOCKED** |
| SL-D02 | Pro daily share link creation | 50 per 24h | **LOCKED** |
| SL-D03 | Free active link cap | 25 | **LOCKED** |
| SL-D04 | Pro active link cap | 250 | **LOCKED** |
| SL-D05 | Per-flow active link cap | None (unlimited) | **LOCKED** |
| SL-D06 | Pro inbox cap | 200 | **LOCKED** |
| SL-D07 | Snapshot payload size limit | 512 KB | **LOCKED** |
| SL-D08 | Imported flow node limit | 300 | **LOCKED** |
| SL-D09 | Active share links per flow | Unlimited | **LOCKED** |
| SL-D10 | Share link creation rate limit | 20 per minute | **LOCKED** |
| SL-D11 | Import save rate limit | 30 per minute | **LOCKED** |
| SL-D12 | L1 warning threshold | 80% of cap | **LOCKED** |
| SL-D13 | Import exceeds node limit | Block entirely (no truncation) | **LOCKED** |

### Warning Ladder / Abuse Controls (13/13 LOCKED)
| ID | Item | Resolution | Status |
|----|------|------------|--------|
| WL-D01 | Ladder structure | Progressive escalation (L0 → L5) | **LOCKED** |
| WL-D02 | Cooldown escalation | Linear: 15 → 30 → 45 minutes | **LOCKED** |
| WL-D03 | Cooldown reset window | 48 hours | **LOCKED** |
| WL-D04 | Cooldown trigger | 3 rapid attempts after L2 | **LOCKED** |
| WL-D05 | Temporary suspension duration | 24 hours | **LOCKED** |
| WL-D06 | Suspension trigger | 5 escalations within 7 days | **LOCKED** |
| WL-D07 | Share creation burst limit | 10 per minute | **LOCKED** |
| WL-D08 | Share open rate limit | 100 per minute per IP/device | **LOCKED** |
| WL-D09 | Import L1 threshold | 8 imports per hour | **LOCKED** |
| WL-D10 | Import L2 threshold | 15 imports/hour → confirmation | **LOCKED** |
| WL-D11 | Import L3 threshold | 30 imports/hour → 30-min cooldown | **LOCKED** |
| WL-D12 | Import L4 suspension | 24-hour suspension | **LOCKED** |
| WL-D13 | Abuse philosophy | Prefer cooldowns over permanent bans | **LOCKED** |

### Mastery Algorithms (12/12 LOCKED)
| ID | Item | Resolution | Status |
|----|------|------------|--------|
| MA-D01 | Sessions to reach "On Track" | 3 sessions | **LOCKED** |
| MA-D02 | Sessions to reach "Maintained" | 6 sessions | **LOCKED** |
| MA-D03 | Days before "Needs Review" | 14 days | **LOCKED** |
| MA-D04 | Days before "Stale" | 30 days | **LOCKED** |
| MA-D05 | Stale recovery penalty | -2 sessions | **LOCKED** |
| MA-D06 | Light cadence | 14 days | **LOCKED** |
| MA-D07 | Standard cadence | 7 days | **LOCKED** |
| MA-D08 | Aggressive cadence | 3 days | **LOCKED** |
| MA-D09 | Max maintenance interval | 30 days | **LOCKED** |
| MA-D10 | Light intensity profile | 1x/week, low sets, short duration | **LOCKED** |
| MA-D11 | Standard intensity profile | 2-3x/week, moderate sets | **LOCKED** |
| MA-D12 | Aggressive intensity profile | 4-5x/week, high intensity | **LOCKED** |

### Export Formats (6/6 LOCKED)
| ID | Item | Resolution | Status |
|----|------|------------|--------|
| EX-D01 | V1 export formats | PDF, JSON | **LOCKED** |
| EX-D02 | Free export access | PDF only, watermarked | **LOCKED** |
| EX-D03 | Pro export access | PDF + JSON, no watermark | **LOCKED** |
| EX-D04 | Export rate limits | 3/day soft, 10/day hard | **LOCKED** |
| EX-D05 | Export size limit | 10 MB | **LOCKED** |
| EX-D06 | Watermark policy | Free only | **LOCKED** |

### StoreKit / Subscription (8/8 LOCKED)
| ID | Item | Resolution | Status |
|----|------|------------|--------|
| SK-D01 | Billing grace period | 3 days | **LOCKED** |
| SK-D02 | Grace period UX | Banner with explanation | **LOCKED** |
| SK-D03 | Subscription validation frequency | Launch + every 24h | **LOCKED** |
| SK-D04 | Offline subscription handling | Respect 24h Pro grace | **LOCKED** |
| SK-D05 | Family Sharing support | Not supported in V1 | **LOCKED** |
| SK-D06 | Promotional offers | Not supported in V1 | **LOCKED** |
| SK-D07 | Upgrades/downgrades handling | Immediate entitlement change | **LOCKED** |
| SK-D08 | Refund handling | Handled by Apple; reflect on next sync | **LOCKED** |

---

## Summary

| Source File | Locked | Pending |
|-------------|--------|---------|
| decisions_credit_system.md | 4 | 0 |
| decisions_offline_sync_behavior.md | 7 | 0 |
| decisions_downgrade_ux.md | 10 | 0 |
| decisions_mastery_scope.md | 3 | 0 |
| decisions_sharing_limits.md | 13 | 0 |
| decisions_warning_ladder_thresholds.md | 13 | 0 |
| decisions_mastery_algorithms.md | 12 | 0 |
| decisions_export_formats_availability.md | 6 | 0 |
| decisions_storekit_subscription_gaps.md | 8 | 0 |
| **Total** | **76** | **0** |

---

## Core Principles Established

- Never delete user data
- Server is authoritative on sync
- No negative credits
- Transparent UX with visible explanations
- No punitive treatment for legitimate offline use
- Prefer cooldowns over permanent bans
- Show value, gate execution (for Free users)

---

## DECISION RESOLUTION COMPLETE

All 76 blocking decisions have been locked and documented.

Owner: Hieu  
Completion Date: 2026-01-06

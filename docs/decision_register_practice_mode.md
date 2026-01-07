# Decision Register: Practice Mode (Domain 02)

Generated: 2026-01-05
Source: PRD Batch 06 (CH20–CH22), Batch 07 (CH23–CH24), Batch 02b (CH08)
Status: Flagged items — awaiting resolution

---

## 1. Unresolved Decisions

### 1.1 Blocker

| ID | Item | Options | Default | Owner | Source |
|----|------|---------|---------|-------|--------|
| PM-B01 | Time per set default | 3:00 / 5:00 | TBD | CH20 | CH20 §Placeholders |
| PM-B02 | Demo flow practice allowed | Allow / Block | TBD | CH20 | CH20 §Placeholders |
| PM-B03 | Max drill items per session | 100 / other | 100 | CH30/CH12 | CH20 §Placeholders |
| PM-B04 | Background timeout before interrupted | 120 / 300 / 600 sec | 300 | CH21 | CH21 §Placeholders |
| PM-B05 | Credit reservation expiry window | 1h / 12h / 24h | 12h | CH22 | CH22 §Placeholders |
| PM-B06 | Exact mastery thresholds (numerical) | TBD | TBD | CH23 | CH23 §Placeholders |
| PM-B07 | Spaced-repetition schedule parameters | TBD | TBD | CH23 | CH23 §Placeholders |
| PM-B08 | Mastery planning Pro-only vs Free-limited | TBD | TBD | CH08/CH25 | CH23 §Placeholders |
| PM-B09 | Default maintenance capacity | 3 items/week / 6 items/week / minutes-based | 3 items/week | CH24 | CH24 §Placeholders |
| PM-B10 | Overdue threshold for "Behind" status | 3 / 7 / 14 items | 7 items | CH24 | CH24 §Placeholders |

### 1.2 Important

| ID | Item | Options | Default | Owner | Source |
|----|------|---------|---------|-------|--------|
| PM-I01 | Audio countdown default (last 3s ticks) | on / off | off | CH21 | CH21 §Placeholders |
| PM-I02 | Audio cue default (set/rest start sounds) | on / off | on | CH21 | CH21 §Placeholders |
| PM-I03 | Undo completed set toast | on / off | on | CH21 | CH21 §Placeholders |
| PM-I04 | Add Set in-session control (+1 set) | include / exclude in V1 | include (+1 only) | CH21 | CH21 §Placeholders |
| PM-I05 | Inter-item rest inclusion in planned duration | include / exclude | include (if CH21 defines) | CH22 | CH22 §Placeholders |
| PM-I06 | Pro-rating reps on skipped sets | 0 reps / time-proportional / user-editable | 0 reps | CH22 | CH22 §Placeholders |
| PM-I07 | History search in V1 | none / basic / advanced | none | CH22 | CH22 §Placeholders |
| PM-I08 | Record types expansion | keep simple / expand with mastery-linked | keep simple | CH22 | CH22 §Placeholders |
| PM-I09 | "Strict camp" mode | enabled / disabled | placeholder | CH23 | CH23 §Placeholders |
| PM-I10 | Copy mastery progress into duplicate | allow / disallow | do not copy | CH23 | CH23 §Placeholders |
| PM-I11 | Cadence presets | 3/7/14 days / 2/5/10 days / user-defined only | 3/7/14 days | CH24 | CH24 §Placeholders |
| PM-I12 | Global vs per-gameplan maintenance plans | global only / allow per-gameplan / multiple plans | allow per-gameplan | CH24 | CH24 §Placeholders |
| PM-I13 | Pro gating for maintenance autoplan/reminders | autoplan for all / autoplan Pro-only / reminders Pro-only | autoplan for all | CH24 | CH24 §Placeholders |
| PM-I14 | Save-as-Preset for session setups | Off in V1 / Pro-only | Off | CH22/CH23 | CH20 §Placeholders |

### 1.3 Nice-to-have

| ID | Item | Options | Default | Owner | Source |
|----|------|---------|---------|-------|--------|
| PM-N01 | Practice Demo content pack (exact content) | TBD | TBD | CH20/CH05 | CH25 §Placeholders |
| PM-N02 | Offline grace window for Pro | 12h / 24h / 48h | 24h | CH28/CH08 | CH25 §Placeholders |

---

## 2. Placeholders

| ID | Location | Placeholder | Impact | Source |
|----|----------|-------------|--------|--------|
| PM-P01 | CH20 §Step 0 | Demo Practice option | Guest/Free value preview undefined | CH20 L48 |
| PM-P02 | CH20 §Step 3 | Hard caps (max drill items, session plan size) | Performance/stability limits undefined | CH20 L181-184 |
| PM-P03 | CH21 | Wake lock behavior on manual screen lock | Unclear if session pauses or continues | CH21 L436-437 |
| PM-P04 | CH22 | Qualifying practice day definition | completedSetCount >= 1 (confirmed) but edge cases unclear | CH22 L600-605 |
| PM-P05 | CH23 | Path difficulty proxy (move types) | Blocked on move taxonomy (CH09/CH10) | CH23 L135 |
| PM-P06 | CH24 | Capacity model default numbers | Conservative defaults undefined | CH24 L371 |
| PM-P07 | CH24 | "Fight camp" goal type for maintenance | Listed as "optional future placeholder" | CH24 L365 |

---

## 3. Contradictions / Ambiguities

| ID | Issue | Location A | Location B | Severity |
|----|-------|------------|------------|----------|
| PM-C01 | Credit spend timing: CH20 says "at moment user starts (tap Start)" but CH22 says "at session start" with reservation model | CH20 §Step 4 (L193-194) | CH22 §Plan Gating (L624-630) | Blocker |
| PM-C02 | Interrupted session credit: CH22 says credit remains spent; CH25 says "interruptions do not refund" but reservation model allows discard | CH22 L628-629 | CH25 L640-641 | Important |
| PM-C03 | Free can create Gameplans (CH23) but mastery planning may be Pro-only (CH08/CH25) — scope unclear | CH23 L194 | CH23 L258 | Blocker |
| PM-C04 | Maintenance sessions consume "same entitlements/credits as practice" but CH24 says session consumes credit "if available" — what if 0 credits? | CH24 L293 | CH24 L420 | Blocker |
| PM-C05 | Inter-item rest: CH22 says "if CH21 defines" but CH21 does not explicitly define inter-item rest (only inter-set rest) | CH22 L552-553 | CH21 L399-401 | Important |

---

## 4. Cross-Chapter Dependencies

### 4.1 Blocking Dependencies (Practice Mode needs other batches)

| ID | Blocked Item | Depends On | Batch | Severity |
|----|--------------|------------|-------|----------|
| PM-D01 | Practice Setup validation | CH30 Warning Ladder (hard caps) | Batch 08 | Blocker |
| PM-D02 | Offline session persistence | CH28 Offline/Sync | Batch 08 | Blocker |
| PM-D03 | Practice credit anti-exploit | CH29 Data Storage (server-side reset) | Batch 08 | Blocker |
| PM-D04 | Paywall integration | CH25 Paywalls | Batch 07 | Blocker |
| PM-D05 | Notification scheduling | CH27 Notifications | Batch 08 | Important |
| PM-D06 | Path enumeration from flows | CH12/CH13 Flow Builder | Batch 04 | Blocker |
| PM-D07 | Error state handling | CH31 Error States | Batch 08 | Important |
| PM-D08 | Analytics events | CH33 Analytics | Batch 09 | Nice-to-have |

### 4.2 Internal Dependencies (Within Practice Domain)

| ID | Source | Target | Nature |
|----|--------|--------|--------|
| PM-D09 | CH20 (Setup) | CH21 (Active Session) | Session Plan output → Session input |
| PM-D10 | CH21 (Active Session) | CH22 (Logging) | Session data contract |
| PM-D11 | CH22 (Logging) | CH23 (Mastery) | Practice logs → Mastery state updates |
| PM-D12 | CH23 (Mastery) | CH24 (Maintenance) | Mastery signals → Maintenance scheduling |
| PM-D13 | CH23 (Gameplans) | CH20 (Setup) | Gameplan prefill → Practice Setup |
| PM-D14 | CH24 (Maintenance) | CH20 (Setup) | Maintenance session → Practice pipeline |

---

## 5. Edge Cases Requiring Decision

| ID | Scenario | Question | Source | Severity |
|----|----------|----------|--------|----------|
| PM-E01 | App crash with credit reservation | Resume vs Discard clears reservation, but what if user force-closes? | CH22 L628-629 | Blocker |
| PM-E02 | Free user at 0 credits + maintenance due | Block maintenance session or allow viewing only? | CH24 L420 | Blocker |
| PM-E03 | Flow deleted mid-setup (Step 1-4) | Revalidate at Review; disabled items — but what about mid-session? | CH20 L272-273 | Important |
| PM-E04 | Multiple gameplans with conflicting intensity | "Use higher intensity when practicing from that gameplan" — how to resolve at session level? | CH23 L236 | Important |
| PM-E05 | Orphaned path after flow edit | MasteryRecord frozen until resolved — but maintenance reminders still fire? | CH23 L226 | Important |
| PM-E06 | Timezone shift causing instant overdue | CH24 says "rebase due dates to local midnight" but no algorithm specified | CH24 L515 | Important |
| PM-E07 | Session with 0 completed sets | Credit spent (per CH25) but doesn't qualify for streak (per CH22) | CH22 L604, CH25 L640 | Important |
| PM-E08 | Credits desync (client=1, server=0) | Server is truth; deny start — but what about offline? | CH20 L271 | Important |
| PM-E09 | Backlog > threshold + user continues ignoring | Supportive banner shown but no escalation path defined | CH24 L408 | Nice-to-have |
| PM-E10 | Entitlement lost mid-session | "Allow session to finish; apply restrictions next time" — but what about maintenance context? | CH21 L448-449 | Important |

---

## 6. Entitlement / Degradation Rules

### 6.1 Practice Credits (Free Plan)

| Rule | Source | Status |
|------|--------|--------|
| 3 credits per month | CH08 §5 | LOCKED |
| Reset monthly on local calendar date | CH25 L638 | LOCKED |
| Usable only on saved flows | CH08 §5 | LOCKED |
| Cannot practice inbox items | CH08 §5 | LOCKED |
| Credit spent on session start | CH25 L639 | LOCKED |
| Credit not refunded on interruption | CH25 L640-641 | LOCKED |
| Credits tied to account (no exploit) | CH08 §5 | LOCKED |

### 6.2 Unresolved Entitlement Questions

| ID | Question | Owner | Severity |
|----|----------|-------|----------|
| PM-E11 | Can Free users create Gameplans without limit? | CH08/CH25 | Blocker |
| PM-E12 | Can Free users use Mastery Hub (view-only) or is it Pro-gated? | CH08/CH25 | Blocker |
| PM-E13 | Can Free users access Maintenance Hub without credits? | CH24/CH25 | Blocker |
| PM-E14 | Does maintenance session count as 1 credit regardless of items included? | CH24/CH08 | Blocker |

---

## 7. Validation Gaps

| ID | Area | Gap | Source | Severity |
|----|------|-----|--------|----------|
| PM-V01 | Sets per item | No min/max defined | CH20 §Step 3 | Important |
| PM-V02 | Time per set | No min/max defined (only default options) | CH20 §Step 3 | Important |
| PM-V03 | Rest duration | No min/max defined | CH20 §Step 3 | Important |
| PM-V04 | Assumed reps per set | Soft cap at 50 but no hard max | CH20 L179 | Nice-to-have |
| PM-V05 | Gameplan name | No max length specified | CH23 §Gameplan | Important |
| PM-V06 | Maintenance item label | No max length specified | CH24 §Data Model | Nice-to-have |

---

## 8. Data Contract Gaps

| ID | Contract | Gap | Source | Severity |
|----|----------|-----|--------|----------|
| PM-DC01 | Session Plan (CH20→CH21) | nodeIds marked "optional in setup; CH21 can expand" — expansion logic undefined | CH20 L255 | Important |
| PM-DC02 | Session Output (CH21→CH22) | userActions[] field listed but schema undefined | CH22 L519 | Nice-to-have |
| PM-DC03 | MasteryRecord | next_review_due_at marked "computed" but algorithm undefined | CH23 L218 | Blocker |
| PM-DC04 | MaintenanceItem | cadence_model "auto" undefined | CH24 L468 | Blocker |

---

## Summary Statistics

| Category | Blocker | Important | Nice-to-have | Total |
|----------|---------|-----------|--------------|-------|
| Unresolved Decisions | 10 | 14 | 2 | 26 |
| Placeholders | — | — | — | 7 |
| Contradictions | 3 | 2 | — | 5 |
| Cross-Dependencies | 6 | 2 | 1 | 9 |
| Edge Cases | 2 | 7 | 1 | 10 |
| Entitlement Questions | 4 | — | — | 4 |
| Validation Gaps | — | 4 | 2 | 6 |
| Data Contract Gaps | 2 | 1 | 1 | 4 |
| **Total** | **27** | **30** | **7** | **64** |

---

## Next Steps

1. Resolve credit reservation vs immediate spend contradiction (PM-C01)
2. Clarify Free plan scope for Mastery/Maintenance features (PM-E11–E14)
3. Define exact mastery thresholds before implementation (PM-B06)
4. Coordinate with CH30 on hard caps for session stability (PM-B03)
5. Finalize maintenance cadence algorithm (PM-DC04)
6. Align CH21/CH22 on inter-item rest definition (PM-C05)

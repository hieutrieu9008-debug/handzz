# Master Blocker List

Generated: 2026-01-05
Source: Decision Registers (Domains 01–04)
Status: Consolidated blockers — awaiting resolution

---

## Summary

| Domain | Blockers | High Risk | Medium Risk |
|--------|----------|-----------|-------------|
| Flow Builder | 11 | 4 | 7 |
| Practice Mode | 27 | 12 | 15 |
| Monetization | 17 | 8 | 9 |
| Sharing & Inbox | 14 | 6 | 8 |
| **Total** | **69** | **30** | **39** |

---

## 1. Limits & Caps

### High Risk

| ID | Blocker | Options | Owner | Source |
|----|---------|---------|-------|--------|
| FB-B01 | Max nodes/edges per flow per plan | TBD | CH30/CH29 | Flow Builder |
| SI-B07 | Snapshot payload size limit | 256KB / 512KB / 1MB | CH30 | Sharing |
| SI-B08 | Imported flow node limit | 150 / 300 / 500 | CH30 | Sharing |
| PM-B03 | Max drill items per session | 100 / other | CH30/CH12 | Practice Mode |

### Medium Risk

| ID | Blocker | Options | Owner | Source |
|----|---------|---------|-------|--------|
| SI-B01 | Daily link creation cap (Free) | 5 / 10 / 20 per 24h | CH17 | Sharing |
| SI-B02 | Daily link creation cap (Pro) | 25 / 50 / 100 per 24h | CH17 | Sharing |
| SI-B03 | Active link cap (Free) | 10 / 25 / 50 | CH17 | Sharing |
| SI-B04 | Active link cap (Pro) | 100 / 250 / 500 | CH17 | Sharing |
| SI-B05 | Per-flow active link cap | none / 3 / 5 | CH17 | Sharing |
| SI-B06 | Pro inbox cap | 50 / 200 / Unlimited | CH08/CH30 | Sharing |
| ME-B03 | Pro saved-flow soft cap | TBD | CH15/CH30 | Monetization |
| ME-B04 | Pro inbox cap | 50 / 200 / unlimited | CH18/CH30 | Monetization |

---

## 2. Entitlements & Plan Gating

### High Risk

| ID | Blocker | Options | Owner | Source |
|----|---------|---------|-------|--------|
| PM-B08 | Mastery planning Pro-only vs Free-limited | TBD | CH08/CH25 | Practice Mode |
| PM-E11 | Can Free users create Gameplans without limit? | TBD | CH08/CH25 | Practice Mode |
| PM-E12 | Can Free users use Mastery Hub (view-only)? | TBD | CH08/CH25 | Practice Mode |
| PM-E13 | Can Free users access Maintenance Hub without credits? | TBD | CH24/CH25 | Practice Mode |
| PM-E14 | Does maintenance session count as 1 credit? | TBD | CH24/CH08 | Practice Mode |
| ME-E01 | Downgrade Pro→Free: which flows become read-only? | first 2 by date / user choice | CH08 | Monetization |
| ME-WL01 | Share-link creation: exact caps for Free/Pro | TBD | CH17/CH30 | Monetization |

### Medium Risk

| ID | Blocker | Options | Owner | Source |
|----|---------|---------|-------|--------|
| ME-B01 | Pro share-link daily cap | Soft caps / Hard daily limit | CH17/CH30 | Monetization |
| ME-B02 | Free share-link limits | TBD | CH17 | Monetization |
| ME-B06 | Practice credit reset timing | Calendar month / 30 days | CH08 | Monetization |
| ME-B07 | Annual plan pricing | None in V1 / Add with discount | CH25 | Monetization |
| PM-C03 | Free Gameplan creation vs Pro-only mastery | Scope unclear | CH08/CH25 | Practice Mode |
| PM-C04 | Maintenance session at 0 credits | Block / Allow viewing only | CH24/CH08 | Practice Mode |
| PM-E02 | Free at 0 credits + maintenance due | Block / Allow viewing | CH24 | Practice Mode |

---

## 3. Credit & Reservation System

### High Risk

| ID | Blocker | Issue | Owner | Source |
|----|---------|-------|-------|--------|
| PM-C01 | Credit spend timing contradiction | CH20 says "tap Start"; CH22 has reservation model | CH20/CH22 | Practice Mode |
| ME-C01 | Credit spend timing contradiction | CH08 says "session start"; CH22 has reservation/release | CH08/CH22 | Monetization |
| PM-B05 | Credit reservation expiry window | 1h / 12h / 24h | CH22 | Practice Mode |
| PM-E01 | App crash with credit reservation | Resume vs Discard behavior | CH22 | Practice Mode |

### Medium Risk

| ID | Blocker | Options | Owner | Source |
|----|---------|---------|-------|--------|
| ME-SK05 | Grace period for billing issues | TBD | CH08/CH28 | Monetization |

---

## 4. Performance & Stability

### High Risk

| ID | Blocker | Issue | Owner | Source |
|----|---------|-------|-------|--------|
| FB-E05 | Large flow (>150 nodes) degradation | Fallback behaviors undefined | CH12 | Flow Builder |
| SI-E05 | Import package exceeds node limit | Block entirely or truncate? | CH30 | Sharing |

### Medium Risk

| ID | Blocker | Options | Owner | Source |
|----|---------|---------|-------|--------|
| PM-B04 | Background timeout before interrupted | 120 / 300 / 600 sec | CH21 | Practice Mode |
| PM-B01 | Time per set default | 3:00 / 5:00 | CH20 | Practice Mode |

---

## 5. Data & Algorithm Definitions

### High Risk

| ID | Blocker | Issue | Owner | Source |
|----|---------|-------|-------|--------|
| PM-B06 | Exact mastery thresholds (numerical) | TBD | CH23 | Practice Mode |
| PM-B07 | Spaced-repetition schedule parameters | TBD | CH23 | Practice Mode |
| PM-DC03 | MasteryRecord.next_review_due_at algorithm | Marked "computed" but undefined | CH23 | Practice Mode |
| PM-DC04 | MaintenanceItem.cadence_model "auto" | Algorithm undefined | CH24 | Practice Mode |
| SI-D02 | Canonical ID matching algorithm | Depends on CH10 | CH10 | Sharing |

### Medium Risk

| ID | Blocker | Options | Owner | Source |
|----|---------|---------|-------|--------|
| PM-B09 | Default maintenance capacity | 3 / 6 items/week / minutes-based | CH24 | Practice Mode |
| PM-B10 | Overdue threshold for "Behind" status | 3 / 7 / 14 items | CH24 | Practice Mode |
| FB-B02 | Soft cycles for drill loops | TBD | CH20/CH21 | Flow Builder |
| SI-C03 | Pro inbox cap decision | 50 / 200 / Unlimited | CH08/CH18 | Sharing |

---

## 6. UX & Feature Decisions

### High Risk

| ID | Blocker | Issue | Owner | Source |
|----|---------|-------|-------|--------|
| PM-B02 | Demo flow practice allowed | Allow / Block | CH20 | Practice Mode |
| FB-B04 | Export formats and limits | TBD | CH29/CH25 | Flow Builder |
| FB-C04 | Export availability per plan | TBD for both Free and Pro | CH16 | Flow Builder |

### Medium Risk

| ID | Blocker | Options | Owner | Source |
|----|---------|---------|-------|--------|
| FB-B03 | Show sequence notes during active practice | Off / View-only / Edit | CH21 | Flow Builder |
| SI-E03 | Free at inbox cap (10) + Add to Library | Block Save to Inbox but allow direct Add? | CH18 | Sharing |
| ME-E08 | Offline Pro exceeds grace period | Degrade silently or warn first? | CH28/CH08 | Monetization |

---

## 7. Cross-Domain Dependencies (Blocking)

### High Risk

| ID | Blocker | Blocked By | Source |
|----|---------|------------|--------|
| FB-D01 | Max nodes/edges per plan | CH30 Warning Ladder | Flow Builder |
| FB-D06 | Paywall flows | CH25 Paywalls | Flow Builder |
| FB-D08 | Practice entry gating | CH20 Practice Setup | Flow Builder |
| PM-D01 | Practice Setup validation | CH30 Warning Ladder | Practice Mode |
| PM-D02 | Offline session persistence | CH28 Offline/Sync | Practice Mode |
| PM-D03 | Practice credit anti-exploit | CH29 Data Storage | Practice Mode |
| PM-D04 | Paywall integration | CH25 Paywalls | Practice Mode |
| PM-D06 | Path enumeration from flows | CH12/CH13 Flow Builder | Practice Mode |
| ME-D01 | Share-link caps enforcement | CH17 Sharing | Monetization |
| ME-D02 | Inbox cap enforcement | CH18 Inbox | Monetization |
| ME-D03 | Paywall copy and layout | CH25 (finalized) | Monetization |
| ME-D04 | Offline grace period | CH28 Offline/Sync | Monetization |
| ME-D05 | Warning ladder thresholds | CH30 Safety/Abuse | Monetization |
| SI-D01 | Link creation caps | CH08 Entitlements | Sharing |
| SI-D03 | Abuse limits enforcement | CH30 Warning Ladder | Sharing |

### Medium Risk

| ID | Blocker | Blocked By | Source |
|----|---------|------------|--------|
| FB-D02 | Soft cycles for drills | CH20/CH21 Practice Mode | Flow Builder |
| FB-D03 | Share link limits | CH17 Sharing + CH30 | Flow Builder |
| ME-B05 | Offline subscription grace period | CH28 Offline/Sync | Monetization |

---

## 8. Security & Anti-Abuse

### Medium Risk

| ID | Blocker | Issue | Owner | Source |
|----|---------|-------|-------|--------|
| SI-B07 | Snapshot payload size limit | Anti-abuse threshold | CH30 | Sharing |
| SI-B08 | Imported flow node limit | Anti-abuse threshold | CH30 | Sharing |

---

## Priority Resolution Order

### Tier 1: Must resolve before implementation starts
1. **Credit system contradiction** (PM-C01, ME-C01) — blocks Practice Mode
2. **Free plan scope for Mastery/Maintenance** (PM-E11–E14, PM-B08) — blocks feature gating
3. **Mastery thresholds and algorithms** (PM-B06, PM-B07, PM-DC03, PM-DC04) — blocks core logic
4. **Share-link caps** (SI-B01–B05, ME-WL01) — blocks sharing feature

### Tier 2: Must resolve before feature completion
5. **Flow/node limits** (FB-B01, SI-B07, SI-B08) — blocks performance testing
6. **Downgrade UX** (ME-E01) — blocks subscription flow
7. **Export formats** (FB-B04, FB-C04) — blocks export feature
8. **Demo practice** (PM-B02) — blocks onboarding

### Tier 3: Must resolve before beta
9. **Offline grace period** (ME-B05) — blocks offline testing
10. **Background timeout** (PM-B04) — blocks session reliability
11. **Credit reservation expiry** (PM-B05) — blocks edge case handling
12. **Pro inbox cap** (SI-B06, SI-C03, ME-B04) — blocks Pro testing

---

## Cross-Reference Index

| Domain | Register File | Blocker Count |
|--------|---------------|---------------|
| Flow Builder | `decision_register_flow_builder.md` | 11 |
| Practice Mode | `decision_register_practice_mode.md` | 27 |
| Monetization | `decision_register_monetization.md` | 17 |
| Sharing & Inbox | `decision_register_sharing_inbox.md` | 14 |

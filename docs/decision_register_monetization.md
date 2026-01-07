# Decision Register: Monetization & Entitlements (Domain 03)

Generated: 2026-01-05
Source: PRD Batch 02b (CH07–CH08), Batch 07 (CH25), Batch 08 (CH29–CH30)
Status: Flagged items — awaiting resolution

---

## 1. Unresolved Decisions

### 1.1 Blocker

| ID | Item | Options | Default | Owner | Source |
|----|------|---------|---------|-------|--------|
| ME-B01 | Pro share-link daily cap | Soft caps only / Hard daily limit | Soft caps only | CH17/CH30 | CH08 §Placeholders |
| ME-B02 | Free share-link limits | TBD | TBD | CH17 | CH08 §4.3 |
| ME-B03 | Pro saved-flow soft cap | TBD | TBD | CH15/CH30 | CH08 §Placeholders |
| ME-B04 | Pro inbox cap | 50 / 200 / unlimited (soft) | 200 | CH18/CH30 | CH29 §Placeholders |
| ME-B05 | Offline subscription verification grace period | 12h / 24h / 48h | 24h | CH28/CH08 | CH25 §Placeholders |
| ME-B06 | Practice credit reset timing | Calendar month / 30 days from signup | TBD | CH08 | CH08 §Placeholders |
| ME-B07 | Annual plan pricing | None in V1 / Add with discount | None in V1 | CH25 | CH25 §Placeholders |

### 1.2 Important

| ID | Item | Options | Default | Owner | Source |
|----|------|---------|---------|-------|--------|
| ME-I01 | Handle feature enablement | Enabled in V1 / Deferred | TBD | CH07 | CH07 §4 |
| ME-I02 | Email verification strictness | Current (save allowed, share blocked) / Stricter | Current | CH07 | CH07 §6.4 |
| ME-I03 | Practice demo for Guests | Enabled / Disabled | TBD | CH25 | CH07 §5.1 |
| ME-I04 | Max upload file size | 50MB / 100MB / 250MB | 100MB | CH29 | CH29 §Placeholders |
| ME-I05 | Allowed upload types | video-only / video+gif | video-only | CH29 | CH29 §Placeholders |
| ME-I06 | Max link attachments per move/flow | 1 / 3 / unlimited | 3 | CH11/CH16 | CH29 §Placeholders |
| ME-I07 | Text field caps (notes, sequence details) | 500 / 2000 / 10000 chars | 2000 | CH14/CH13 | CH29 §Placeholders |
| ME-I08 | Share-link creation caps + thresholds | TBD | TBD | CH17/CH25 | CH30 §Placeholders |
| ME-I09 | Cooldown durations per warning level | TBD | TBD | CH30 | CH30 §Placeholders |
| ME-I10 | Edit/import rate thresholds | TBD | TBD | CH30 | CH30 §Placeholders |
| ME-I11 | Share payload media policy for links | Include previews / TBD | TBD | CH17/CH19 | CH29 §Placeholders |
| ME-I12 | Export data formats and scopes | TBD | TBD | CH34 | CH08 §Placeholders |
| ME-I13 | Intro offer beyond trial | TBD | TBD | CH25 | CH25 §Placeholders |
| ME-I14 | Paywall A/B variants | TBD | TBD | CH25 | CH25 §Placeholders |

### 1.3 Nice-to-have

| ID | Item | Options | Default | Owner | Source |
|----|------|---------|---------|-------|--------|
| ME-N01 | Password blacklist implementation | Size and source TBD | TBD | CH07 | CH07 §8.3 |
| ME-N02 | Login throttling thresholds | TBD | TBD | CH30 | CH07 §10 |
| ME-N03 | Route ID reconciliation (CH05 vs CH07) | TBD | TBD | CH05/CH07 | CH07 §Placeholders |
| ME-N04 | Admin tooling scope | TBD | TBD | CH30 | CH30 §Placeholders |

---

## 2. Locked Decisions (Authoritative)

### 2.1 Pricing & Plans

| Decision | Value | Owner | Source |
|----------|-------|-------|--------|
| Monthly price | $9.99/month | CH25 | CH08 §8, CH25 |
| Trial duration | 7 days | CH25 | CH08 §6 |
| Trial behavior | Equivalent to Pro | CH08 | CH08 §6 |
| Free saved flows cap | 2 | CH08 | CH08 §3.2 (LOCKED) |
| Free inbox cap | 10 | CH08 | CH08 §3.2 (LOCKED) |
| Free practice credits | 3/month | CH08 | CH08 §5 (LOCKED) |
| Credits usable on | Saved flows only | CH08 | CH08 §5 (LOCKED) |
| Credits usable on inbox items | No | CH08 | CH08 §5 (LOCKED) |
| Branch cap per move | 10 | CH12/CH13 | CH08 §2 (LOCKED) |

### 2.2 Storage & Media

| Decision | Value | Owner | Source |
|----------|-------|-------|--------|
| Pro media cap | 2GB total | CH29 | CH29 (LOCKED) |
| Uploads shareable | No (private-only) | CH29 | CH29 (LOCKED) |
| Uploads plan requirement | Pro-only | CH29 | CH08 §4.5 (LOCKED) |
| Link attachments shareable | Yes | CH29 | CH29 |

### 2.3 Guest Restrictions

| Decision | Value | Owner | Source |
|----------|-------|-------|--------|
| Guest can save flows | No | CH07 | CH07-G2 (LOCKED) |
| Guest can save custom moves | No | CH07 | CH07-G3 (LOCKED) |
| Guest draft persistence | No | CH07 | CH07 §5.3 (LOCKED) |
| Guest can practice | No (demo only if enabled) | CH08 | CH08 §4.4 |

---

## 3. Contradictions / Ambiguities

| ID | Issue | Location A | Location B | Severity |
|----|-------|------------|------------|----------|
| ME-C01 | Credit spend timing: CH08 says "starting a Practice session" consumes credit; CH22 has reservation model with release on 0 completed sets | CH08 §5 | CH22 L624-630 | Blocker |
| ME-C02 | Downgrade UX: CH08 says "read-only beyond cap" but CH15/CH25 owns exact UX | CH08 §6 | CH15/CH25 (TBD) | Important |
| ME-C03 | Export data: CH08 lists "Optional (placeholder) for Free, Yes (placeholder) for Pro" but no format/scope defined | CH08 §4.1 | CH34 (TBD) | Important |
| ME-C04 | Pro saved-flow limit: CH08 says "unlimited (subject to abuse soft caps)" but CH30 doesn't define the cap | CH08 §1.1 | CH30 | Important |
| ME-C05 | Trial eligibility display: CH25 says show "Start 7-Day Free Trial" if eligible, "Start Pro" if not — but eligibility check mechanism undefined | CH25 L620-625 | — | Important |

---

## 4. Cross-Chapter Dependencies

### 4.1 Blocking Dependencies

| ID | Blocked Item | Depends On | Batch | Severity |
|----|--------------|------------|-------|----------|
| ME-D01 | Share-link caps enforcement | CH17 Sharing | Batch 05 | Blocker |
| ME-D02 | Inbox cap enforcement | CH18 Inbox | Batch 05 | Blocker |
| ME-D03 | Paywall copy and layout | CH25 (finalized) | Batch 07 | Blocker |
| ME-D04 | Offline grace period | CH28 Offline/Sync | Batch 08 | Blocker |
| ME-D05 | Warning ladder thresholds | CH30 Safety/Abuse | Batch 08 | Blocker |
| ME-D06 | Error state handling | CH31 Error States | Batch 08 | Important |
| ME-D07 | Analytics events | CH33 Analytics | Batch 09 | Nice-to-have |
| ME-D08 | Export/deletion behavior | CH34 Export & Deletion | Batch 09 | Important |

### 4.2 Internal Dependencies (Within Monetization Domain)

| ID | Source | Target | Nature |
|----|--------|--------|--------|
| ME-D09 | CH08 (Entitlements) | CH25 (Paywalls) | Gate triggers → paywall surfaces |
| ME-D10 | CH08 (Entitlements) | CH29 (Storage) | Media caps enforcement |
| ME-D11 | CH08 (Entitlements) | CH30 (Warning Ladder) | Soft cap escalation |
| ME-D12 | CH25 (Paywalls) | CH30 (Warning Ladder) | Abuse prevention on upgrades |
| ME-D13 | CH29 (Storage) | CH30 (Warning Ladder) | Storage abuse detection |

---

## 5. Edge Cases Requiring Decision

| ID | Scenario | Question | Source | Severity |
|----|----------|----------|--------|----------|
| ME-E01 | User downgrades Pro→Free with >2 saved flows | Which flows become read-only? (first 2 by date? user choice?) | CH08 §6 | Blocker |
| ME-E02 | Free user at 2-flow cap receives import | Block import acceptance or allow into inbox (cap 10)? | CH08 §3.2, CH18 | Important |
| ME-E03 | Pro user at 2GB uploads to deleted flow | Does deleting flow free quota or is media orphaned? | CH29 | Important |
| ME-E04 | Trial ends mid-session | "Allow session to finish; apply restrictions next time" — confirmed? | CH21 L448-449 | Important |
| ME-E05 | Credit spent but session has 0 completed sets | Credit stays spent (CH25) but no streak (CH22) — correct? | CH25 L640, CH22 L604 | Important |
| ME-E06 | Guest converts mid-sandbox-flow | Does sandbox draft transfer or is it lost? | CH07 §5.3 | Important |
| ME-E07 | StoreKit purchase pending indefinitely | Keep current plan + "purchase-in-progress UI" — how long? | CH08 §2 | Important |
| ME-E08 | Offline Pro user exceeds grace period | Degrade to Free silently or show warning first? | CH28/CH08 | Blocker |

---

## 6. Plan-Feature Matrix Gaps

| Feature | Guest | Free | Pro/Trial | Gap |
|---------|-------|------|-----------|-----|
| Share link creation | No | Limited (placeholder) | Yes (higher limits; placeholder) | Free/Pro limits undefined |
| Export data | No | Optional (placeholder) | Yes (placeholder) | Formats/scopes undefined |
| Practice demo | TBD | TBD | N/A | Enablement decision pending |
| Gameplan creation | TBD | TBD | TBD | Plan gating unclear (CH23/CH08) |
| Mastery planning | TBD | TBD | TBD | Pro-only vs Free-limited undefined |
| Maintenance autoplan | TBD | TBD | TBD | Pro-only vs all users undefined |

---

## 7. Validation Gaps

| ID | Area | Gap | Source | Severity |
|----|------|-----|--------|----------|
| ME-V01 | Display name | No max length specified | CH07 §4 | Nice-to-have |
| ME-V02 | Handle | 3-20 chars specified but no reserved words | CH07 §4 | Nice-to-have |
| ME-V03 | Flow title | Max length placeholder (60 default) | CH15 | Important |
| ME-V04 | Upload file size | Max placeholder (100MB default) | CH29 | Important |
| ME-V05 | Note fields | Max placeholder (2000 chars default) | CH29 | Important |

---

## 8. StoreKit / Subscription Gaps

| ID | Issue | Source | Severity |
|----|-------|--------|----------|
| ME-SK01 | No annual plan defined | CH25 §Placeholders | Important |
| ME-SK02 | Trial eligibility check mechanism undefined | CH25 L620-625 | Important |
| ME-SK03 | Purchase pending timeout undefined | CH08 §2 | Important |
| ME-SK04 | Restore purchases behavior on conflict undefined | CH08 §6 | Important |
| ME-SK05 | Grace period for billing issues undefined | CH08/CH28 | Blocker |

---

## 9. Warning Ladder Gaps (CH30)

| ID | Vector | Gap | Severity |
|----|--------|-----|----------|
| ME-WL01 | Share-link creation | Exact caps for Free/Pro undefined | Blocker |
| ME-WL02 | Import acceptance | Burst thresholds undefined | Important |
| ME-WL03 | High-frequency edits | Rate-limit thresholds undefined | Important |
| ME-WL04 | Video upload churn | Storage warning thresholds (80%/95% recommended but not locked) | Important |
| ME-WL05 | All vectors | Cooldown durations per L1-L5 undefined | Important |

---

## Summary Statistics

| Category | Blocker | Important | Nice-to-have | Total |
|----------|---------|-----------|--------------|-------|
| Unresolved Decisions | 7 | 14 | 4 | 25 |
| Contradictions | 1 | 4 | — | 5 |
| Cross-Dependencies | 5 | 2 | 1 | 8 |
| Edge Cases | 2 | 6 | — | 8 |
| Plan-Feature Gaps | — | — | — | 6 |
| Validation Gaps | — | 3 | 2 | 5 |
| StoreKit Gaps | 1 | 4 | — | 5 |
| Warning Ladder Gaps | 1 | 4 | — | 5 |
| **Total** | **17** | **37** | **7** | **61** |

---

## Next Steps

1. Resolve credit spend timing contradiction (ME-C01)
2. Define Free/Pro share-link caps (ME-B01, ME-B02, ME-WL01)
3. Define Pro saved-flow soft cap (ME-B03)
4. Finalize offline grace period (ME-B05)
5. Clarify downgrade UX for flows beyond cap (ME-E01)
6. Define practice demo enablement (ME-I03)
7. Coordinate with CH17/CH18 on sharing/inbox limits
8. Finalize warning ladder thresholds (CH30)

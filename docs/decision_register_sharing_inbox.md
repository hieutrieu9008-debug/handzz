# Decision Register: Sharing & Inbox (Domain 04)

Generated: 2026-01-05
Source: PRD Batch 05 (CH17–CH19), referenced from Batch 02b (CH08), Batch 08 (CH30)
Status: Flagged items — awaiting resolution

---

## 1. Unresolved Decisions

### 1.1 Blocker

| ID | Item | Options | Default | Owner | Source |
|----|------|---------|---------|-------|--------|
| SI-B01 | Daily link creation cap (Free) | 5 / 10 / 20 per 24h | 10 | CH17 | CH17 §Placeholders |
| SI-B02 | Daily link creation cap (Pro) | 25 / 50 / 100 per 24h | 50 | CH17 | CH17 §Placeholders |
| SI-B03 | Active link cap (Free) | 10 / 25 / 50 | 25 | CH17 | CH17 §Placeholders |
| SI-B04 | Active link cap (Pro) | 100 / 250 / 500 | 250 | CH17 | CH17 §Placeholders |
| SI-B05 | Per-flow active link cap | none / 3 / 5 | 5 | CH17 | CH17 §Placeholders |
| SI-B06 | Pro inbox cap | 50 / 200 / Unlimited | 200 | CH08/CH30 | CH18 §Placeholders |
| SI-B07 | Snapshot payload size limit | 256KB / 512KB / 1MB | 512KB | CH30 | CH18 §Placeholders |
| SI-B08 | Imported flow node limit (anti-abuse) | 150 / 300 / 500 | 300 | CH30 | CH18 §Placeholders |

### 1.2 Important

| ID | Item | Options | Default | Owner | Source |
|----|------|---------|---------|-------|--------|
| SI-I01 | Link expiry support | Off / 7 days / 30 days | Off | CH17 | CH17 §Placeholders |
| SI-I02 | Deep-link transport | Universal Links / Dynamic Links | Universal Links | CH17 | CH17 §Placeholders |
| SI-I03 | Link stats visibility to owner | Show / Hide | Show opens only | CH17 | CH17 §Placeholders |
| SI-I04 | Add-to-Library removes or marks Inbox item | Remove / Keep with status | Remove | CH18 | CH18 §Placeholders |
| SI-I05 | Save to Inbox from Preview only or also Landing | Both / Preview-only | Both | CH18 | CH18 §Placeholders |
| SI-I06 | Allow Save to Inbox if saved-flow cap reached | Allow / Block | Allow (view-only) | CH18/CH08 | CH18 §Placeholders |
| SI-I07 | Rate limiting for import saves | 10 / 30 / 60 per min | 30/min | CH30 | CH18 §Placeholders |
| SI-I08 | Report issue flow | email link / in-app form / none | email link | CH30/CH31 | CH18 §Placeholders |
| SI-I09 | Sender name/avatar shown or anonymized | show / anonymize | show display name only | CH17/CH07 | CH18 §Placeholders |
| SI-I10 | Import batch size cap | TBD | TBD | CH30 | CH19 §Placeholders |
| SI-I11 | Action C (overwrite library) availability | Free / Pro-only / All | Pro-only | CH08 | CH19 §Placeholders |
| SI-I12 | Canonical matching heuristics thresholds | TBD | TBD | CH10 | CH19 §Placeholders |
| SI-I13 | Share payload media policy for links | Include previews / TBD | TBD | CH17/CH19 | CH29 §Placeholders |

### 1.3 Nice-to-have

| ID | Item | Options | Default | Owner | Source |
|----|------|---------|---------|-------|--------|
| SI-N01 | Deep-link fallback web landing copy | TBD | Simple install instructions | CH17 | CH18 §Placeholders |
| SI-N02 | Export before adding to library | Yes / No | No in V1 | CH34 | CH18 §Placeholders |

---

## 2. Locked Decisions (Authoritative)

### 2.1 Share Links

| Decision | Value | Owner | Source |
|----------|-------|-------|--------|
| Link visibility | Unlisted only | CH17 | CH17 |
| Token entropy | Min 128 bits | CH17 | CH17 |
| Token storage | Hashed (SHA-256) | CH17 | CH17 |
| Guest can create links | No | CH17 | CH17 |
| Revocation reversible | No (create new link) | CH17 | CH17 |
| Snapshot policy | Current version (not at-share-time) | CH17 | CH17 |

### 2.2 Inbox

| Decision | Value | Owner | Source |
|----------|-------|-------|--------|
| Free inbox cap | 10 items | CH08 | CH18 (LOCKED) |
| Free can practice inbox items | No | CH08 | CH18 (LOCKED) |
| Free can save inbox to library | Yes (if under 2-flow cap) | CH08 | CH18 |
| Inbox item stores snapshot | Yes (usable if link revoked) | CH18 | CH18 |

### 2.3 Media in Sharing

| Decision | Value | Owner | Source |
|----------|-------|-------|--------|
| Uploaded media shareable | No (private-only) | CH29 | CH17 (LOCKED) |
| External video links shareable | Yes | CH29 | CH17 |
| Media dropped on import | Uploads dropped; badge shown | CH19 | CH19 |

---

## 3. Contradictions / Ambiguities

| ID | Issue | Location A | Location B | Severity |
|----|-------|------------|------------|----------|
| SI-C01 | Free at 2-flow cap + import: CH18 says "can still Preview" and "can still Add to Library if under saved-flow cap" but this is contradictory when at cap | CH18 L372-373 | CH18 L376-381 | Important |
| SI-C02 | Link revoked while viewer open: "allow viewing to continue; refresh/import revalidates" but what about offline viewer? | CH17 L192-193 | — | Important |
| SI-C03 | Pro inbox cap: CH08 says "placeholder" but CH18 lists options (50/200/Unlimited) — no decision made | CH08 §Placeholders | CH18 §Placeholders | Blocker |
| SI-C04 | Schema version drift handling: CH19 says "block save-to-inbox" for missing required fields but doesn't specify which fields are required | CH19 L576-577 | — | Important |
| SI-C05 | Ambiguous match: CH19 says "user must choose" but doesn't define timeout or skip behavior | CH19 L558-560 | — | Important |

---

## 4. Cross-Chapter Dependencies

### 4.1 Blocking Dependencies

| ID | Blocked Item | Depends On | Batch | Severity |
|----|--------------|------------|-------|----------|
| SI-D01 | Link creation caps | CH08 Entitlements | Batch 02b | Blocker |
| SI-D02 | Canonical ID matching | CH10 Move Taxonomy | Batch 03 | Blocker |
| SI-D03 | Abuse limits enforcement | CH30 Warning Ladder | Batch 08 | Blocker |
| SI-D04 | Revert model for overwrites | CH11 Revert Model | Batch 03 | Important |
| SI-D05 | Error state copy | CH31 Error States | Batch 08 | Important |
| SI-D06 | Offline behavior for imports | CH28 Offline/Sync | Batch 08 | Important |
| SI-D07 | Export from inbox | CH34 Export | Batch 09 | Nice-to-have |

### 4.2 Internal Dependencies (Within Sharing Domain)

| ID | Source | Target | Nature |
|----|--------|--------|--------|
| SI-D08 | CH17 (Share Links) | CH18 (Inbox) | Link token → Inbox save |
| SI-D09 | CH17 (Share Links) | CH19 (Import Conflicts) | Payload fetch → Conflict detection |
| SI-D10 | CH18 (Inbox) | CH19 (Import Conflicts) | Add-to-Library → Conflict resolution |
| SI-D11 | CH19 (Import Conflicts) | CH10 (Canonical IDs) | Move matching algorithm |

---

## 5. Edge Cases Requiring Decision

| ID | Scenario | Question | Source | Severity |
|----|----------|----------|--------|----------|
| SI-E01 | Owner deletes flow with active links | Links become invalid — show "no longer available" — but what about existing inbox items with snapshots? | CH17 L67-68 | Important |
| SI-E02 | Link revoked while recipient has inbox item | Snapshot still opens with banner — correct behavior? | CH18 L348-349 | Important |
| SI-E03 | Free user at inbox cap (10) receives import | Block Save to Inbox; but can they Preview + Add directly to Library (if under 2-flow cap)? | CH18 L367-373 | Blocker |
| SI-E04 | Ambiguous match with no user choice | What happens if user abandons Preflight without resolving? Save blocked or partial save? | CH19 L530-531 | Important |
| SI-E05 | Import package exceeds node limit (300) | Block import entirely or truncate? | CH18 §Placeholders | Blocker |
| SI-E06 | Sender renames flow after link created | "Viewer shows updated name" — what about inbox item with snapshot? | CH17 L69 | Important |
| SI-E07 | Multiple active links to same flow, owner revokes one | Other links remain active — clear? | CH17 L64-65 | Nice-to-have |
| SI-E08 | Schema version drift on future import | Unknown fields preserved — but what if recipient app is older? | CH19 L575-577 | Important |
| SI-E09 | Deep-link opened when app not installed | Web landing → install → re-open — but token persistence? | CH17 L103-104 | Important |
| SI-E10 | Import with moves from deleted family | Canonical ID exists but family gone — C1 or C2? | CH19 | Important |

---

## 6. Conflict Resolution Gaps

| ID | Conflict Class | Gap | Severity |
|----|----------------|-----|----------|
| SI-CR01 | C1 Missing Move | If incoming has canonical_id but recipient has no moves in that family — which action is default? | Important |
| SI-CR02 | C2 Ambiguous Match | No skip option defined; no timeout for unresolved choices | Important |
| SI-CR03 | C3 Sender Customization | Diff preview format/content undefined | Nice-to-have |
| SI-CR04 | C4 Media Incompatibility | Badge copy "Video not included (upload is private)" but no action to request link from sender | Nice-to-have |
| SI-CR05 | C5 Schema Version Drift | Required vs optional fields list undefined | Important |

---

## 7. Anti-Abuse Gaps (CH30 Integration)

| ID | Vector | Gap | Severity |
|----|--------|-----|----------|
| SI-AB01 | Link creation rate | Rolling window duration undefined (per 24h stated but window type unclear) | Important |
| SI-AB02 | Link opens from same IP/device | Rate limit threshold undefined | Important |
| SI-AB03 | Import save rate | 30/min stated but burst vs sustained unclear | Important |
| SI-AB04 | Suspicious pattern flags | Exact thresholds for "many creates, opens, high failure rates" undefined | Important |
| SI-AB05 | DISABLED link state | Escalation path from abuse_flag to DISABLED undefined | Important |

---

## 8. Data Model Gaps

| ID | Model | Gap | Severity |
|----|-------|-----|----------|
| SI-DM01 | share_link.expires_at | Optional in V1 but expiry handling UX undefined | Important |
| SI-DM02 | InboxItem.linkRevokedHint | How/when is this updated if source link status changes? | Important |
| SI-DM03 | Import Package.move_descriptors | Exact required vs optional fields undefined | Important |
| SI-DM04 | Resolution snapshot | Structure for "chosen actions per move_ref_id" undefined | Important |

---

## 9. UX Flow Gaps

| ID | Screen | Gap | Severity |
|----|--------|-----|----------|
| SI-UX01 | Import Landing | Preview snippet "first 5 nodes" — selection logic undefined | Nice-to-have |
| SI-UX02 | Inbox List | "Unopened" badge logic — when does it clear? (on view, on detail open, on tap?) | Important |
| SI-UX03 | Preflight | "Apply to all" only for Sender Customizations — but C1 Missing Moves can have many; no bulk option | Important |
| SI-UX04 | Manage Links | "Opened X times" — privacy implications? (sender sees recipient activity) | Important |
| SI-UX05 | Viewer Mode | Educational copy dismissal — persistent or per-session? | Nice-to-have |

---

## 10. Validation Gaps

| ID | Area | Gap | Source | Severity |
|----|------|-----|--------|----------|
| SI-V01 | Link token | Min 128 bits specified; max undefined | CH17 | Nice-to-have |
| SI-V02 | Snapshot payload | 512KB default but no validation UX for oversized | CH18 | Important |
| SI-V03 | Import node count | 300 default but no clear error message defined | CH18 | Important |
| SI-V04 | Flow name on import | No max length validation specified | CH18/CH15 | Important |

---

## Summary Statistics

| Category | Blocker | Important | Nice-to-have | Total |
|----------|---------|-----------|--------------|-------|
| Unresolved Decisions | 8 | 13 | 2 | 23 |
| Contradictions | 1 | 4 | — | 5 |
| Cross-Dependencies | 3 | 3 | 1 | 7 |
| Edge Cases | 2 | 7 | 1 | 10 |
| Conflict Resolution Gaps | — | 4 | 2 | 6 |
| Anti-Abuse Gaps | — | 5 | — | 5 |
| Data Model Gaps | — | 4 | — | 4 |
| UX Flow Gaps | — | 3 | 2 | 5 |
| Validation Gaps | — | 3 | 1 | 4 |
| **Total** | **14** | **46** | **9** | **69** |

---

## Next Steps

1. Finalize link creation caps for Free/Pro (SI-B01–SI-B04)
2. Finalize Pro inbox cap (SI-B06, SI-C03)
3. Define snapshot payload size enforcement UX (SI-B07)
4. Define imported flow node limit enforcement (SI-B08, SI-E05)
5. Clarify Free at inbox cap + Add to Library behavior (SI-E03)
6. Define required vs optional fields in import package schema (SI-CR05)
7. Coordinate with CH10 on canonical matching thresholds (SI-I12)
8. Coordinate with CH30 on anti-abuse rate limits (SI-AB01–SI-AB05)

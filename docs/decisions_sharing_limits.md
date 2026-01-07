# Decision: Sharing, Links, Inbox Limits & Abuse Controls

**Date:** 2026-01-06  
**Status:** FINAL — ALL DECISIONS LOCKED  
**Mode:** No assumptions, no decisions, no invented values  
**Resolves:** SI-B01, SI-B02, SI-B03, SI-B04, SI-B05, SI-B06, SI-B07, SI-B08, SI-C03

---

## LOCKED (Verbatim PRD Only)

### Free Inbox Cap — CH08 §3.2 (L381)
| Cap | Value | Source |
|-----|-------|--------|
| Free inbox cap | 10 items | CH08 L381 (LOCKED) |

### Free Cannot Practice Inbox Items — CH08 §4.3 (L417)
| Rule | Value | Source |
|------|-------|--------|
| Free can practice inbox items | No | CH08 L417 (LOCKED) |

### Free Saved Flows Cap — CH08 §3.2 (L379)
| Cap | Value | Source |
|-----|-------|--------|
| Free saved flows cap | 2 | CH08 L379 (LOCKED) |

### Share Link Token Requirements — CH17 L38-42
| Requirement | Specification | Source |
|-------------|---------------|--------|
| Token entropy | Minimum 128 bits; recommend 160-192 | CH17 L39 |
| Token encoding | Must NOT encode user_id or flow_id directly | CH17 L40 |
| No incremental IDs | No incremental IDs in URLs | CH17 L41 |
| Server storage | Store only hash of token (SHA-256) | CH17 L42 |

### Share Link Lifecycle States — CH17 L50-54
| State | Description | Source |
|-------|-------------|--------|
| CREATED | Record exists but not yet visible (rare; typically immediate to ACTIVE) | CH17 L50 |
| ACTIVE | Link opens successfully into Viewer Mode | CH17 L51 |
| REVOKED | Owner revoked; must not reveal flow content | CH17 L52 |
| EXPIRED | Time-expired; treat like REVOKED for UX (optional V1) | CH17 L53 |
| DISABLED | System-disabled for abuse/safety (See CH30) | CH17 L54 |

### Share Link State Transitions — CH17 L57-60
| Transition | Allowed | Source |
|------------|---------|--------|
| CREATED -> ACTIVE | Yes | CH17 L58 |
| ACTIVE -> REVOKED | Yes | CH17 L58 |
| ACTIVE -> DISABLED | Yes | CH17 L58 |
| ACTIVE -> EXPIRED | Yes | CH17 L58 |
| REVOKED -> ACTIVE | No (V1 default: create new link instead) | CH17 L59 |
| DISABLED -> ACTIVE | Rare; admin/unban only | CH17 L60 |

### Revocation Semantics — CH17 L63-65
| Rule | Behavior | Source |
|------|----------|--------|
| Timing | Revocation must be immediate | CH17 L63 |
| Flow deletion | Revoking does not delete underlying flow | CH17 L64 |
| Reversibility | Revocation is irreversible in V1 (create new link to reshare) | CH17 L65 |

### Snapshot Policy (V1) — CH17 L72-77
| Policy | Behavior | Source |
|--------|----------|--------|
| Default V1 | Show current flow version (not snapshot at share time) | CH17 L73 |
| Mitigation | Show "Last updated" timestamp in viewer header | CH17 L77 |

### URL Format — CH17 L98-99
| Format | URL | Source |
|--------|-----|--------|
| Primary | `https://handz.app/s/<token>` | CH17 L98 |
| Alternate | `handz://share/<token>` or `https://handz.app/share/<token>` | CH17 L99 |

### Share Link Copy Link Behavior — CH17 L106-109
| Scenario | Behavior | Source |
|----------|----------|--------|
| Existing ACTIVE link | Copy most-recent ACTIVE link | CH17 L107 |
| No ACTIVE link | Create new ACTIVE link, then copy | CH17 L108 |
| Over cap | Show cap warning and path to manage/revoke old links | CH17 L109 |

### Media Attachment Rule (V1) — CH17 L152-154
| Media Type | Shareable | Source |
|------------|-----------|--------|
| Uploaded videos | No (private-only; count toward 2GB cap) | CH17 L152 |
| External video links (URLs) | Yes | CH17 L153 |

### Inbox Item Lifecycle — CH18 L339-358

**Creation Rules (CH18 L339-342):**
| Rule | Behavior | Source |
|------|----------|--------|
| Who can create | Signed-in Free/Pro/Trial taps "Save to Inbox" | CH18 L340 |
| Snapshot storage | Stores snapshot payload (usable even if original link revoked later) | CH18 L341 |
| Editability | View-only until moved to Library (no edits, practice, exports) | CH18 L342 |
| Cap counting | Does NOT count against "Saved flows" cap until added to Library | CH18 L343 |

**Deletion Rules (CH18 L349-353):**
| Rule | Behavior | Source |
|------|----------|--------|
| Effect | Permanently removes from account | CH18 L350 |
| Confirmation | Confirmable with undo toast (5 seconds) | CH18 L351 |
| Offline | Queue delete; show "Will delete when you're back online" | CH18 L352 |

### Free Inbox Cap UX — CH18 L362-372

**Soft Warning (at 8/10) — CH18 L363-366:**
| Element | Value | Source |
|---------|-------|--------|
| Trigger | At 8/10 | CH18 L363 |
| Surface | Non-blocking banner on Inbox List and Import Landing | CH18 L364 |
| Copy | "Inbox almost full (8/10). Delete items or upgrade." | CH18 L365 |
| Buttons | "Manage Inbox", "Upgrade" | CH18 L366 |

**Hard Cap (at 10/10) — CH18 L368-372:**
| Element | Value | Source |
|---------|-------|--------|
| Trigger | At 10/10 | CH18 L368 |
| Action | Block Save to Inbox | CH18 L369 |
| Modal title | "Inbox Full" | CH18 L369 |
| Modal body | "Your Free plan can hold 10 imports. Delete one to save this, or upgrade for a bigger inbox." | CH18 L370 |
| Buttons | "Manage Inbox" (primary), "Upgrade to Pro" (secondary), "Cancel" | CH18 L371 |
| Still allowed | Preview; Add to Library if under saved-flow cap | CH18 L372 |

### Warning Ladder Levels — CH30 L770-804

| Level | Name | Behavior | Source |
|-------|------|----------|--------|
| L0 | Normal | No warnings; user actions proceed normally; light background counting | CH30 L772-774 |
| L1 | Informational Nudge | Early, friendly notice approaching soft cap; non-blocking toast or inline banner; no slowdown | CH30 L776-780 |
| L2 | Soft Warning + Friction | Inline warning card + optional cooldown (seconds/minutes); optional confirmation step | CH30 L782-786 |
| L3 | Temporary Cooldown / Partial Block | Action blocked for time window (e.g., 15-60 minutes) OR reduced rate; countdown + reason + recovery | CH30 L788-793 |
| L4 | Feature Suspension | Suspend feature category for 24-72 hours; clear message + link to support | CH30 L795-798 |
| L5 | Account Security Block | Block high-risk actions until verification/support; audit log entry mandatory | CH30 L800-804 |

### Warning Ladder for Share Links — CH30 L827
| Behavior | PRD Statement | Source |
|----------|---------------|--------|
| L1 | Approaching daily cap | CH30 L827 |
| L2 | Confirmation + cooldown | CH30 L827 |
| L3 | Temporary block | CH30 L827 |
| L4 | 24-72h suspension | CH30 L827 |
| L5 | Security block | CH30 L827 |

### Warning Ladder for Imports — CH30 L828
| Behavior | PRD Statement | Source |
|----------|---------------|--------|
| Free | Inbox cap 10 + 2-flow cap | CH30 L828 |
| Burst imports | Trigger L2 confirm + cooldown; L3 if continued | CH30 L828 |

### Anti-Abuse Mechanics — CH17 L194-200
| Mechanism | PRD Statement | Source |
|-----------|---------------|--------|
| Rate limit link creation | Rolling window | CH17 L195 |
| Rate limit link opens | From same IP/device | CH17 L196 |
| Flag suspicious patterns | Many creates, opens, high failure rates; set abuse_flag | CH17 L197 |
| System can DISABLE | Link and/or block link creation for account (See CH30) | CH17 L198 |
| Prefer soft caps | Soft caps first (warnings), then block at high thresholds | CH17 L199 |
| Neutral copy | Never show accusatory copy; keep neutral | CH17 L200 |

### Copy Pack (V1 Strings) — CH17 L157-179
| Key | String | Source |
|-----|--------|--------|
| Share sheet title | "Share flow" | CH17 L159 |
| Share sheet note | "Anyone with this link can view this flow." | CH17 L160 |
| Copy link button | "Copy link" | CH17 L161 |
| Share button | "Share..." | CH17 L162 |
| Manage links | "Manage links" | CH17 L163 |
| Copied toast | "Link copied" | CH17 L164 |
| Created+copied toast | "New link created and copied" | CH17 L165 |
| Cap reached | "Link limit reached - revoke one to create a new link" | CH17 L166 |
| Revoked confirmation title | "Revoke this link?" | CH17 L167 |
| Revoked confirmation body | "Anyone who has it won't be able to open this flow anymore." | CH17 L168 |
| Revoked confirm CTA | "Revoke link" | CH17 L169 |
| Revoked success | "Link revoked" | CH17 L170 |
| Link not found title | "Link not found" | CH17 L171 |
| Link not found body | "This link doesn't exist or was typed wrong." | CH17 L172 |
| Link not available title | "Link not available" | CH17 L173 |
| Link not available body | "This link was revoked or expired." | CH17 L174 |
| Link disabled body | "This link is no longer available." | CH17 L175 |
| Viewer badge | "Viewer mode" | CH17 L176 |
| Guest CTA | "Create an account to save this flow" | CH17 L179 |

---

## Blocking Decisions — ALL LOCKED

| ID | Decision | Resolution | Status |
|----|----------|------------|--------|
| SL-D01 | Share link daily cap (Free) | **10 per 24h** | **LOCKED** |
| SL-D02 | Share link daily cap (Pro) | **50 per 24h** | **LOCKED** |
| SL-D03 | Active link cap (Free) | **25** | **LOCKED** |
| SL-D04 | Active link cap (Pro) | **250** | **LOCKED** |
| SL-D05 | Per-flow active link cap | **None (unlimited)** | **LOCKED** |
| SL-D06 | Pro inbox cap | **200** | **LOCKED** |
| SL-D07 | Snapshot payload size limit | **512 KB** | **LOCKED** |
| SL-D08 | Imported flow node limit | **300** | **LOCKED** |
| SL-D09 | Active share links per flow | **Unlimited (no cap)** | **LOCKED** |
| SL-D10 | Share link creation rate limit | **20 per minute (rolling)** | **LOCKED** |
| SL-D11 | Import save rate limit | **30 per minute** | **LOCKED** |
| SL-D12 | L1 warning threshold | **80% of cap** | **LOCKED** |
| SL-D13 | Import exceeds node limit behavior | **Block entirely (no truncation)** | **LOCKED** |

---

## LOCKED DECISIONS

### SL-D01: Share link daily cap (Free) — **LOCKED: 10 per 24h**

**Decision:** Free users can create up to 10 share links per rolling 24-hour window.

**Resolution Date:** 2026-01-06

---

### SL-D02: Share link daily cap (Pro) — **LOCKED: 50 per 24h**

**Decision:** Pro users can create up to 50 share links per rolling 24-hour window.

**Resolution Date:** 2026-01-06

---

### SL-D03: Active link cap (Free) — **LOCKED: 25**

**Decision:** Free users can have up to 25 active (non-revoked) share links total.

**Resolution Date:** 2026-01-06

---

### SL-D04: Active link cap (Pro) — **LOCKED: 250**

**Decision:** Pro users can have up to 250 active (non-revoked) share links total.

**Resolution Date:** 2026-01-06

---

### SL-D05: Per-flow active link cap — **LOCKED: None (unlimited)**

**Decision:** No per-flow cap on active share links. Users can create unlimited links per flow (subject to overall caps).

**Resolution Date:** 2026-01-06

---

### SL-D06: Pro inbox cap — **LOCKED: 200**

**Decision:** Pro users can have up to 200 items in their inbox.

**Resolution Date:** 2026-01-06

---

### SL-D07: Snapshot payload size limit — **LOCKED: 512 KB**

**Decision:** Maximum snapshot payload size for imported flows is 512 KB.

**Resolution Date:** 2026-01-06

---

### SL-D08: Imported flow node limit — **LOCKED: 300**

**Decision:** Maximum node count for imported flows is 300 nodes.

**Resolution Date:** 2026-01-06

---

### SL-D09: Active share links per flow — **LOCKED: Unlimited (no cap)**

**Decision:** No per-flow cap on active share links.

**Resolution Date:** 2026-01-06

---

### SL-D10: Share link creation rate limit — **LOCKED: 20 per minute (rolling)**

**Decision:** Share link creation is rate-limited to 20 per minute per user (rolling window).

**Resolution Date:** 2026-01-06

---

### SL-D11: Import save rate limit — **LOCKED: 30 per minute**

**Decision:** Import saves are rate-limited to 30 per minute.

**Resolution Date:** 2026-01-06

---

### SL-D12: L1 warning threshold — **LOCKED: 80% of cap**

**Decision:** L1 informational nudge triggers at 80% of any cap.

**Resolution Date:** 2026-01-06

---

### SL-D13: Import exceeds node limit behavior — **LOCKED: Block entirely (no truncation)**

**Decision:** If an import exceeds the node limit (300), block the import entirely. Do not truncate.

**Resolution Date:** 2026-01-06

---

## Data Model (PRD-Specified Fields Only)

### share_link — CH17 L78-94
```
id: UUID (primary key)
token: string (hashed server-side)
owner_user_id: UUID
flow_id: UUID
status: enum(ACTIVE, REVOKED, EXPIRED, DISABLED)
created_at: timestamp
revoked_at: timestamp? (nullable)
expires_at: timestamp? (nullable; optional V1)
last_opened_at: timestamp? (nullable)
open_count: int
last_opened_ip_hash: string? (nullable, privacy-aware)
last_opened_user_agent_hash: string? (nullable)
abuse_flag: bool (default false)
notes: string? (internal; admin/moderation; optional)
```

### InboxItem — CH18 L385-409
```
id: string (stable)
ownerUserId: string
shareTokenHash: string (do NOT store raw token)
shareLinkId: string? (if available)
sourceCreatorId: string?
sourceCreatorName: string? (display only)
sourceFlowName: string
sourceFlowUpdatedAt: timestamp?
receivedAt: timestamp
lastOpenedAt: timestamp?
status: "unopened" | "opened" | "converted" | "deleted"
snapshotPayload: object (full flow snapshot)
flags:
  hasCustomMoves: boolean
  hasExternalLinks: boolean
  hasPrivateUploads: boolean
  hasConflictsHint: boolean
  linkRevokedHint: boolean (optional)
metrics:
  approxNodeCount: number
  approxEdgeCount: number
  approxBranchingMax: number
```

---

## PRD References

- CH08 §3.2 (L379-381): Free caps (saved flows, inbox)
- CH08 §4.3 (L417): Free cannot practice inbox items
- CH17 L38-42: Share link token requirements
- CH17 L50-65: Link lifecycle states and transitions
- CH17 L72-77: Snapshot policy
- CH17 L78-94: share_link data model
- CH17 L98-109: URL format and copy link behavior
- CH17 L152-154: Media attachment rules
- CH17 L157-179: Copy pack
- CH17 L194-200: Anti-abuse mechanics
- CH17 L202-212: Placeholders
- CH18 L339-358: Inbox item lifecycle
- CH18 L362-372: Free inbox cap UX
- CH18 L385-409: InboxItem data model
- CH18 L439-453: Placeholders
- CH30 L770-804: Warning ladder levels
- CH30 L823-831: Abuse-sensitive vectors
- CH30 L878-883: Placeholder thresholds

---

**End of Document — All Sharing Limits Decisions LOCKED**

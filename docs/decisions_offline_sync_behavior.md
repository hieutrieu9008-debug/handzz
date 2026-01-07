# Decision: Offline & Sync Behavior

**Date:** 2026-01-06  
**Status:** PRD-STRICT EXTRACTION  
**Mode:** No assumptions, no decisions, no invented values  
**Resolves:** ME-E08, ME-B05, PM-D02, related offline blockers

---

## LOCKED (Verbatim PRD Only)

### Definitions — CH28 (L330-337)
| Term | Definition | Source |
|------|------------|--------|
| Online | Device can reach backend services reliably (auth + database) | CH28 L331 |
| Offline | Device cannot reach backend services, or connectivity is intermittent | CH28 L332 |
| Degraded | Network exists but is slow/unreliable; app must avoid blocking user and queue work | CH28 L333 |
| Local cache | Locally stored read copies of server data | CH28 L334 |
| Local drafts | Locally stored user changes not yet synced to server | CH28 L335 |
| Sync queue | Ordered list of pending operations to replay when online | CH28 L336 |
| Conflict | Same entity modified in two places since last successful sync | CH28 L337 |

### Goals — CH28 (L339-344)
| Goal | Source |
|------|--------|
| Never lose user work due to connectivity issues | CH28 L340 |
| Make offline state obvious but non-alarming | CH28 L341 |
| Keep flow-builder and practice usable in typical gym connectivity | CH28 L342 |
| Keep behavior consistent across screens | CH28 L343 |
| Prevent abuse/runaway storage via soft caps + warnings (CH30) | CH28 L344 |

### Non-Goals (V1) — CH28 (L346-349)
| Non-Goal | Source |
|----------|--------|
| Full offline access to share links opened outside the app | CH28 L347 |
| Offline collaboration in V1 | CH28 L348 |
| Perfect automatic merges; V1 prioritizes no-loss conflict copies | CH28 L349 |

### Offline Capability Matrix — CH28 (L353-365)

| Feature | Online | Degraded | Offline | Source |
|---------|--------|----------|---------|--------|
| Guest mode | Browse demo flows; build sandbox flow; cannot save | Same; show connectivity banner | Browse cached demos; sandbox ephemeral; cannot save | CH28 L357 |
| Auth / account | Sign up / log in / manage account | Use if token valid; login may fail; retry | Cannot sign up/log in; usable only if session cached | CH28 L358 |
| Moves library | View/search; create/edit custom moves | Create/edit as drafts; queue sync | View cached; create/edit as drafts (account required) | CH28 L359 |
| Flow builder | Create/edit flows; autosave | Edits stored as drafts; queue sync | Edit cached flows; create as drafts; sync later | CH28 L360 |
| Inbox / imports | View items; save to library | Queue save operations; avoid duplicates | View cached items only; cannot fetch new; cannot resolve share links | CH28 L361 |
| Share links | Create/revoke links | May take time; retry; avoid duplicates | Blocked: cannot create/revoke | CH28 L362 |
| Practice mode | Start sessions; log history | Allow if content cached; queue logs | Allow only if entitlements recently validated and content cached; queue logs | CH28 L363 |
| Media (video) | Pro uploads; links shareable; uploads private | Queue uploads; show pending; allow cancel | Select/record to attach locally; upload later; sharing uses links only | CH28 L364 |
| Export / deletion | Export data; delete account | Exports may queue; deletion requires online | Export cached only; account deletion blocked | CH28 L365 |

### Connectivity Indicator (Global Banner) — CH28 (L369-375)
| Rule | Specification | Source |
|------|---------------|--------|
| Placement | Persistent, non-blocking banner at top when Degraded or Offline | CH28 L370 |
| Positioning | Must not cover navigation bars or push important controls off-screen | CH28 L371 |
| Degraded copy | "Connection is unstable — saving locally." | CH28 L372 |
| Offline copy | "Offline — changes will sync when you're back online." | CH28 L373 |
| Details link | Includes "Details" link opening Sync Status sheet | CH28 L374 |
| Dismissal | Banner disappears automatically after 5s only if state returns to Online | CH28 L375 |

### Save Status States — CH28 (L377-381)
| State | Meaning | Source |
|-------|---------|--------|
| Saved (synced) | Successfully synced to server | CH28 L379 |
| Saving... | Local write in progress | CH28 L379 |
| Saved locally | Draft pending sync | CH28 L379 |
| Sync error | Needs attention | CH28 L379 |

### Sync Status Sheet — CH28 (L383-387)
| Element | Content | Source |
|---------|---------|--------|
| Summary | Current connectivity state, last successful sync time, number of pending operations, blocking errors | CH28 L385 |
| Actions | "Retry now", "Copy debug info", "Open storage manager", "Contact support" | CH28 L386 |
| Warning | If pending operations exceed soft cap, show warning with guidance (CH30) | CH28 L387 |

### Draft Creation Rules — CH28 (L393-397)
| Rule | Specification | Source |
|------|---------------|--------|
| Trigger | When editing begins (first change), create or update local draft record | CH28 L394 |
| Keys | Drafts keyed by stable object IDs (server ID when known, otherwise locally generated UUID) | CH28 L395 |
| Revision | Drafts store draftRevision counter (increments on every meaningful edit) | CH28 L396 |
| Persistence | Drafts persisted immediately (no in-memory-only drafts) | CH28 L397 |

### Autosave Cadence — CH28 (L399-403)
| Context | Timing | Source |
|---------|--------|--------|
| Flow builder | Autosave within 250ms after last edit gesture ends (debounced) | CH28 L400 |
| Move editor | Autosave within 500ms after last field change (debounced) | CH28 L401 |
| Practice session | Log events instantly; update per-set completion at event time | CH28 L402 |
| App backgrounded/terminated | Flush pending local writes before suspension | CH28 L403 |

### Sync Queue Operation Types — CH28 (L410-416)
| Operation | Description | Source |
|-----------|-------------|--------|
| UPSERT entity | Create or update (moves, flows, folders, settings) | CH28 L411 |
| DELETE entity | Delete (reversible until synced) | CH28 L412 |
| APPEND_LOG | Append practice log events or session summaries | CH28 L413 |
| UPLOAD_MEDIA | Upload local media file to cloud storage | CH28 L414 |
| LINK_OP | Create/revoke share links | CH28 L415 |
| INBOX_OP | Accept import, save to library, or dismiss | CH28 L416 |

### Operation Record Schema — CH28 (L418-419)
```
op_id, op_type, entity_type, entity_id, payload_json, created_at, 
attempt_count, next_retry_at, last_error_code, last_error_message, 
depends_on_op_id (optional), device_id, user_id
```
Source: CH28 L419

### Retry and Backoff — CH28 (L421-425)
| Rule | Specification | Source |
|------|---------------|--------|
| Strategy | Exponential backoff with jitter for network failures | CH28 L422 |
| Timing | Do not retry immediately in tight loop; respect next_retry_at | CH28 L423 |
| Feedback | Non-blocking toasts for transient failures; escalate only if failures persist or queue grows | CH28 L424 |
| Manual retry | Users can tap "Retry now" from Sync Status sheet | CH28 L425 |

### Queue Ordering and Dependencies — CH28 (L427-430)
| Rule | Specification | Source |
|------|---------------|--------|
| Processing | Operations processed FIFO within same entity; may interleave entities for throughput | CH28 L428 |
| Dependencies | If operation depends on another, store depends_on_op_id and enforce ordering | CH28 L429 |
| Blocked ops | If dependency missing or failed permanently, mark dependent ops as blocked and surface error (CH31) | CH28 L430 |

### Pull Sync Rules — CH28 (L432-436)
| Trigger | Behavior | Source |
|---------|----------|--------|
| App launch | Lightweight sync summary check; if changes exist, pull deltas | CH28 L433 |
| Foreground resume | If last sync > 5 minutes ago and network available, pull deltas | CH28 L434 |
| While editing | Do not interrupt user; apply remote changes in background; surface conflicts only if needed | CH28 L435 |
| Payload size | Deltas bounded to avoid huge payloads on cellular; paginate if needed | CH28 L436 |

### What is Cached — CH28 (L438-444)
| Data Type | Caching Rule | Source |
|-----------|--------------|--------|
| Moves | Default + custom and user-specific overrides | CH28 L439 |
| Flows | Metadata + nodes/edges + sequence metadata in user's library, plus pinned/offline-designated flows | CH28 L440 |
| Folders | Flow organization metadata | CH28 L441 |
| Inbox items | Metadata (not necessarily full payloads if large) | CH28 L442 |
| Practice history | Summaries (recent window) and streak state | CH28 L443 |
| Media | Caching follows CH29 caps | CH28 L444 |

### Conflict Detection Signals — CH28 (L449-453)
| Rule | Specification | Source |
|------|---------------|--------|
| Server tracking | Each entity stores serverUpdatedAt and monotonic serverRevision | CH28 L450 |
| Local tracking | Local drafts store baseServerRevision captured when editing started | CH28 L451 |
| Detection | When pushing draft, if serverRevision changed since baseServerRevision, treat as conflict | CH28 L452 |
| Clock rule | Clock time alone is not sufficient; use serverRevision | CH28 L453 |

### Default Conflict Policy — CH28 (L455-459)
| Rule | Specification | Source |
|------|---------------|--------|
| No auto-merge | Do not attempt deep automatic merges in V1 | CH28 L456 |
| Preservation | Create a Conflict Copy so both versions preserved | CH28 L457 |
| Naming | "{OriginalName} (Conflict Copy — {date})" | CH28 L458 |
| Banner | "We found edits from another device. Review your conflict copy." | CH28 L459 |

### Conflict Resolution UI — CH28 (L461-466)
| Option | Behavior | Source |
|--------|----------|--------|
| Keep Mine | Keep local draft as main; save server version as copy | CH28 L462 |
| Keep Theirs | Discard local draft as main; keep server; save local draft as copy | CH28 L463 |
| Keep Both | Keep both versions as separate items (default) | CH28 L464 |
| Additional | Include: "View differences" (basic metadata diff) and "Undo this decision" | CH28 L465 |

### Offline Media Behavior — CH28 (L468-485)

**Attaching Media While Offline (CH28 L471-475):**
| Rule | Specification | Source |
|------|---------------|--------|
| Pro user | May select/record video offline; store local file reference and mark Pending Upload | CH28 L472 |
| Free user | Show paywall before allowing selection; do not store media | CH28 L473 |
| Status display | Pending uploads show status: "Waiting for connection" | CH28 L474 |
| Removal | Users can remove pending upload at any time | CH28 L475 |

**Upload Resume and Failure (CH28 L477-481):**
| Rule | Specification | Source |
|------|---------------|--------|
| Auto-resume | When online resumes, begin uploads automatically in background | CH28 L478 |
| Failures | Retry with backoff | CH28 L479 |
| Edit while pending | Upload attaches to latest draft unless removed | CH28 L480 |
| Cap exceeded | Block upload and route to Storage manager | CH28 L481 |

### Deletion Behavior While Offline — CH28 (L493-497)
| Rule | Specification | Source |
|------|---------------|--------|
| Marking | Offline delete marks entity as Pending Delete, not immediate destruction | CH28 L494 |
| Visibility | Pending Delete items appear in 'Recently Deleted'; can be restored until synced | CH28 L495 |
| After sync | Remove from cache except minimal tombstone metadata | CH28 L496 |
| Restore | If restored before sync, remove delete op from queue | CH28 L497 |

### Guest Limitations (Offline-Specific) — CH28 (L499-503)
| Rule | Specification | Source |
|------|---------------|--------|
| No local saves | Guests cannot save flows (not even locally); flow building is sandbox only | CH28 L500 |
| Cached demos | Offline guest may explore cached demos; edits lost on exit | CH28 L501 |
| Interstitial | Before entering Flow Builder as guest, show interstitial: "Guest mode doesn't save your flows. Create an account to save." Buttons: "Continue as guest" / "Create account" | CH28 L502 |
| Draft blocking | If guest attempts action creating draft (e.g., custom move), block and route to sign-up | CH28 L503 |

### Storage Manager Screen — CH28 (L507-510)
| Element | Content | Source |
|---------|---------|--------|
| Path | Settings > Storage & Offline | CH28 L508 |
| Display | Local cache size (non-media), Offline media pending uploads, Cached thumbnails, 'Pinned for offline' flows | CH28 L509 |
| Actions | "Clear cache", "Clear thumbnails", "Remove pending uploads", "Manage offline flows" | CH28 L510 |

### Offline Pinning for Flows — CH28 (L513-517)
| Rule | Specification | Source |
|------|---------------|--------|
| User action | Users can mark flows as "Available offline" | CH28 L514 |
| Caching | Pinned flows ensure nodes/edges/sequence details cached and refreshed when online | CH28 L515 |
| Video links | Link-based videos stored but playback still requires network | CH28 L516 |
| Budget | Pinned flows count toward offline cache budget (placeholder) | CH28 L517 |

### App Lifecycle (iOS) — CH28 (L519-523)
| Event | Behavior | Source |
|-------|----------|--------|
| On background | Flush local draft writes; pause non-critical network operations; persist sync queue state | CH28 L520 |
| On foreground | Re-check connectivity; resume queue; refresh deltas if stale | CH28 L521 |
| Long upload | Optionally notify when long upload finishes/fails (CH27) | CH28 L522 |
| Background sync | Do not rely on always-on background sync; users never blocked waiting for background work | CH28 L523 |

### Security for Offline Storage — CH28 (L525-530)
| Rule | Specification | Source |
|------|---------------|--------|
| Auth tokens | iOS Keychain only | CH28 L526 |
| Database | Encrypt local database at rest (recommended) or encrypt sensitive fields at minimum | CH28 L527 |
| Media files | Protect local media files with iOS file protection | CH28 L528 |
| Analytics | Do not log PII or raw content in analytics; use redacted debug logs (CH33) | CH28 L529 |
| On logout | Clear local drafts and caches by default (unless user explicitly opts to keep) | CH28 L530 |

### Edge Cases and Failure Scenarios — CH28 (L532-539)
| Scenario | Behavior | Source |
|----------|----------|--------|
| Crash during edit | Recover unsynced drafts on next launch; show "Recovered draft" banner | CH28 L533 |
| Rapid online/offline toggling | Prevent duplicates via op_id idempotency | CH28 L534 |
| Clock drift | Never rely on client time for ordering; use server revisions | CH28 L535 |
| Large flows | Autosave must remain responsive; do not block UI while persisting | CH28 L536 |
| Dangling paths | Allowed; draft validity does not require all branches to terminate | CH28 L537 |
| Merge nodes | Multiple incoming edges allowed; validations tolerate this | CH28 L538 |
| Soft limit exceeded offline | Warn but do not delete work; offer export draft JSON as escape hatch | CH28 L539 |

### Pro (grace) State Definition — CH08 (L330)
| State | Definition | Source |
|-------|------------|--------|
| Pro (grace) | Subscription was active recently but cannot be verified due to network/StoreKit failure. Allow Pro features for short grace window (owned by CH28). Display non-blocking banner: "Can't verify subscription right now. Some Pro features may pause if this continues." | CH08 L330 |

### Plan State Evaluation with Grace — CH08 (L342)
| Rule | Specification | Source |
|------|---------------|--------|
| Grace condition | If StoreKit cannot verify and a recent verified snapshot exists → state may be Pro (grace) | CH08 L342 |

### Plan State Enum — CH08 (L349)
```
planState: enum (guest | free | trial | pro | pro_grace)
```
Source: CH08 L349

### Offline Entitlement Behavior — CH25 (L745-748)
| Plan | Offline Behavior | Source |
|------|------------------|--------|
| Pro offline | Allow Pro entitlements for grace window (e.g., 24 hours) using last-known timestamp; after grace, treat as Free until refresh | CH25 L746 |
| Free offline | Credit/cap checks use locally cached values; resolve conservatively after sync | CH25 L747 |
| Paywalls | Should not block app from opening; only block monetized action | CH25 L748 |

### Error State: No Internet — CH31 (L1008)
| ERR ID | Severity | Surface | Copy | Source |
|--------|----------|---------|------|--------|
| ERR-001 | S2 | Banner + Toast | "Offline. You're not connected. You can keep viewing saved content, but actions that require internet won't work." | CH31 L1008 |

### Degraded Mode Standard — CH31 (L999-1001)
| Scenario | Behavior | Source |
|----------|----------|--------|
| Network offline | Banner "Offline — some actions are unavailable"; disable network-dependent buttons with inline explanation | CH31 L999 |
| Entitlements cannot be verified | Default to safer side (Free behavior) but show banner "Can't verify subscription — some Pro features may be locked until you reconnect." | CH31 L1000 |
| Remote config fails | Use built-in defaults; log S1 warning once per session | CH31 L1001 |

---

## RESOLVED DECISIONS

### OFF-D01: Offline Grace Period for Pro — LOCKED
| Decision | Selected Option |
|----------|-----------------|
| Offline grace period for Pro | **B) 24 hours** |

**Resolution Date:** 2026-01-06

**Selected Behavior:**
- Pro users retain Pro entitlements for up to 24 hours when subscription verification is unavailable
- Grace window starts from last successful verification timestamp
- After 24h without verification: treat as Free until refresh succeeds

**Rationale:**
- Covers realistic offline scenarios (gym sessions, travel, overnight outages)
- Aligns with PRD guidance using 24h as intended example (CH25 L746)
- Balances user forgiveness with limited exploit exposure

**Scope Clarification:**
| Scenario | Grace Applies? |
|----------|----------------|
| Network unavailable, valid subscription | Yes — 24h grace |
| StoreKit unavailable, valid subscription | Yes — 24h grace |
| Known-expired subscription | No — downgrade immediately |
| Apple billing retry period | Separate — Apple handles |

**Dependencies:**
- StoreKit validation timing
- Sync reconciliation for entitlement refresh

**Source:** CH25 L768 placeholder default confirmed.

---

### OFF-D02: Grace Period Expiry UX — LOCKED
| Decision | Selected Option |
|----------|-----------------|
| Grace period expiry UX | **C) Banner + Block** — persistent banner at expiry with explanation |

**Resolution Date:** 2026-01-06

**Selected Behavior:**
- When 24h grace period expires without successful verification:
  - Pro-only actions are blocked
  - Persistent banner is displayed explaining the verification issue
  - Banner remains until entitlement verification succeeds
- No silent state changes without user-visible explanation

**Banner Copy (from CH08 L330):**
> "Can't verify subscription right now. Some Pro features may pause if this continues."

**On Expiry Copy:**
> "We couldn't verify your subscription. Pro features are paused until you reconnect."

**Rationale:**
- Aligns with PRD guidance favoring transparency (CH31 L1000)
- Avoids surprise downgrades, especially in offline scenarios
- Works reliably even if user remains offline at grace expiry
- Clearly communicates why Pro features are paused and how to restore

**UX Flow:**
| Time | State | UI |
|------|-------|-----|
| 0-24h offline | Pro (grace) | Subtle banner: "Can't verify subscription..." |
| 24h+ offline | Free (blocked) | Persistent banner: "Pro features paused until reconnect" |
| On reconnect + verify | Pro restored | Banner dismissed; toast: "Pro restored" |

**Source:** CH08 L330, CH31 L1000 interpreted as requiring visible explanation.

---

### OFF-D04: "Recently Validated" Entitlement Window — LOCKED
| Decision | Selected Option |
|----------|-----------------|
| "Recently validated" entitlement window | **C) 12 hours** |

**Resolution Date:** 2026-01-06

**Selected Behavior:**
- Free users can practice offline only if entitlement validation occurred within the last 12 hours
- Validation timestamp must be server-issued (not client clock)
- After 12h without validation: offline practice blocked with explanation

**Rationale:**
- Covers realistic gym and day-part offline scenarios
- Balances user forgiveness with credit desync and exploit risk
- Aligns cleanly with existing system boundaries:
  - Credit reservation expiry: 12h (CS-D02)
  - Pro offline grace: 24h (OFF-D01)
- Maintains clear Free vs Pro differentiation (Free: 12h, Pro: 24h)

**System Alignment:**
| Boundary | Duration | Plan |
|----------|----------|------|
| Credit reservation expiry | 12h | Free |
| Free validation window | 12h | Free |
| Pro offline grace | 24h | Pro |

**Guardrails:**
- Validation timestamp is server-issued
- Offline usage reconciled conservatively on sync (CS-D04)
- Repeated desync patterns → warning ladder escalation (CH30)

**Blocked UX Copy:**
> "You're offline and we couldn't verify your credits. Connect to the internet to start practice."

**Satisfies Dependency:** CS-D04 (Offline practice for Free) required this definition.

**Source:** CH28 L363 "recently validated" now defined as 12 hours.

---

### OFF-D05: Offline Cache Budget for Pinned Flows — LOCKED
| Decision | Selected Option |
|----------|-----------------|
| Offline cache budget | **E) Hybrid — 10 flows OR 100 MB** (whichever is hit first) |

**Resolution Date:** 2026-01-06

**Selected Behavior:**
- Users may pin up to 10 flows for offline use
- Secondary size cap of 100 MB applies as hard ceiling
- Whichever limit is hit first blocks additional pinning
- Media assets governed separately by CH29 caching rules

**Rationale:**
- Preserves clear user mental model ("up to 10 flows")
- Prevents edge cases where large flows consume excessive storage
- Future-proofs as flow complexity or metadata size grows
- Balances user flexibility with system safety

**Expected Behavior:**
| Scenario | Limit Hit | Result |
|----------|-----------|--------|
| 10 small flows (500 KB total) | Count | Cannot pin 11th flow |
| 3 huge flows (100 MB total) | Size | Cannot pin 4th flow |
| Typical use (10 flows, ~2 MB) | Count | Works fine |

**UI Messaging:**
- Emphasize flow count: "You can save up to 10 flows for offline use"
- Show storage only if approaching size limit: "Offline storage almost full"

**At Limit Copy:**
> "You've reached your offline limit (10 flows). Remove one to add another."

**Source:** CH28 L550 placeholder resolved.

---

### OFF-D06: Sync Queue Soft Cap — LOCKED
| Decision | Selected Option |
|----------|-----------------|
| Sync queue soft cap | **C) 200 operations** |

**Resolution Date:** 2026-01-06

**Selected Behavior:**
- Warning shown when pending sync operations exceed 200
- This is a soft cap only — no operations are blocked
- Warning provides guidance to reconnect or export drafts
- Queue continues to accept operations beyond the soft cap

**Rationale:**
- Avoids noisy warnings during normal offline use
- Captures genuine risk scenarios (extended offline or heavy editing)
- Preserves warning ladder signal quality
- Aligns with PRD intent to warn without blocking or deleting work (CH28 L539)

**Warning UX:**
| Threshold | Behavior |
|-----------|----------|
| < 200 ops | Normal — no warning |
| ≥ 200 ops | L1 warning banner in Sync Status sheet |
| Continued growth | Escalate per warning ladder (CH30) |

**Warning Copy:**
> "You have a lot of unsynced changes. Connect to the internet to sync your work."

**Sync Status Sheet Actions (from CH28 L386):**
- "Retry now"
- "Copy debug info"
- "Open storage manager"
- "Contact support"

**Escape Hatch (from CH28 L539):**
- Offer "Export draft JSON" if user cannot sync

**Source:** CH28 L387, CH28 L539 interpreted with 200 ops as soft cap.

---

### OFF-D07: Streak Handling When Offline — LOCKED
| Decision | Selected Option |
|----------|-----------------|
| Streak handling when offline | **A) Credit on Sync** — backdated to original session date |

**Resolution Date:** 2026-01-06

**Selected Behavior:**
- Offline practice sessions are queued with original device-local timestamps
- On sync, server credits the streak for the qualifying day(s) represented by the session
- Streak UI updates only after successful sync
- No client-side optimistic streak increments

**Rationale:**
- Preserves fairness by counting legitimate offline practice
- Avoids punitive behavior of ignoring offline sessions
- Prevents anxiety-inducing streak rollbacks from optimistic updates
- Aligns with PRD streak computation using session-local qualifying days (CH22 L602)

**Example Scenario:**
| Event | Day | Streak |
|-------|-----|--------|
| User practices offline | Monday | UI shows previous streak (e.g., 5) |
| User stays offline | Tuesday | UI still shows 5 |
| User syncs | Wednesday | Server processes Monday session; streak becomes 6 |
| UI updates | Wednesday | User sees streak = 6 (Monday counted) |

**Key Rule:**
- Streak computation uses `deviceTimezoneAtStart` from session, not sync timestamp
- Monday practice synced on Wednesday = Monday counts as qualifying day

**Source:** CH22 L602, CH22 L606-609 interpreted with offline credit on sync.

---

## UNRESOLVED (PRD Provides Options, No Selection Made)

---

## OPEN QUESTIONS (PRD Silent or Ambiguous)

### OQ-OFF01: Exact grace period duration for Pro offline?
- **PRD Reference:** CH25 L768 lists options (12h/24h/48h) with 24h as "default"
- **PRD Reference:** CH08 L330 says "short grace window (owned by CH28)"
- **Status:** PLACEHOLDER — not locked
- **Impact:** When Pro users lose access offline

### OQ-OFF02: What happens when grace period expires?
- **PRD Reference:** CH25 L746 says "after grace, treat as Free until refresh"
- **PRD silent** on exact UX (silent downgrade vs warning first)
- **Related:** ME-E08 blocker

### OQ-OFF03: Can Free users start practice offline?
- **PRD Reference:** CH28 L363 says "Allow only if entitlements recently validated and content cached"
- **PRD Reference:** Credit verification is server-authoritative (CH20 L275)
- **Implication:** Free may be blocked offline (cannot verify credits)
- **PRD does not explicitly state** Free offline practice behavior

### OQ-OFF04: What is "recently validated" for entitlements?
- **PRD Reference:** CH28 L363 uses "recently validated"
- **PRD silent** on exact time window
- **Impact:** How stale can entitlement snapshot be?

### OQ-OFF05: What happens to credit reservation if offline mid-session?
- **PRD Reference:** CH22 L624-630 describes reservation model
- **PRD silent** on reservation behavior when offline
- **Impact:** Credit consumption when connectivity lost

### OQ-OFF06: How long can Practice session logs remain unsynced?
- **PRD Reference:** CH28 L402 says log events instantly, queue logs
- **PRD silent** on maximum queue age before expiry
- **Impact:** Very long offline periods

### OQ-OFF07: What is the offline cache budget for pinned flows?
- **PRD Reference:** CH28 L517 says "placeholder"
- **PRD does not specify** size limit
- **Impact:** How many flows can be pinned

### OQ-OFF08: What happens to maintenance reminders when offline?
- **PRD Reference:** CH24 L510-516 describes offline behavior
- **PRD Reference:** CH24 L512 says "hide Start if practice cannot start offline (CH28 rules)"
- **PRD silent** on whether reminders fire offline

### OQ-OFF09: Can users resolve import conflicts offline?
- **PRD Reference:** CH28 L361 says "cannot resolve share links" offline
- **PRD silent** on already-downloaded inbox items with conflicts
- **Impact:** CH19 conflict resolution flow

### OQ-OFF10: What is the maximum sync queue size before warning?
- **PRD Reference:** CH28 L387 says "If pending operations exceed soft cap, show warning"
- **PRD Reference:** CH28 L539 says "warn but do not delete work"
- **PRD silent** on exact soft cap number
- **Related:** CH30 owns thresholds

### OQ-OFF11: How does streak calculation work if offline for multiple days?
- **PRD Reference:** CH22 L606-609 defines streak computation
- **PRD silent** on offline streak handling
- **Impact:** Users practicing offline may lose streak credit

### OQ-OFF12: What happens if user downgrades to Free while offline?
- **PRD Reference:** CH08 L466 describes downgrade behavior
- **PRD silent** on offline downgrade scenario
- **Impact:** When downgrade takes effect

---

## PRD References

- CH08 L330: Pro (grace) state definition
- CH08 L342: Plan state evaluation with grace
- CH08 L349: planState enum
- CH25 L745-748: Offline entitlement behavior
- CH25 L768: Offline grace window placeholder
- CH28 L330-337: Definitions
- CH28 L339-349: Goals and non-goals
- CH28 L353-365: Offline capability matrix
- CH28 L369-387: Global UX patterns
- CH28 L389-403: Local drafts and autosave
- CH28 L405-430: Sync queue model
- CH28 L432-444: Pull sync and caching
- CH28 L446-466: Conflict detection and resolution
- CH28 L468-485: Offline media behavior
- CH28 L487-497: Undo, revert, deletion
- CH28 L499-503: Guest limitations
- CH28 L505-517: Storage and offline access management
- CH28 L519-523: App lifecycle
- CH28 L525-530: Security and privacy
- CH28 L532-539: Edge cases
- CH28 L550: Placeholders
- CH31 L999-1001: Degraded mode standard
- CH31 L1008: No internet error state

---

## Blocking Decisions Required

| ID | Decision | Options | Owner | Status |
|----|----------|---------|-------|--------|
| OFF-D01 | Offline grace period for Pro | 12h / 24h / 48h | CH08/CH28 | **LOCKED: 24h** |
| OFF-D02 | Grace period expiry UX | Silent downgrade / Warning first / Banner + block | PM | **LOCKED: Banner + Block** |
| OFF-D03 | Free offline practice | Block / Allow if cached + recently validated | PM + Eng | **LOCKED: Allow** (via CS-D04) |
| OFF-D04 | "Recently validated" entitlement window | TBD (hours) | CH08/CH28 | **LOCKED: 12h** |
| OFF-D05 | Offline cache budget for pinned flows | TBD (MB or count) | CH28 | **LOCKED: 10 flows / 100 MB** |
| OFF-D06 | Sync queue soft cap (pending operations) | TBD (count) | CH28/CH30 | **LOCKED: 200 ops** |
| OFF-D07 | Streak handling when offline | Credit on sync / Require online / TBD | CH22/CH28 | **LOCKED: Credit on Sync** |

---

**End of PRD-Strict Extraction**

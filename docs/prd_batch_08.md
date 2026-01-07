# PRD Batch 08 — CH27-CH32 (System & Platform Features)

**Generated:** 2026-01-05
**Chapters:** CH27, CH28, CH29, CH30, CH31, CH32
**Status:** Extracted

---

## CH27 — Notifications

### Overview
Defines the notification system for Handz V1: reminders (maintenance, mastery, practice, inbox), user controls, scheduling rules, and rate limits. iOS-only. Notifications help users return to Handz at the right time without becoming spammy or breaking trust.

### Dependencies
- CH01, CH02, CH06, CH08, CH23, CH24, CH25, CH28, CH30, CH31

### Related
- CH18, CH20, CH21, CH22, CH33, CH34

### V1 Principles
- Notifications are user-serving first; no "marketing blast" notifications in V1
- Default posture is quiet; if user denies permission, app still works fully via in-app badges/dashboards
- All scheduling in user's local time; respects quiet hours, training time, user-selected cadence
- Notifications are actionable: tapping deep-links to specific screen and state
- Rate-limited and content-safe (no shaming, no medical claims; see CH30)

### Non-Goals (V1)
- No community/gym broadcast messaging
- No ML-based "best time to notify" optimization
- No push campaigns/promos; trial/billing notifications are purely transactional

### Key Requirements

#### Notification Primitives
- **Local notification**: Scheduled/delivered by device (works offline); used for reminders
- **Remote notification (push)**: Via APNs (requires backend); used where device-scheduling insufficient (e.g., new inbox import)
- **In-app notification**: Entry in in-app feed/center (optional V1) or inline banner/toast; always available
- **Badge**: iOS app icon badge count; represents actionable items (unread inbox, maintenance due today), not total history

#### Permission Strategy
- Do NOT prompt on first launch
- First eligible moment: after user completes one "value loop" (saved first flow, created first Gameplan/Mastery target, OR completed first Practice session)
- Never request permission while user is creating a flow
- Guest users: do not prompt; cannot save flows or create recurring reminders
- Free users: prompt allowed after scheduling maintenance or creating mastery plan

#### Pre-Permission Soft Prompt
- Title: "Stay on track?"
- Body: "Handz can remind you when it's time to maintain your gameplans and keep reactions sharp. You control timing and can turn this off anytime."
- Buttons: [Enable Reminders] (primary) / [Not Now] (secondary)
- Only trigger system permission dialog if user taps "Enable Reminders"

#### If Permission Denied
- Show one-time confirmation banner: "No problem — you can still use Handz. You can enable reminders later in Settings."
- Settings > Notifications shows status line + [Open iOS Settings] button
- All reminder logic must degrade to in-app indicators (dashboard cards, badges, Maintenance 'Due' list)

#### Notification Categories (User-Facing Toggles)
| Category | Description |
|----------|-------------|
| Maintenance Reminders | Reminders to maintain mastered paths/gameplans (CH24) |
| Practice Reminders | Reminders for planned practice sessions; credit/trial nudges (CH20-CH22) |
| Mastery Updates | Milestones and plan checkpoints; progress markers only |
| Inbox & Sharing | New imports and link activity (CH17-CH19) |
| Account & Security | Email verification, password resets, account deletion confirmation (CH07/CH34) |
| Billing (Transactional) | Trial ending soon, payment failed, subscription renewed (CH25) |

#### Notification Type Specifications

**N-MAINT-DUE-TODAY (Maintenance Reminders)**
- Trigger: At user's chosen daily reminder time if >= 1 maintenance item due today
- Delivery: Local notification
- Eligibility: Account required; Free/Pro/Trial
- Default: ON (recommended)
- Frequency cap: 1/day
- Deep-link: handz://maintenance?filter=dueToday
- Copy: "Maintenance due: {count} item(s). Keep your reactions sharp."

**N-MAINT-OVERDUE (Maintenance Reminders)**
- Trigger: At daily reminder time when >= 1 item overdue AND user hasn't opened Maintenance in 48h
- Delivery: Local notification
- Eligibility: Account required; Free/Pro/Trial
- Default: OFF
- Frequency cap: max 2/week
- Deep-link: handz://maintenance?filter=overdue
- Copy: "You've got {count} overdue item(s). Want to reschedule?"

**N-PRACTICE-SCHEDULED (Practice Reminders)**
- Trigger: User explicitly schedules practice session for time T; fire at T
- Delivery: Local notification
- Eligibility: Pro/Trial; Free only when consuming credit session (saved flows only)
- Default: ON if user scheduled
- Frequency cap: 1 per scheduled session
- Deep-link: handz://practice/session?id={sessionId}
- Copy: "Time to practice: {sessionName}."

**N-PRACTICE-STREAK-NUDGE (Practice Reminders)**
- Trigger: At daily reminder time if user has active streak at risk AND no practice logged today
- Delivery: Local notification
- Eligibility: Pro/Trial; Free only if credits remaining
- Default: OFF
- Frequency cap: max 3/week
- Deep-link: handz://practice
- Copy: "Keep the streak alive? One quick set is enough."

**N-PRACTICE-CREDIT-EXPIRY (Practice Reminders)**
- Trigger: 48 hours before monthly credit refresh, only if unused credits
- Delivery: Local notification
- Eligibility: Free (account required)
- Default: ON
- Frequency cap: 1/month
- Deep-link: handz://paywall?context=credits
- Copy: "You have {creditsLeft} practice credit(s) left this month. Want to use one?"

**N-MASTERY-CHECKPOINT (Mastery Updates)**
- Trigger: Mastery plan reaches checkpoint date (e.g., week boundary)
- Delivery: Local notification
- Eligibility: Account required; Free/Pro/Trial
- Default: ON
- Frequency cap: max 2/week
- Deep-link: handz://mastery/plan?id={planId}
- Copy: "Checkpoint: {planName}. Review what to maintain next."

**N-MASTERY-MAINT-OVERLOAD (Mastery Updates)**
- Trigger: Maintenance queue exceeds user's preferred weekly capacity (CH24)
- Delivery: Local notification
- Eligibility: Account required; Free/Pro/Trial
- Default: ON
- Frequency cap: max 1/week
- Deep-link: handz://maintenance?filter=overload
- Copy: "Too much to maintain? Let's prioritize your top paths."

**N-INBOX-NEW-IMPORT (Inbox & Sharing)**
- Trigger: New import arrives in Inbox (CH18)
- Delivery: Push preferred; fallback in-app banner + badge update on open/resume
- Eligibility: Account required; Free/Pro/Trial (Free inbox cap = 10)
- Default: ON
- Frequency cap: hard cap 10/day + burst cap 1/min; if exceeded, deliver daily summary instead
- Deep-link: handz://inbox?highlight={importId}
- Copy: "New gameplan received." (lockscreen-safe); expanded: "{senderName} sent {flowName}."

**N-BILLING-TRIAL-END (Billing)**
- Trigger: Trial ends in 48 hours; schedule at moment trial starts
- Delivery: Local (scheduled) and/or push
- Eligibility: Trial users only
- Default: ON
- Frequency cap: 1 per trial
- Deep-link: handz://paywall?context=trialEnding
- Copy: "Your trial ends soon. Keep practicing without interruptions."

**N-BILLING-PAYMENT-FAILED (Billing)**
- Trigger: StoreKit reports billing issue / payment failed
- Delivery: Push if available; must also show in-app alert on next open
- Eligibility: Pro users
- Default: ON
- Frequency cap: 1 per event
- Deep-link: handz://paywall?context=paymentFailed
- Copy: "Payment issue. Update your subscription to keep Pro features."

#### General Copy Rules
- Lock-screen safe defaults (generic) if user hasn't enabled full preview
- No claims like "guaranteed subconscious"; keep promises modest and training-focused
- Copy < 90 characters to avoid truncation

#### Scheduling Rules

**User-Configurable Inputs:**
- Daily reminder time (default: 7:00 PM local)
- Quiet hours (default: 10:00 PM–7:00 AM)
- Training days selector (default: none selected)
- Max notifications per day (soft cap, default 2/day)
- Snooze duration (default options: 15m, 1h, tomorrow)

**Delivery Window Rules:**
- No notification during quiet hours; defer to next quiet-hour end
- If multiple notifications within 15-minute window, batch into single notification when possible
- If user has Training Days set and trigger is not time-critical, prefer next selected training day
- Respect iOS limits for pending local notifications; schedule only next 7 days, re-schedule on app open

**Time Zone and DST Handling:**
- Stored schedule preferences in local wall-clock time + device time zone identifier
- On app open, compare stored time zone to current; if different, re-compute and re-schedule
- DST: if time doesn't exist (spring forward), deliver at next valid minute; if time repeats (fall back), deliver only once

**Missed Notifications:**
- Show same information as in-app 'Due' card on next open
- No 'catch-up spam'; show single summary on next open if needed

#### User Controls (Settings > Notifications)
- System status row: 'Enabled' or 'Off in iOS Settings' + [Open iOS Settings]
- Master toggle: 'Pause all notifications'
- Category toggles: Maintenance, Practice, Mastery, Inbox, Billing, Account/Security
- Schedule section: Daily reminder time picker, Quiet hours start/end, Training days multi-select, Max per day (2/3/4)
- Preview section: example of notification with current settings
- Any change triggers immediate re-scheduling (idempotent)

#### Contextual Controls
- Maintenance page: banner with [Snooze 1 day] [Edit priorities] [Change reminder time]
- Practice setup: 'Remind me' toggle with time picker
- Gameplan/Mastery plan setup: 'Maintenance reminders for this plan' toggle + optional cadence

#### Critical vs Optional Notifications
- User can disable categories, but transactional billing and security/account recovery must still appear in-app
- If user disables Billing notifications, app still shows in-app reminders during trial end / payment issue window
- Email verification/password reset primarily via email; push/local is optional

#### Tap Behavior and Deep-Link Routing
- Tapping notification opens app and routes via internal deep-link URI
- If logged out: route to Login, then continue after login (post-auth redirect)
- If Guest mode: route to 'Account required' interstitial
- If target object deleted: route to parent screen with toast "That item is no longer available."

**Deep-Link Map:**
| Deep-link | Destination |
|-----------|-------------|
| handz://maintenance?filter=dueToday | Maintenance tab, filtered to Due Today |
| handz://maintenance?filter=overdue | Maintenance tab, filtered to Overdue |
| handz://maintenance?filter=overload | Maintenance tab, Overload banner + priority picker |
| handz://practice | Practice tab landing |
| handz://practice/session?id={sessionId} | Practice Setup pre-filled |
| handz://mastery/plan?id={planId} | Gameplan/Mastery detail screen |
| handz://inbox?highlight={importId} | Inbox list with import highlighted |
| handz://paywall?context=trialEnding | Paywall screen (trial ending) |
| handz://paywall?context=paymentFailed | Paywall screen (payment issue) |
| handz://paywall?context=credits | Paywall/credits info sheet |
| handz://settings/notifications | Settings > Notifications |

#### Badge Counts
- Badge = unreadInboxCount + (maintenanceDueToday ? 1 : 0) + (billingIssue ? 1 : 0) + (securityFlag ? 1 : 0)
- Inbox: number of unread inbox items not yet opened
- Maintenance: 1 if >= 1 Due Today item and user hasn't opened Maintenance today
- Billing: 1 if unresolved billing issue
- Security: 1 if email not verified after X days (optional)

#### In-App Notifications (Minimum Viable)
- Lightweight toasts/banners for: new inbox import while app open, payment issue detected, 'notifications disabled' info banners
- Optional: simple in-app 'Updates' feed (if implemented, not required for core flows)

#### Data Model

**NotificationPreferences (per user):**
```json
{
  "userId": "string",
  "systemPermission": "unknown | granted | denied",
  "pausedAll": "boolean",
  "categoryEnabled": {
    "maintenance": "boolean",
    "practice": "boolean",
    "mastery": "boolean",
    "inbox": "boolean",
    "billing": "boolean",
    "accountSecurity": "boolean"
  },
  "schedule": {
    "dailyReminderTime": "19:00",
    "quietHours": { "start": "22:00", "end": "07:00" },
    "trainingDays": ["Mon", "Wed", "Fri"],
    "maxNotifsPerDay": 2
  },
  "lastPromptedAt": "ISODateTime|null",
  "updatedAt": "ISODateTime"
}
```

**NotificationEventLog (debug + analytics):**
```json
{
  "id": "string",
  "userId": "string",
  "typeId": "string",
  "channel": "local | push | in_app",
  "scheduledFor": "ISODateTime|null",
  "sentAt": "ISODateTime|null",
  "openedAt": "ISODateTime|null",
  "suppressedReason": "null | category_off | paused_all | quiet_hours | rate_limited | system_denied | missing_data",
  "context": { "flowId?": "string", "planId?": "string", "importId?": "string" },
  "appVersion": "string"
}
```

#### Safety and Rate Limits

**Content Safety Rules:**
- No shaming language ("lazy", "you failed", "you skipped"); use neutral, supportive tone
- No medical or injury claims; keep wording as "help you practice and remember"
- No aggressive upsell copy; upsells happen in-app (CH25)
- No references to violence outside sport context

**Global Rate Limits (Hard Caps):**
- Hard cap: 10 notifications/day total (all categories combined)
- Burst cap: max 1 notification per minute
- Inbox cap: max 10 inbox push notifications/day; if exceeded, switch to daily summary
- Billing: max 1 per event
- Maintenance: max 1/day due-today; overdue max 2/week

**Soft Caps (UX Guardrails):**
- Default maxNotifsPerDay = 2; if user raises, show note about spam
- When multiple triggers fire, prefer single summary notification
- If user repeatedly dismisses a category for 2 weeks, suggest turning it off (in-app, not notification)

**Abuse/Security Considerations:**
- Do not include sensitive data in push payloads; use generic text on lock screen
- Validate all deep-link parameters server-side
- Protect against notification-triggered enumeration; import IDs must be opaque and unguessable
- Follow CH30 warning ladder for abuse signals; warnings should be in-app, not push

### Placeholders (CH27)
| ID | Placeholder | Default | Notes |
|----|-------------|---------|-------|
| P-CH27-01 | Default daily reminder time | 7:00 PM local | Expose as config; allow A/B tests |
| P-CH27-02 | Default quiet hours | 10:00 PM–7:00 AM | Expose as config |
| P-CH27-03 | Inbox import expiry | Off (no expiry) | If added, update inbox UI |
| P-CH27-04 | In-app Updates feed | Optional | If shipped, add nav entry + metrics |
| P-CH27-05 | Training-days defaults | None selected | If user indicates in onboarding, pre-fill |

---

## CH28 — Offline Behavior & Sync

### Overview
Specifies how Handz behaves with limited or no connectivity, how data is cached locally, how changes sync to server, and how conflicts/failures are handled without losing user work. Includes UX patterns for offline states.

### Dependencies
- CH04, CH07, CH08, CH12, CH15, CH18, CH29, CH30, CH31

### Related
- CH09-CH11, CH20-CH24, CH33, CH34, CH36, CH37

### Definitions
- **Online**: Device can reach backend services reliably (auth + database)
- **Offline**: Device cannot reach backend services, or connectivity is intermittent
- **Degraded**: Network exists but is slow/unreliable; app must avoid blocking user and queue work
- **Local cache**: Locally stored read copies of server data
- **Local drafts**: Locally stored user changes not yet synced to server
- **Sync queue**: Ordered list of pending operations to replay when online
- **Conflict**: Same entity modified in two places since last successful sync

### Goals
- Never lose user work due to connectivity issues
- Make offline state obvious but non-alarming
- Keep flow-builder and practice usable in typical gym connectivity
- Keep behavior consistent across screens
- Prevent abuse/runaway storage via soft caps + warnings (CH30)

### Non-Goals
- Full offline access to share links opened outside the app
- Offline collaboration in V1
- Perfect automatic merges; V1 prioritizes no-loss conflict copies

### Key Requirements

#### Offline Capability Matrix

| Feature | Online | Degraded | Offline |
|---------|--------|----------|---------|
| Guest mode | Browse demo flows; build sandbox flow; cannot save | Same; show connectivity banner | Browse cached demos; sandbox ephemeral; cannot save |
| Auth / account | Sign up / log in / manage account | Use if token valid; login may fail; retry | Cannot sign up/log in; usable only if session cached |
| Moves library | View/search; create/edit custom moves | Create/edit as drafts; queue sync | View cached; create/edit as drafts (account required) |
| Flow builder | Create/edit flows; autosave | Edits stored as drafts; queue sync | Edit cached flows; create as drafts; sync later |
| Inbox / imports | View items; save to library | Queue save operations; avoid duplicates | View cached items only; cannot fetch new; cannot resolve share links |
| Share links | Create/revoke links | May take time; retry; avoid duplicates | Blocked: cannot create/revoke |
| Practice mode | Start sessions; log history | Allow if content cached; queue logs | Allow only if entitlements recently validated and content cached; queue logs |
| Media (video) | Pro uploads; links shareable; uploads private | Queue uploads; show pending; allow cancel | Select/record to attach locally; upload later; sharing uses links only |
| Export / deletion | Export data; delete account | Exports may queue; deletion requires online | Export cached only; account deletion blocked |

#### Global UX Patterns

**Connectivity Indicator (Global Banner):**
- Persistent, non-blocking banner at top when Degraded or Offline
- Must not cover navigation bars or push important controls off-screen
- Degraded copy: "Connection is unstable — saving locally."
- Offline copy: "Offline — changes will sync when you're back online."
- Includes "Details" link opening Sync Status sheet
- Banner disappears automatically after 5s only if state returns to Online

**Save Status (Per Editable Object):**
- Compact status near primary title or in header
- States: Saved (synced), Saving... (local write in progress), Saved locally (draft pending sync), Sync error (needs attention)
- Updates immediately when user makes edit (optimistic)
- On "Sync error", show tappable affordance: "Retry" and "Details"

**Sync Status Sheet:**
- Bottom sheet summarizing sync health with recovery actions
- Shows: current connectivity state, last successful sync time, number of pending operations, blocking errors
- Actions: "Retry now", "Copy debug info", "Open storage manager", "Contact support"
- If pending operations exceed soft cap, show warning with guidance (CH30)

#### Local Drafts and Autosave
- Local-first autosave model; users should never need to tap 'Save'
- Explicit save buttons commit to local draft immediately and enqueue sync

**Draft Creation Rules:**
- When editing begins (first change), create or update local draft record
- Drafts keyed by stable object IDs (server ID when known, otherwise locally generated UUID)
- Drafts store draftRevision counter (increments on every meaningful edit)
- Drafts persisted immediately (no in-memory-only drafts)

**Autosave Cadence:**
- Flow builder: autosave within 250ms after last edit gesture ends (debounced)
- Move editor: autosave within 500ms after last field change (debounced)
- Practice session: log events instantly; update per-set completion at event time
- If app backgrounded/terminated, flush pending local writes before suspension

#### Sync Queue Model
- All server mutations represented as idempotent operations in local sync queue
- When online, queue replayed in order
- When offline, operations accumulate; UI reflects "Saved locally"

**Operation Types:**
- UPSERT entity: create or update (moves, flows, folders, settings)
- DELETE entity: delete (reversible until synced)
- APPEND_LOG: append practice log events or session summaries
- UPLOAD_MEDIA: upload local media file to cloud storage
- LINK_OP: create/revoke share links
- INBOX_OP: accept import, save to library, or dismiss

**Operation Record Schema:**
- op_id, op_type, entity_type, entity_id, payload_json, created_at, attempt_count, next_retry_at, last_error_code, last_error_message, depends_on_op_id (optional), device_id, user_id

**Retry and Backoff:**
- Exponential backoff with jitter for network failures
- Do not retry immediately in tight loop; respect next_retry_at
- Non-blocking toasts for transient failures; escalate only if failures persist or queue grows
- Users can tap "Retry now" from Sync Status sheet

**Queue Ordering and Dependencies:**
- Operations processed FIFO within same entity; may interleave entities for throughput
- If operation depends on another, store depends_on_op_id and enforce ordering
- If dependency missing or failed permanently, mark dependent ops as blocked and surface error (CH31)

#### Pull Sync (Keeping Local Cache Fresh)
- On app launch: lightweight sync summary check; if changes exist, pull deltas
- On foreground resume: if last sync > 5 minutes ago and network available, pull deltas
- While actively editing: do not interrupt user; apply remote changes in background; surface conflicts only if needed
- Deltas bounded to avoid huge payloads on cellular; paginate if needed

**What is Cached:**
- Moves (default + custom) and user-specific overrides
- Flows (metadata + nodes/edges + sequence metadata) in user's library, plus pinned/offline-designated flows
- Folders and flow organization metadata
- Inbox item metadata (not necessarily full payloads if large)
- Practice history summaries (recent window) and streak state
- Media caching follows CH29 caps

#### Conflict Detection and Resolution
- V1 prioritizes no data loss and clear user recovery over perfect automatic merges

**Conflict Detection Signals:**
- Each entity stores serverUpdatedAt and monotonic serverRevision
- Local drafts store baseServerRevision captured when editing started
- When pushing draft, if serverRevision changed since baseServerRevision, treat as conflict
- Clock time alone is not sufficient; use serverRevision

**Default Conflict Policy:**
- Do not attempt deep automatic merges in V1
- Create a Conflict Copy so both versions preserved
- Naming: "{OriginalName} (Conflict Copy — {date})"
- Surface banner: "We found edits from another device. Review your conflict copy."

**Conflict Resolution UI:**
- Keep Mine: keep local draft as main; save server version as copy
- Keep Theirs: discard local draft as main; keep server; save local draft as copy
- Keep Both: keep both versions as separate items (default)
- Include: "View differences" (basic metadata diff) and "Undo this decision"
- Error-state visuals align with CH31

#### Offline Media Behavior
- Uploads are Pro-only (CH29); 2GB total cap; private-only, not shareable

**Attaching Media While Offline:**
- Pro user may select/record video offline; store local file reference and mark Pending Upload
- If Free, show paywall before allowing selection; do not store media
- Pending uploads show status: "Waiting for connection"
- Users can remove pending upload at any time

**Upload Resume and Failure Handling:**
- When online resumes, begin uploads automatically in background
- Failures retry with backoff
- If user edits while upload pending, upload attaches to latest draft unless removed
- If cap exceeded, block upload and route to Storage manager

**Shareability Reminder:**
- Links are shareable; uploaded videos are not shareable via share links/imports
- Label wherever user adds media: "Uploaded videos stay private and won't be included when you share."

#### Undo, Revert, and Recovery

**Local Undo vs Version Revert:**
- Local undo: undo/redo inside Flow Builder for last N actions; works offline
- Version revert: revert move/flow to prior saved version; requires version cached locally or online to fetch

**Deletion Behavior While Offline:**
- Offline delete marks entity as Pending Delete, not immediate destruction
- Pending Delete items appear in 'Recently Deleted'; can be restored until synced
- After delete sync succeeds, remove from cache except minimal tombstone metadata
- If restored before sync, remove delete op from queue

#### Guest Limitations (Offline-Specific)
- Guests cannot save flows (not even locally); flow building is sandbox only
- Offline guest may explore cached demos; edits lost on exit
- Before entering Flow Builder as guest, show interstitial: "Guest mode doesn't save your flows. Create an account to save." Buttons: "Continue as guest" / "Create account"
- If guest attempts action creating draft (e.g., custom move), block and route to sign-up

#### Storage and Offline Access Management

**Storage Manager Screen (Settings):**
- Path: Settings > Storage & Offline
- Show: Local cache size (non-media), Offline media pending uploads, Cached thumbnails, 'Pinned for offline' flows
- Actions: "Clear cache", "Clear thumbnails", "Remove pending uploads", "Manage offline flows"
- Confirmations explain consequences

**Offline Pinning for Flows:**
- Users can mark flows as "Available offline"
- Pinned flows ensure nodes/edges/sequence details cached and refreshed when online
- Link-based videos stored but playback still requires network
- Pinned flows count toward offline cache budget (placeholder)

#### App Lifecycle and Background Sync (iOS)
- On background: flush local draft writes; pause non-critical network operations; persist sync queue state
- On foreground: re-check connectivity; resume queue; refresh deltas if stale
- Optionally notify when long upload finishes/fails (CH27)
- Do not rely on always-on background sync; users never blocked waiting for background work

#### Security and Privacy for Offline Storage
- Auth tokens in iOS Keychain only
- Encrypt local database at rest (recommended) or encrypt sensitive fields at minimum
- Protect local media files with iOS file protection
- Do not log PII or raw content in analytics; use redacted debug logs (CH33)
- On logout, clear local drafts and caches by default (unless user explicitly opts to keep)

#### Edge Cases and Failure Scenarios
- Crash during edit: recover unsynced drafts on next launch; show "Recovered draft" banner
- Rapid online/offline toggling: prevent duplicates via op_id idempotency
- Clock drift: never rely on client time for ordering; use server revisions
- Large flows: autosave must remain responsive; do not block UI while persisting
- Dangling paths allowed; draft validity does not require all branches to terminate
- Merge nodes with multiple incoming edges allowed; validations tolerate this
- If user exceeds soft limits offline (too many pending ops), warn but do not delete work; offer export draft JSON as escape hatch

#### Telemetry and Diagnostics
- Sync queue length (bucketed), retry counts, permanent failures
- Average time from local save to successful sync
- Upload success/failure rates and average upload size (no content)
- Conflict events count and chosen resolution option
- Storage usage snapshots (bucketed)
- Users can copy redacted debug string from Sync Status sheet

### Placeholders (CH28)
- Offline cache budget for pinned flows: TBD

---

## CH29 — Data Storage & Limits

### Overview
Defines what data exists in Handz V1, where it is stored (device vs cloud), storage/size limits (including locked 2GB Pro media cap), and enforcement + UX rules. Focus on product behavior and data rules.

### Dependencies
- CH00, CH02, CH07, CH08, CH17, CH28, CH30

### Related
- CH09-CH16, CH20-CH24, CH31, CH34

### Key Requirements

#### Plan States & Storage Behavior

| Plan | Storage Behavior |
|------|------------------|
| Guest | Browse demo content; cannot save flows/moves locally or to cloud (locked); edits are temporary session draft discarded on app close or inactivity timeout |
| Free | Cloud account; save up to 2 flows (locked); inbox cap 10 items (locked); cannot upload media; links allowed; practice paywalled |
| Trial/Pro | Full cloud storage; Pro may upload media up to 2GB total (locked); uploads do not propagate via share links (locked) |

#### Storage Domains

**Domain A — Cloud (Authoritative):**
- User account profile, entitlements/plan state, all saved user content (moves, flows, practice history, mastery plans, etc.)
- Share links and inbox imports
- Metadata for multi-device continuity

**Domain B — Device (Ephemeral or Cached):**
- Temporary drafts when offline or before first save
- Cached read models for performance (flow thumbnails, computed path lists)
- Downloaded previews for share/import screens
- Local-only media files prior to upload (Pro) or as device-local reference (not shareable)

**Rule:** Cloud is authoritative except when CH28 defines explicit offline edit merge path.

#### Data Types & Storage

**User Profile & Entitlements:**
- Stored in cloud
- Contains: user ID, display name, username, createdAt, planState, trialStart/trialEnd, subscription status, feature flags
- Plan state cached on device for offline gating; refreshed when network available
- No sensitive training content stored here

**Moves (Default + Custom + Customizations):**
- Default move catalog packaged in-app and mirrored in cloud (CH09/CH10)
- User custom moves stored in cloud; editing creates new revision for revert (CH11)
- Move alias/family references stored as canonical IDs (CH10)
- Technique descriptions optional; V1 default moves ship with minimal/no description (locked CH09/CH10)

**Flows + Builder Graph:**
- Saved flows are cloud objects
- Free cap = 2 flows (locked)
- Each flow owns graph (nodes, edges) and references moves by canonical ID + optional override payloads
- Draft flows exist only on device until saved; Guest cannot save drafts

**Sequences / Transition Details:**
- Sequence detail objects attach to edges (or edge+node pair per CH13); stored in cloud with flow
- High-text fields; apply per-field length caps (placeholder)

**Practice Logs / History:**
- Cloud stored for Free/Pro
- Logs must be compact: store references to selected paths and session summary; avoid duplicating entire graphs per session

**Mastery / Maintenance Plans:**
- Cloud stored
- May reference multiple flows and paths
- Maintenance schedules may trigger notifications (CH27)

**Inbox Items (Imports):**
- Cloud object referencing incoming share payload or link snapshot
- Free inbox cap = 10 items (locked)
- Free can view but cannot practice inbox items (locked); practice requires saving to library and consuming credits on saved flows only

**Media Attachments:**
- Two types: Links (shareable) and Uploads (private-only)
- Uploads are Pro-only, counted toward 2GB cap, never included in share payloads (locked)
- Links can be attached at move-level and/or flow-level

**Exports:**
- Generated on device (PDF/JSON/ZIP etc.) then optionally shared via iOS share sheet
- Export formats/scopes owned by CH34

#### Media Rules (Uploads vs Links)

**Link Attachments (Shareable):**
- URL string stored in cloud; shareable when flow shared via unlisted link
- Links may be added by Free and Pro
- If link becomes invalid, show non-blocking warning ("Link unavailable"); allow edit/remove
- Links included in import payloads per sender's share settings

**Uploaded Media (Private-only):**
- Uploads are Pro-only; count toward 2GB total cap (locked)
- Uploads are private-only: recipients of shared flow do not receive uploaded media
- Share screen must show disclosure: "Uploads won't be shared. Add links if you want recipients to see media."
- Uploads must have per-file constraints (placeholder) and metadata: file size, mime type, duration, createdAt, internal storage path

**Size Accounting:**
- Only uploaded media counts toward 2GB cap
- Text data (moves/flows/logs) not counted in user-facing "Media Storage" meter unless separate quota added later
- Storage meter shows: used bytes, remaining bytes, top contributors (largest files)
- Deleting upload frees quota after backend confirmation; until confirmed, show "Pending cleanup" state

#### Limit Enforcement UX

**Pro Media Cap (2GB):**
- When upload would exceed remaining quota, block immediately (do not start upload)
- Show blocking modal with: current usage, remaining storage, upload size estimate, actions: [Manage Storage] [Cancel] [Upgrade] (Upgrade shown only if not Pro/trial)
- Manage Storage opens screen listing uploads sorted by size with multi-select delete
- If user deletes uploads, re-check quota before re-attempting upload

**Free Flow Cap (2 saved flows):**
- If Free tries to save new flow beyond cap, block save and show upgrade gate:
  - Option A: Delete existing saved flow (with clear warning)
  - Option B: Upgrade to Pro (7-day trial per CH25)
- If action from Inbox "Save to Library," see Inbox rules

**Free Inbox Cap (10 items):**
- If inbox full, new imports not accepted
- Receiving flow link screen shows: "Inbox full (10). Save or delete an item to accept new imports."
- Pro users may have higher cap (placeholder; CH18/CH30)

**Guest Restrictions:**
- Guest cannot save flows/moves; any attempt triggers account creation gate (CH07)
- Guest cannot store uploads or links persistently

#### Sharing & Imports: Storage-Safe Rules

**Share Payload Composition:**
- Shared flow link resolves to: flow graph + referenced moves + link attachments + text notes per share settings
- Upload attachments replaced with placeholders: {type: 'upload', shared: false}
- Recipients see disclosure: "This flow contains private uploads that are not shared."
- Recipient can still import; imported version preserves placeholder markers

**Inbox -> Library Save (Free cap interactions):**
- When Free saves inbox item to library, counts toward 2-flow cap
- If at cap, save blocked; user prompted to delete saved flow or upgrade (no silent overwrite)
- Free can view inbox items without saving but cannot practice them (locked)

**Import does not bypass practice paywall:**
- Imports allowed for Free (preserves viral funnel)
- Practicing imported content requires: save to library (subject to cap) and use monthly credits on saved flows, OR upgrade to Pro
- Inbox items never consume practice credits directly (locked); credits only usable on saved flows

#### Implementation Reference (Firebase-first)

**Reference Storage Components:**
- Firestore: primary database for user content
- Cloud Storage: Pro media uploads (counted toward 2GB)
- Authentication: Apple/Google/Email providers (CH07)
- Cloud Functions (recommended): secure share-link generation, quota checks, cleanup tasks

**Abstraction Boundary:**
- Implement thin "StorageService" interface: saveFlow, fetchFlow, uploadMedia, listMedia, deleteMedia, estimateQuota
- Makes switching to Supabase or other backend later easier

#### Security & Privacy (Storage-Specific)
- All user content private by default; sharing only through unlisted links (CH17)
- Share link tokens must be unguessable and revocable; never use sequential IDs
- Uploads private-only: even with link, recipient cannot access sender's uploaded media
- Rate-limit share link creation and imports (CH30 owns thresholds)
- Do not log or store raw video frames or biometric data in V1
- If user deletes account, all stored media and metadata must be deleted (CH34)

### Placeholders (CH29)
| ID | Description | Options | Default | Decide-by |
|----|-------------|---------|---------|-----------|
| - | Max upload file size | 50MB / 100MB / 250MB | 100MB | before TestFlight |
| - | Allowed upload types | video-only / video+gif | video-only | - |
| - | Max link attachments per move/flow | 1 / 3 / unlimited | 3 | CH11/CH16 |
| - | Text field caps (notes, sequence details) | 500/2000/10000 chars | 2000 | CH14/CH13 |
| - | Pro inbox cap | 50 / 200 / unlimited (soft) | 200 | CH18 |
| - | Share payload media policy for links | include previews? | TBD | CH17/CH19 |

---

## CH30 — Safety/Abuse Limits & Warning Ladder

### Overview
Defines the complete V1-shippable safety and anti-abuse system: what constitutes abuse, the user-visible warning ladder, plan-aware caps and soft caps, enforcement behavior across Guest/Free/Pro/Trial, and recovery/support paths.

### Dependencies
- CH00, CH08, CH17, CH18, CH20-CH22, CH29, CH31

### Related
- CH25, CH27, CH33, CH34

### Non-Goals
- Community moderation, public content moderation, harassment reporting, gym/community roles (out of V1)
- This chapter is strictly about protecting app resources, preventing spam/automation, avoiding accidental data-loss, and preserving user trust

### Threat Model

**We protect against:**
- Resource abuse: automated/excessive creation of share links, imports, flows, or edits
- Spam distribution: mass generation of unlisted share links
- Storage abuse: attempts to exceed media/storage caps or upload/delete cycling
- Account farming: many guest sessions or new accounts from one device/network to bypass caps
- Denial of service vectors: repeated heavy operations (exporting, importing, huge flows)
- User self-harm via UX: confusing caps causing lost work or trapped feeling; punishing messages causing churn

**We do NOT protect against (V1):**
- Public feed toxicity (no public feed in V1)
- DM harassment (no DMs in V1)
- Copyright enforcement on user-linked videos

### Key Requirements

#### Global Principles (Non-Negotiable)
- **Soft caps by default**: Limits feel like guardrails, not punishments; warn early, only block when necessary
- **No silent failure**: When blocked or degraded, show why, what to do next, and how to recover
- **Don't destroy work**: If user hits cap, preserve drafts and offer clear choices (upgrade, delete, export, later)
- **Plan-aware but respectful**: Free usable (explore/build basics); Pro smooth but not unlimited
- **Reversible states**: Provide "revert" path when safety action changes user state
- **Transparent counting**: Show users what is counted toward cap and what is not

#### Warning Ladder (L0-L5)

**L0 — Normal:**
- No warnings; user actions proceed normally
- Light background counting; all counters tracked for potential escalation

**L1 — Informational Nudge:**
- Early, friendly notice approaching soft cap
- Non-blocking toast or inline banner
- Provides: current usage + cap + how it resets
- No slowdown; user can continue

**L2 — Soft Warning + Friction:**
- User crosses soft cap or repeats risky action quickly
- Inline warning card + optional cooldown (seconds/minutes)
- Optional confirmation step for repetitive actions
- Still allows completion for legitimate users

**L3 — Temporary Cooldown / Partial Block:**
- Clear misuse pattern or rapid repetition
- Action blocked for time window (e.g., 15-60 minutes) OR reduced (e.g., 1 link/hour)
- User sees countdown + reason + recovery options
- Other parts of app remain usable

**L4 — Feature Suspension:**
- Severe or repeated abuse
- Suspend feature category (e.g., share-link creation or media upload) for 24-72 hours
- User can still view own content
- Clear message + link to support + review steps

**L5 — Account Security Block:**
- High-confidence automation/fraud signals or repeated L4
- Block high-risk actions (and possibly login) until verification/support
- Show security screen with next steps
- Audit log entry is mandatory

**Ladder Escalation Rules:**
- Escalation is per-vector (e.g., L3 for links but L0 elsewhere)
- De-escalation happens automatically after cooldown windows unless signals persist
- Escalation requires at least one of: rate threshold exceeded, repeated failures to comply, suspicious signal
- Always log: vector, level, timestamp, device/session identifier, action counts, plan state

#### Limit Catalog

**Core Limits (Owned Elsewhere; Enforced Here):**
| Vector | Guest | Free | Pro/Trial | Owner |
|--------|-------|------|-----------|-------|
| Save flows to library | Blocked | Max 2 | Higher/uncapped (placeholder) | CH07, CH08, CH15 |
| Inbox capacity | N/A | Max 10 | Higher/uncapped (placeholder) | CH18 |
| Practice sessions | Blocked | 3 monthly credits on saved flows only | Unlimited (fair-use) | CH20-CH22, CH25 |
| Outgoing branches per move | N/A | Max 10 | Max 10 (V1) | CH12 |
| Video uploads | Blocked | Blocked | Allowed; 2GB total cap | CH29 |

**Abuse-Sensitive Vectors (Owned by CH30):**

| Vector | Abuse Risk | Ladder Behavior | Plan Notes |
|--------|------------|-----------------|------------|
| Share-link creation | Spam distribution & backend load | L1: approaching daily cap. L2: confirmation + cooldown. L3: temporary block. L4: 24-72h suspension. L5: security block | Free/Pro caps are placeholders; owned by CH17/CH25 |
| Import acceptance | Bypass flow limits or load app | Free: inbox cap 10 + 2-flow cap. Burst imports trigger L2 confirm + cooldown; L3 if continued | Keep sharing funnel alive |
| High-frequency edits | Backend stress from scripts | Server rate-limit; show L2 when hit; L3 cooldown if continued | Thresholds are placeholders |
| Video upload churn (Pro) | Storage probing | L1 at ~80% storage. L2 at ~95%. L3 cooldown on repeated failed attempts. L4 upload suspension | Storage cap owned by CH29 |

#### Required UX Behaviors at Caps

**Free hits saved-flow cap (2):**
- Allow viewing/editing existing saved flows
- Block saving additional flows
- Show blocking modal with: Upgrade to Pro, Delete a saved flow, Cancel (plus Export/Copy if CH34 supports)

**Free tries to practice:**
- If credits remaining and flow saved: allow
- If credits are 0: show paywall (CH25)
- If flow from inbox (not saved): block and explain 'Practice requires a saved flow' + CTA to save

**Free inbox full (10):**
- Receiving new imports triggers 'Inbox full' screen
- Provide: Delete inbox items, Save one to library (if under cap), Upgrade to Pro

**Guest tries to save:**
- Show conversion wall with Apple/Google/Email options (CH07) + Cancel

**Pro approaches 2GB storage:**
- Show storage meter on upload UI
- L1 banner at ~80%; hard block at 100%
- Actions: delete uploads or use links

#### Implementation Rules

**Single Source of Truth for Limits:**
- All caps/thresholds in single configuration object (server-delivered if possible)
- Client reads config for counters/messaging; server enforces same config

**Required Config Fields (V1):**
- planStates: guest/free/pro/trial
- caps: savedFlowsFree=2; inboxFree=10; practiceCreditsFreeMonthly=3; storageProBytes=2GB
- ladder: per-vector thresholds; cooldown durations; suspension durations
- copyKeys: mapping from vector+level to copy template keys
- resets: monthly credit reset; daily link reset; rolling-window counts

**Suspicious-Activity Signals (V1 Minimal):**
- Very high rate actions beyond plausible human use
- Repeated failed attempts to perform blocked actions
- Many new accounts from same device/session in short window
- High-frequency import acceptance with immediate delete cycles
- Unusually large flows or repeated attempts to create extremely large graphs

**Important:** Treat signals as probabilistic; prefer cooldowns over permanent bans; avoid false positives for real coaches building big gameplans.

**Placeholder Thresholds (Must Be Decided Later):**
- Exact share-link creation caps (Free/Pro) and rolling-window rate limits
- Edit-rate limit thresholds allowing 2-hour build sessions
- Import burst thresholds that catch automation without blocking normal coach sharing
- Cooldown durations per ladder level
- Storage warning thresholds (recommended 80%/95%)

#### Telemetry Requirements
- limit_counter_incremented (vector, plan, count, window)
- ladder_level_shown (vector, level, plan)
- action_blocked (vector, reason, plan, level)
- cooldown_started / cooldown_ended
- upgrade_cta_clicked (from which limit screen)
- user_deleted_item_to_resolve_limit (flow/inbox/media)

#### UX Copy Rules

**Mandatory Copy Components:**
- What happened: 'You've reached your Free plan limit for saved flows.'
- Why (1 sentence max): 'This keeps Handz fast and prevents spam.'
- What you can do next: 'Delete one flow, upgrade, or come back later.'
- How counting works: show count and limit: '2 of 2 saved.'
- Reset info (when applicable): 'Credits refresh monthly.' 'Link limits reset daily.'

**Reusable Templates:**
| Template | Copy |
|----------|------|
| Approaching soft cap (L1) | Heads up — you're close to the limit for {THING} ({COUNT} of {LIMIT}). |
| Soft warning (L2) | You're doing this a lot quickly. To keep Handz fast, we'll add a short cooldown ({COOLDOWN}). |
| Cooldown (L3) | Temporarily paused: {THING}. Try again in {TIME_REMAINING}. |
| Feature suspension (L4) | {THING} is suspended for {DURATION} due to unusual activity. If this is a mistake, contact support. |
| Guest save wall | Create an account to save your work. It takes under a minute. |
| Practice paywall (credits 0) | Practice is a Pro feature. You've used your monthly credits. Start a 7-day free trial to keep drilling. |
| Inbox full (Free) | Your inbox is full ({COUNT} of {LIMIT}). Delete items or save one to your library. |

**UX Surfaces:**
- Toast: L1 informational (non-blocking)
- Inline banner: L1-L2
- Blocking modal: when action cannot proceed (cap reached) — must include next actions
- Dedicated 'Limit' screen: L3-L5, with countdown and support link
- Settings > Plan: shows current counters and reset dates

#### Accessibility & Fairness
- Do not rely on color alone to communicate severity (use icons + text)
- Cooldown timers readable by screen readers and support dynamic type
- Glove-on users: do not show non-critical safety prompts mid-practice timer; queue for session end
- Allow dismissal for L1/L2 banners but keep path to re-open details
- Explain plan gating without shaming: 'Free plan includes 2 saved flows' instead of 'You're not allowed'

### Placeholders (CH30)
- Share-link caps + thresholds: TBD (owned by CH17/CH25)
- Cooldown durations per level: TBD
- Edit/import rate thresholds: TBD
- Admin tooling scope: TBD

---

## CH31 — Error States

### Overview
Defines every error state in Handz V1: how errors are categorized, surfaced in UI, exact copy, actions offered, logging, and prevention of error loops. CH31 specifies user-facing behavior when other systems fail or block.

### Dependencies
- CH04, CH06, CH07, CH08, CH12, CH15, CH17, CH18, CH19, CH20, CH21, CH22, CH23, CH24, CH25, CH28, CH29, CH30

### Related
- CH01, CH02, CH03, CH05, CH09-CH11, CH13, CH14, CH26, CH27, CH32, CH33, CH34, CH35, CH36, CH37, CH38

### Non-Goal
- Inventing new product rules; if error reveals missing logic, create PLACEHOLDER and reference owning chapter

### Key Requirements

#### Error Taxonomy

**Severity Levels (S0-S4):**
| Level | Description |
|-------|-------------|
| S0 Info | No user action required; optional toast/badge; never blocks flow |
| S1 Warning | User can continue; warn about limits or partial failures; offer safe default action |
| S2 Recoverable Error | Action failed; user can retry or choose alternative; show clear next step |
| S3 Blocking Error | User cannot proceed on current path; must resolve or exit; provide one primary action |
| S4 Critical | Corrupted state / security block / crash risk; hard-stop and route to safe screen; capture diagnostics |

**Error Surfaces:**
| Surface | Usage |
|---------|-------|
| Toast (2.5s) | S0-S2; short message; optional single action ("Retry") |
| Inline field error | S1-S2 validation; under field; clears on edit |
| Inline empty-state panel | "Nothing here yet" + primary CTA |
| Banner (persistent) | S1-S3; under top bar; dismissible if S1-S2; non-dismissible if S3 |
| Modal | S2-S4; requires acknowledgement; 1-2 actions max |
| Full-screen fallback | S3-S4; when entire screen cannot load or state is unsafe |

#### Copy Rules
- Be specific about what happened and what to do next; avoid vague "Something went wrong"
- Never blame the user; use neutral language ("We couldn't...")
- One primary action; optional secondary action is "Not now" or "Cancel"
- When restriction due to plan state, say so once and link to paywall/account screen (CH25)
- When restriction due to safety/abuse controls, reference "Safety limits" and route to CH30 warning ladder UI
- Do not show raw server error strings; log them, show user-facing copy

#### Logging Rules
- Every S2+ error emits: error_id, surface, screen_id, action_attempted, plan_state, network_status, timestamp, sanitized technical payload
- S3+ includes stable "incident_id" for support/debug correlation
- Never log user-entered free text verbatim (privacy); log length + field name instead
- If retry succeeds, emit "recovered" event with original incident_id

#### Global Error Components
- **HandzToast**: toast queue; max 1 visible; coalesce duplicates within 5s
- **HandzBanner**: persistent banner region below top bar; supports single action button
- **HandzEmptyState**: illustration slot + title + body + primary CTA + optional secondary link
- **HandzModal**: standardized modal with title, body, primary CTA, secondary CTA
- **HandzFullScreenFallback**: when screen cannot render safely; contains "Go back" and "Try again"

#### Retry Behavior Standard
- Idempotent calls (GET/read): allow unlimited retries with exponential backoff after 3 rapid retries
- Non-idempotent calls (POST/create): allow retry only if client-generated request_id and server supports de-duplication; otherwise show "Try again later"
- All retries preserve user input; never clear forms on failure

#### Degraded Mode Standard
- If network offline: banner "Offline — some actions are unavailable"; disable network-dependent buttons with inline explanation
- If entitlements cannot be verified: default to safer side (Free behavior) but show banner "Can't verify subscription — some Pro features may be locked until you reconnect."
- If remote config fails: use built-in defaults; log S1 warning once per session

#### Error Dictionary (Authoritative)

**Network and Service Availability:**
| ERR ID | Description | Severity | Surface | Copy |
|--------|-------------|----------|---------|------|
| ERR-001 | No internet connection | S2 | Banner + Toast | "Offline. You're not connected. You can keep viewing saved content, but actions that require internet won't work." |
| ERR-002 | Request timed out | S2 | Toast (with Retry) | "That took too long. Try again. If it keeps happening, check your connection." |
| ERR-003 | Service temporarily unavailable | S3 | Full-screen fallback | "Handz is down right now. We're having trouble on our side. Try again in a bit." |

**Authentication and Session:**
| ERR ID | Description | Severity | Surface | Copy |
|--------|-------------|----------|---------|------|
| ERR-010 | Session expired | S3 | Modal | "Session expired. Please log in again to continue." |
| ERR-011 | Apple/Google sign-in failed | S2 | Modal | "Couldn't sign you in. Try again, or choose a different sign-in method." |
| ERR-012 | Email verification required | S3 | Full-screen interstitial | "Verify your email. Check your inbox for a verification link, then come back here." |

**Guest Restrictions:**
| ERR ID | Description | Severity | Surface | Copy |
|--------|-------------|----------|---------|------|
| ERR-020 | Guest cannot save flows | S3 | Modal | "Create an account to save. Guest mode lets you explore, but saving flows requires an account." |
| ERR-021 | Guest cannot customize move library | S2 | Banner/Modal | "Account required. To personalize your move library, create an account." |

**Free Plan Caps and Paywalls:**
| ERR ID | Description | Severity | Surface | Copy |
|--------|-------------|----------|---------|------|
| ERR-030 | Free saved flow limit reached | S3 | Modal | "Flow limit reached. Free accounts can save up to 2 flows. Upgrade to save more, or delete one to continue." |
| ERR-031 | Practice is Pro (credits required) | S3 | Paywall interstitial | "Practice Mode is Pro. Use Practice Mode to drill your paths and track progress. Start a 7-day trial or upgrade to Pro." |
| ERR-032 | Free inbox cap reached | S2 | Modal | "Inbox is full. Free accounts can hold up to 10 imports in Inbox. Delete one to make room." |

**Storage, Media, and Limits:**
| ERR ID | Description | Severity | Surface | Copy |
|--------|-------------|----------|---------|------|
| ERR-040 | Upload size limit reached (2GB) | S3 | Modal | "Storage limit reached. You've hit your 2GB upload limit. Delete an upload to free space, or switch to links." |
| ERR-041 | Video upload not shareable | S1 | Inline info + Toast | "Uploads stay private. Uploaded videos don't travel with shared links. Use a link if you need others to see it." |
| ERR-042 | Invalid video link | S2 | Inline field error | "Check the link. That link doesn't look usable. Paste a direct URL to the video." |

**Flow Builder:**
| ERR ID | Description | Severity | Surface | Copy |
|--------|-------------|----------|---------|------|
| ERR-050 | Cannot add branch (cap reached) | S2 | Toast + disabled button | "Branch limit reached. This move already has 10 next options. Replace one or merge paths." |
| ERR-051 | Invalid connection | S2 | Toast | "Can't connect that way. That connection would create an invalid loop. Try connecting to a different move." |
| ERR-052 | Unsaved changes warning | S1 | Modal | "Discard changes? You have unsaved changes. Do you want to keep editing or discard?" |

**Sharing, Inbox, Imports:**
| ERR ID | Description | Severity | Surface | Copy |
|--------|-------------|----------|---------|------|
| ERR-060 | Share link creation failed | S2 | Toast (Retry) | "Couldn't create link. Try again. If it keeps failing, check your connection." |
| ERR-061 | Share link expired or revoked | S3 | Full-screen fallback | "Link not available. This share link has expired or was revoked." |
| ERR-062 | Import contains missing moves | S2 | Modal (resolver) | "Some moves are missing. This flow includes moves you don't have yet. Choose how you want to handle them." |
| ERR-063 | Free user cannot practice inbox items | S2 | Banner + disabled CTA | "Practice requires saving. To practice this, save it to your library first. Practice works on saved flows only." |

**Practice Mode:**
| ERR ID | Description | Severity | Surface | Copy |
|--------|-------------|----------|---------|------|
| ERR-070 | Practice session failed to start | S3 | Full-screen fallback | "Can't start practice. One of your selected paths is missing. Edit your selection and try again." |
| ERR-071 | Timer desync detected | S2 | Banner | "Timer adjusted. We corrected the timer based on actual time passed." |
| ERR-072 | Practice log save failed | S2 | Modal | "Couldn't save your session. Your session is stored on this device for now. We'll try again when you're back online." |

**Mastery and Maintenance:**
| ERR ID | Description | Severity | Surface | Copy |
|--------|-------------|----------|---------|------|
| ERR-080 | Maintenance overload | S1 | Inline panel | "That's a lot to maintain. At your current schedule, this plan may feel overwhelming. Consider narrowing to your top paths." |
| ERR-081 | Mastery confidence mismatch | S1 | Inline prompt | "Adjust mastery. If this doesn't feel locked in, you can step it back. That updates your plan going forward." |

**Permissions:**
| ERR ID | Description | Severity | Surface | Copy |
|--------|-------------|----------|---------|------|
| ERR-090 | Photos permission denied | S3 | Modal | "Photos access needed. Allow Photos access in Settings to upload videos from your library." |
| ERR-091 | Notifications disabled | S1 | Banner | "Notifications are off. To receive reminders, turn on notifications in Settings." |

#### Empty States (Authoritative)
| ID | Title | Primary CTA | Behavior |
|----|-------|-------------|----------|
| EMPTY-01 | Start your first flow | Create Flow | Routes to Flow Builder with move picker open |
| EMPTY-02 | Add a move to start building flows | Add Move | Routes to Add Move screen |
| EMPTY-03 | No imports yet | Learn how sharing works | Opens short explainer |
| EMPTY-04 | Your practice sessions will show up here | Start a session | Routes to paywall if not entitled; otherwise Practice Setup |
| EMPTY-05 | Pick a gameplan to maintain | Create Gameplan | Routes to gameplan builder (CH23) |

### Placeholders (CH31)
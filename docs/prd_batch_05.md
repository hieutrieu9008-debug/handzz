# PRD Batch 05 — CH17-CH19 (Sharing, Inbox, Import Conflict Resolution)

**Generated:** 2026-01-05
**Chapters:** CH17, CH18, CH19
**Status:** Extracted

---

## CH17 — Sharing: Unlisted Links + Link Lifecycle

### Overview
Specifies V1 sharing system: creating unlisted share links for saved flows, opening/viewing links in read-only viewer mode, managing link lifecycle (copy/share/revoke/optional expiry), and abuse-resistance mechanics.

### Dependencies
- CH04 (Navigation Map)
- CH07 (Auth)
- CH08 (Entitlements)
- CH16 (Flow Detail)
- CH18 (Inbox)
- CH19 (Import Conflicts)
- CH25 (Paywalls)
- CH28 (Offline)
- CH30 (Safety/Abuse)

### Definitions (CH17-specific)
- **Unlisted Link:** Share link not discoverable in-app; only accessible via URL
- **Link Token:** Unguessable identifier in URL; minimum 128 bits entropy; server stores hashed
- **Link Record:** Backend record mapping token to flow snapshot reference + metadata
- **Viewer Mode:** Read-only flow view for recipients; editing disabled
- **Link Lifecycle States:** CREATED -> ACTIVE -> (REVOKED | EXPIRED | DISABLED)

### Key Requirements

#### Share Link Creation
- Only saved flows can be shared (guests cannot save flows)
- Guest: Cannot create share links
- Free: Can create unlisted share links; subject to soft caps and abuse limits
- Pro/Trial: Same as Free but higher caps and fewer friction warnings
- Token must be unguessable (min 128 bits entropy, recommend 160-192)
- Token must NOT encode user_id or flow_id directly
- No incremental IDs in URLs
- Server stores only hash of token (SHA-256)

#### Share Link Opening
- Anyone with link can open and view in Viewer Mode
- Import/save actions may require account and may be capped (See CH18)
- If guest: can view but cannot save/import until account created

#### Link Lifecycle State Machine
- **CREATED:** Record exists but not yet visible (rare; typically immediate to ACTIVE)
- **ACTIVE:** Link opens successfully into Viewer Mode
- **REVOKED:** Owner revoked; must not reveal flow content
- **EXPIRED (optional V1):** Time-expired; treat like REVOKED for UX
- **DISABLED:** System-disabled for abuse/safety (See CH30)

#### Transition Rules
- State transitions are server-authoritative; client caches never trusted
- Allowed: CREATED->ACTIVE, ACTIVE->REVOKED, ACTIVE->DISABLED, ACTIVE->EXPIRED
- REVOKED->ACTIVE: Default V1 = No (create new link instead)
- DISABLED->ACTIVE: Rare; admin/unban only

#### Revocation Semantics
- Revocation must be immediate
- Revoking does not delete underlying flow
- Revocation is irreversible in V1 (create new link to reshare)

#### Flow Deletion/Rename Impact
- If owner deletes flow: all links become invalid; show "This flow is no longer available"
- If owner renames flow: link remains valid; viewer shows updated name
- If owner changes content: depends on snapshot policy

#### Snapshot Policy (V1 Decision)
- **Default V1:** Show current flow version (not snapshot at share time)
- Pros: Coach updates flow, students see latest
- Cons: Link content can change after sharing
- Mitigation: Show "Last updated" timestamp in viewer header

#### Data Model (share_link record)
```
share_link:
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

#### URL Format
- `https://handz.app/s/<token>`
- Alternate: `handz://share/<token>` or `https://handz.app/share/<token>`

#### Entry Points
- **Primary:** Flow Detail View (Share, Copy Link, Manage Links)
- **Secondary (optional V1):** Library list item overflow menu
- **Recipient:** Tap URL -> app opens Share Link Viewer (or web landing if app not installed)

#### Owner UX: Copy Link Behavior
- If existing ACTIVE link: copy most-recent ACTIVE link
- If no ACTIVE link: create new ACTIVE link, then copy
- If over cap: show cap warning and path to manage/revoke old links

#### Toast/Feedback Copy
- Success (existing): "Link copied"
- Success (created): "New link created and copied"
- Failure (offline): "No connection - try again"
- Failure (cap): "Link limit reached - revoke one to create a new link"

#### Share Sheet Screen
- Title: "Share Flow"
- Flow card summary (name, last updated, optional tags)
- Primary: "Copy link"
- Secondary: "Share..." (OS share sheet)
- Safety note: "Anyone with this link can view this flow."
- Video note (if uploads present): "Uploaded videos aren't included in shared links. Links to videos are shareable."
- "Manage links" entry

#### Manage Links Screen
- List links for this flow with status pills (ACTIVE/REVOKED/EXPIRED/DISABLED)
- Created date/time
- Optional: "Opened X times"
- Actions: Copy, Share..., Revoke (ACTIVE only)
- Empty state: "No links yet. Create one to share this flow."

#### Recipient UX: Link Opening
- ACTIVE token -> Viewer Mode
- REVOKED/EXPIRED -> "Link not available" screen
- DISABLED -> "Link not available" (different copy; avoid accusing user)
- Invalid/not found -> "Link not found" screen

#### Viewer Mode Requirements
- Must visually communicate read-only mode ("Viewer mode" badge)
- Flow content viewable with pan/zoom (See CH12)
- Editing controls hidden or disabled
- If logged in: show "Save to Inbox" (or "Import") button
- If not logged in: show "Create account to save" button

#### Educational Copy (Viewer Mode)
- Header: "This is a Handz flow - a map of what to throw next based on what your opponent does."
- Subtext: "Save it to your Inbox to practice or edit later."
- Dismiss: "Got it" / "Don't show again"

#### Media Attachment Rule (V1)
- Uploaded videos are private-only; count toward 2GB cap; NOT included in share links
- External video links (URLs) ARE shareable
- Viewer Mode must show badge: "Video not shared (private upload)"

#### Copy Pack (V1 Strings)
| Key | String |
|-----|--------|
| Share sheet title | "Share flow" |
| Share sheet note | "Anyone with this link can view this flow." |
| Copy link button | "Copy link" |
| Share button | "Share..." |
| Manage links | "Manage links" |
| Copied toast | "Link copied" |
| Created+copied toast | "New link created and copied" |
| Cap reached | "Link limit reached - revoke one to create a new link" |
| Revoked confirmation title | "Revoke this link?" |
| Revoked confirmation body | "Anyone who has it won't be able to open this flow anymore." |
| Revoked confirm CTA | "Revoke link" |
| Revoked success | "Link revoked" |
| Link not found title | "Link not found" |
| Link not found body | "This link doesn't exist or was typed wrong." |
| Link not available title | "Link not available" |
| Link not available body | "This link was revoked or expired." |
| Link disabled body | "This link is no longer available." |
| Viewer badge | "Viewer mode" |
| Viewer education header | "Flows map what to throw next." |
| Viewer education body | "Save to your Inbox to practice or edit later." |
| Guest CTA | "Create an account to save this flow" |

#### Error Handling
- **Owner-side offline:** Keep on share sheet, show retry
- **Copy/share OS failure:** Keep link ACTIVE, show "Couldn't copy - try again"
- **Cap reached:** Show cap message, route to Manage Links; optional quick "Revoke oldest link"
- **Token not found:** Show Link not found screen; "Go to Home"
- **Revoked/expired:** Show Link not available; "Ask sender for a new link"
- **Content removed (flow deleted):** "This flow is no longer available."

#### Data Consistency
- Viewer Mode must always validate link status from server on open
- If validation timeout: show skeleton/loading then "Try again"
- If link revoked while viewer open: allow viewing to continue; refresh/import revalidates

#### Anti-Abuse Mechanics
- Rate limit link creation (rolling window)
- Rate limit link opens from same IP/device
- Flag suspicious patterns (many creates, opens, high failure rates); set abuse_flag
- System can DISABLE link and/or block link creation for account (See CH30)
- Use soft caps first (warnings), then block at high thresholds
- Never show accusatory copy; keep neutral

### Placeholders (CH17)
| ID | Description | Options | Default | Decide-by |
|----|-------------|---------|---------|-----------|
| SHARE_DAILY_CREATE_CAP_FREE | Daily link create cap (Free) | 5/10/20 per 24h | 10 | before beta |
| SHARE_DAILY_CREATE_CAP_PRO | Daily link create cap (Pro) | 25/50/100 per 24h | 50 | before beta |
| SHARE_ACTIVE_LINK_CAP_FREE | Active link cap (Free) | 10/25/50 | 25 | before beta |
| SHARE_ACTIVE_LINK_CAP_PRO | Active link cap (Pro) | 100/250/500 | 250 | before beta |
| SHARE_PER_FLOW_ACTIVE_CAP | Per-flow active links | none/3/5 | 5 | before beta |
| LINK_EXPIRY_SUPPORT | Link expiry | Off/7days/30days | Off | beta |
| DEEP_LINK_TRANSPORT | Deep link transport | Universal Links / dynamic links | Universal Links | alpha |
| LINK_STATS_VISIBILITY | Show opens to owner | show/hide | show opens only to owner | beta |

---

## CH18 — Inbox: Receiving Imports

### Overview
Defines the receiving-side experience: what happens when someone opens an import link, where it lands, how Inbox works, and how users move imported flows into their library without breaking plan limits.

### Dependencies
- CH07 (Auth & Account)
- CH08 (Entitlements)
- CH17 (Sharing Links)
- CH19 (Import Conflicts)
- CH15 (Library)
- CH16 (Flow Detail)
- CH30 (Safety/Abuse)
- CH31 (Error States)

### V1 Locks (from CH00)
- **Inbox cap (Free):** 10 items
- **Free:** Can view Inbox items but CANNOT practice Inbox items
- **Practice:** Paywalled; Free receives 3 monthly practice credits usable ONLY on saved flows (not Inbox)
- **Saved flows cap (Free):** 2 saved flows
- **Uploads:** Pro-only, private-only; uploads not shareable; links shareable

### Definitions (CH18-specific)
- **Import link:** Unlisted URL from sender (CH17) resolving to Flow Snapshot payload
- **Flow Snapshot:** Immutable copy of sender's flow content
- **Inbox Item:** Stored import record under receiving user's account; staging area before Library
- **Add to Library:** Turn Inbox Item into user-owned Flow in Library (subject to caps)
- **Preview mode:** View-only rendering; no edits; no practice; no persistence unless authenticated
- **Conflicts:** Mismatches between sender payload and receiver library (See CH19)
- **Inbox Overflow:** User at inbox cap; block saving, show upgrade/manage path

### Key Requirements

#### User Journeys

**Open Import Link (all users):**
1. User taps import link (SMS/Notes/AirDrop/etc)
2. If app not installed: web landing page instructs install, then re-open
3. If app installed: open directly to Import Landing
4. Fetch Flow Snapshot from backend using link token
5. Display: Flow name, summary, creator display name, badges (custom moves, links, private uploads, node/branch count)
6. Primary CTAs depend on entitlements

**Save to Inbox (Free/Pro only):**
1. User taps "Save to Inbox"
2. If Inbox not full: create Inbox Item, navigate to Inbox Detail (or toast + go to Inbox list)
3. If Inbox full: block, show Inbox Full modal
4. Inbox Item remains view-only until moved to Library

**Add to Library (Free/Pro, gated by caps):**
1. From Import Landing or Inbox Item, tap "Add to Library"
2. If CH19 conflicts exist: run conflict resolution flow first
3. If Free at 2 saved flows: block, show paywall/management options
4. If allowed: create new user-owned Flow in Library with new Flow ID
5. Remove (or mark converted) Inbox Item

**Guest Behavior (preview-only):**
- Can open import link and preview flow
- Cannot Save to Inbox or Add to Library
- See CTAs to create account (Apple/Google/Email)
- See explanation of what saving enables

#### Entitlement Matrix

| State | Save to Inbox | Add to Library | Practice Inbox | Practice Saved |
|-------|---------------|----------------|----------------|----------------|
| Guest | No | No | No | No |
| Free | Yes (cap 10) | Yes (if <2 saved) | No | Yes (3 credits/mo) |
| Pro/Trial | Yes (cap TBD) | Yes (no cap) | No | Yes (unlimited) |

#### CTA Visibility on Import Landing

**Guest:**
- Primary: "Create account to save"
- Secondary: "Preview"
- Tertiary: "Why create an account?" (mini sheet)

**Free:**
- Primary: "Save to Inbox" (if not full)
- Secondary: "Add to Library" (if under saved cap)
- Secondary alt (at cap): "Upgrade to Pro to save" or "Manage saved flows"
- Always: "Preview"

**Pro/Trial:**
- Primary: "Add to Library"
- Secondary: "Save to Inbox"
- Always: "Preview"

#### Screens

**Import Landing (/import/:token)**
- Header: Back/close button, title "Import Flow"
- Flow card: Name, Creator line, chips (Custom Moves, Links Included, Uploads Not Shared), meta row (Nodes/Branches/Updated)
- Preview snippet: Mini thumbnail or first 5 nodes; tap opens full Preview
- Primary actions: Per entitlement
- Trust note: "This is an unlisted link. Only people with the link can view it."
- If links present: "External video links may open outside the app."
- If uploads referenced: "Uploaded videos are private and won't transfer via link."

**Import Preview (view-only, /import/:token/preview)**
- Header: Back, Flow Name, overflow menu (Report issue)
- Flow renderer: Same as Flow Detail (CH16) but strict view-only; no edit controls; pan/zoom per CH12
- Tapping nodes opens Node Detail read-only sheet
- Bottom action bar: Same CTAs as Import Landing

**Inbox List (/library/inbox)**
- Header: "Inbox", Info icon, Manage icon
- Subheader counter: "X/10 saved" (Free)
- Empty state: "No imports yet" + "When someone sends you a Handz link, it'll show up here after you save it." + "Learn how sharing works" CTA
- List item card: Flow Name, From: Creator, Received date (relative), badges (Unopened, Conflicts, Link Revoked, Custom Moves, Links Included, Uploads Not Shared)
- Actions: View (primary), Add to Library, Delete (swipe/kebab)
- Footer (Free): Soft warning at 8/10, "Inbox full" strip at 10/10 with Upgrade/Manage CTAs

**Inbox Item Detail (/library/inbox/:inboxItemId)**
- Header: Back, Flow Name, overflow (Delete, Report, Source info)
- Source block: From, Received, Link status (Active/Revoked/Unknown)
- Flow preview: Same view-only renderer using stored Inbox payload
- Action area: Primary "Add to Library", Secondary "Delete from Inbox"
- Note: "You can't practice from Inbox. Add to Library first."
- If conflicts: Compact panel with "Resolve & Add to Library" button (starts CH19)

#### Inbox Item Lifecycle

**Creation Rules:**
- Created only when signed-in Free/Pro/Trial taps "Save to Inbox"
- Stores snapshot payload (usable even if original link revoked later)
- View-only until moved to Library (no edits, practice, exports)
- Does NOT count against "Saved flows" cap until added to Library

**Viewing Rules:**
- Always allowed for owner user
- If original link revoked: snapshot still opens; banner: "Source link is no longer active. This is your saved copy."

**Deletion Rules:**
- Permanently removes from account
- Confirmable with undo toast (5 seconds)
- If offline: queue delete; show "Will delete when you're back online"

**Conversion Rules (Inbox -> Library):**
- Initiated by "Add to Library"
- If conflicts: route through CH19 first
- On success: Create new user-owned Flow (new Flow ID), set metadata (importedFrom: creatorId, shareLinkId, importedAt), remove Inbox Item (or mark converted)
- If fails mid-way: Inbox item unchanged; user can retry

#### Free Inbox Cap (10 items)

**Soft Warning (at 8/10):**
- Non-blocking banner on Inbox List and Import Landing
- "Inbox almost full (8/10). Delete items or upgrade."
- Buttons: "Manage Inbox", "Upgrade"

**Hard Cap (at 10/10):**
- Block Save to Inbox
- Modal: "Inbox Full"
- Body: "Your Free plan can hold 10 imports. Delete one to save this, or upgrade for a bigger inbox."
- Buttons: "Manage Inbox" (primary), "Upgrade to Pro" (secondary), "Cancel"
- Can still Preview; can still Add to Library if under saved-flow cap

#### Saved-Flow Cap (Free cap = 2)

**Blocking State (Free at 2 saved flows):**
- Block Add to Library
- Modal: "Saved Flow Limit Reached"
- Body: "Free accounts can save up to 2 flows. Delete one to save this import, or upgrade to save unlimited flows and practice more."
- Buttons: "Upgrade to Pro" (primary), "Manage My Flows" (secondary), "Cancel"
- "Manage My Flows" routes to Library list with "Saved flows" filter
- After user deletes and returns: offer one-tap retry "Now save this import?"

#### Data Model (InboxItem)
```
InboxItem:
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

**snapshotPayload Requirements:**
- Must include: Flow graph (nodes, edges, layout), move references (canonical IDs + display names), sequence nodes (if present), shared notes, external video links
- Must exclude: Private uploaded media binary (include placeholders instead)
- Must include: payloadVersion number for migrations

#### Copy Requirements (CH18)

**Import Landing:**
- Title: "Import Flow"
- Primary (Guest): "Create account to save"
- Primary (Free): "Save to Inbox"
- Primary (Pro/Trial): "Add to Library"
- Secondary: "Preview"
- Upload note: "Uploaded videos are private and won't transfer via link."
- Always note: "This is an unlisted link. Only people with the link can view it."

**Inbox List:**
- Title: "Inbox"
- Empty headline: "No imports yet"
- Empty body: "When someone sends you a Handz link, you can save it here and add it to your library later."
- Soft warning: "Inbox almost full (8/10). Delete items or upgrade."
- Hard cap strip: "Inbox full. Delete an item or upgrade for a bigger inbox."

**Modals:**
- Inbox Full body: "Your Free plan can hold 10 imports. Delete one to save this, or upgrade for a bigger inbox."
- Saved Flow Limit body: "Free accounts can save up to 2 flows. Delete one to save this import, or upgrade to save unlimited flows and practice more."
- Guest Save Block body: "Create an account to save flows and access practice mode."

### Placeholders (CH18)
| ID | Description | Options | Default | Owner |
|----|-------------|---------|---------|-------|
| CH18-P1 | Pro inbox cap | 50/200/Unlimited | 200 | CH08/CH30 |
| CH18-P2 | Add-to-Library removes or marks Inbox item | Remove/Keep with status | Remove | CH18 |
| CH18-P3 | Save to Inbox from Preview only or also Landing | Both/Preview-only | Both | CH18 |
| CH18-P4 | Allow Save to Inbox if saved-flow cap reached | Allow/Block | Allow (view-only) | CH18/CH08 |
| CH18-P5 | Snapshot payload size limit | 256KB/512KB/1MB | 512KB | CH30 |
| CH18-P6 | Imported flow node limit (anti-abuse) | 150/300/500 | 300 | CH30 |
| CH18-P7 | Rate limiting for import saves | 10/30/60 per min | 30/min | CH30 |
| CH18-P8 | Report issue flow | email link/in-app form/none | email link | CH30/CH31 |
| CH18-P9 | Exported before adding to library | Yes/No | No in V1 | CH34 |
| CH18-P10 | Deep-link fallback web landing copy | - | simple install instructions | CH17 |
| CH18-P11 | Sender name/avatar shown or anonymized | show/anonymize | show display name only | CH17/CH07 |
| CH18-P12 | Analytics events | - | see CH33 | CH33 |

---

## CH19 — Import Conflict Resolution

### Overview
Specifies how Handz handles imports when incoming flow references moves or move details that don't cleanly match the recipient's current move library. Goal: deterministic mapping rules, minimal-but-safe user choices, predictable audit/revert model.

### Dependencies
- CH08 (Entitlements & Plan States)
- CH10 (Canonical IDs/Aliases/Families/Variants)
- CH17 (Share Links)
- CH18 (Inbox)
- CH29 (Media & Limits)
- CH11 (Revert Model)

### Definitions (CH19-specific)
- **Import Package:** Structured payload when sharing; includes flow graph + move descriptors
- **Recipient Library:** Recipient's saved moves and canonical records
- **Flow-local Move:** Move definition stored inside imported flow only (does not alter recipient library)
- **Canonical Move ID:** Stable identifier to map "same" move across users (See CH10)
- **Alias:** Alternate name string mapping to same canonical move family (See CH10)
- **Conflict:** Situation where Handz cannot map incoming move cleanly without losing info or overwriting user data
- **Preflight:** Review step before saving, summarizing conflicts and required choices

### Conflict Types (Authoritative)

| Class | Name | Description |
|-------|------|-------------|
| C1 | Missing Move | Incoming move cannot match any recipient library move via canonical ID or alias |
| C2 | Ambiguous Match | Incoming move matches multiple recipient moves |
| C3 | Sender Customization | Canonical match exists but sender metadata differs (notes/tags/attributes/media) |
| C4 | Media Incompatibility | Incoming move references uploaded media (not shareable in V1) |
| C5 | Schema Version Drift | Import package schema version differs; fields missing or extra |

### Import Lifecycle
1. **Step 0:** Open link (CH17); fetch import package
2. **Step 1:** Preflight (CH19); parse package, run mapping, classify conflicts
3. **Step 2:** Save to Inbox (CH18); store with stable import_id and resolution snapshot
4. **Step 3:** View imported flow; rendering works even if unresolved (using flow-local moves)
5. **Step 4:** Save to Library (CH18); requires account and plan caps

### Mapping Algorithm (Deterministic)

**Inputs:**
- Incoming move descriptor: canonical_id?, primary_name, aliases[], family_id?, variant_of?, attributes?, user_notes?, tags?, media_links[], uploaded_media_refs?
- Recipient move index: canonical_id, primary_name, aliases, normalized token set

**Matching Precedence (stop at first success):**
1. **P1 Canonical ID exact match:** incoming.canonical_id matches recipient move.canonical_id -> map
2. **P2 Explicit alias match:** any incoming.alias equals recipient.primary_name or recipient.alias -> candidate
3. **P3 Normalized name match:** compare normalized tokens (lowercase, trim, remove punctuation)
4. **P4 Family-based fallback:** if incoming.family_id exists and recipient has moves in that family, pick best normalized match; otherwise mark ambiguous
5. **P5 No match:** classify as C1 Missing Move

**Ambiguity Rules:**
- If P2-P4 yield exactly 1 candidate -> map to it
- If multiple candidates tie -> classify as C2 Ambiguous Match
- If recipient has multiple local variants with same name -> do not auto-pick; require user choice

**Output Mapping Object:**
```
{
  move_ref_id,
  resolution: 'mapped' | 'flow_local' | 'needs_choice',
  recipient_move_id?,
  flow_local_move_id?,
  conflict_class?,
  notes?
}
```

### Preflight UX

**Screen Layout:**
- Header: "Review Import" + flow name + sender handle
- Summary counters: Moves: X total, Conflicts: Y, Media issues: Z
- Primary CTA: [Save to Inbox] (disabled if required choices incomplete)
- Secondary: [Cancel]
- Optional: [View Flow Preview]

**Conflict Cards:**
- One card per conflict; grouped by class (Missing, Ambiguous, Sender Customizations, Media)
- Card header: incoming move name + badge
- Card body: short explanation + two allowed actions
- Default selection: safest choice (never overwrites library)
- Required vs optional: Missing + Ambiguous = required; Sender Customizations = optional

**Allowed Actions (V1 simplified):**
- **Action A — Use My Library:** Map to existing recipient move (keeps recipient's move as-is)
- **Action B — Use Sender Version (Flow-only):** Create/keep flow-local move (does not alter library)
- **Action C — Overwrite My Library:** Optional, gated (likely Pro only per CH08). Replaces recipient move metadata. Requires warning + one-tap revert entry

**Bulk Controls:**
- "Apply to all" for Sender Customizations only (default to Use My Library)
- No bulk apply for ambiguous matches

### Resolution Rules by Conflict Class

**C1 Missing Move:**
- Default: Action B (creates flow-local move)
- If incoming has canonical_id and recipient has that family -> show Action A with picker
- If user selects Action A -> map to chosen move; incoming descriptor kept as "source note" in audit

**C2 Ambiguous Match:**
- Default: no selection; user must choose
- Show picker list of candidates (name, family, tags, thumbnail)
- Action B as escape hatch

**C3 Sender Customization:**
- Default: Action A (ignore sender differences)
- If Action B selected -> show concise diff preview
- If Action C available -> require confirmation sheet: "This will update your move. You can revert later."

**C4 Media Incompatibility:**
- V1 rule: uploaded media is private-only
- If incoming references uploaded media -> drop it, show badge: "Video not included (upload is private)"
- If incoming includes shareable link -> keep it
- Never block import solely due to media issues

**C5 Schema Version Drift:**
- Unknown fields: ignored but preserved in raw payload
- Missing optional fields: default to empty
- Missing required fields (flow graph invalid): block save-to-inbox, show error with support code

### Plan State Gating
- **Guest:** Cannot save flows; cannot save to Inbox; can preview demo render; must create account to accept
- **Free:** Inbox cap 10; can accept to Inbox and view; cannot practice Inbox items; can save to Library if under cap (2)
- **Pro/Trial:** Normal import; Action C may be enabled; share link caps differ
- If Free at 2 saved flows and attempts Save to Library: show cap screen with [Manage Flows] / [Upgrade]

### Data Model

**Import Package Schema (V1):**
```json
{
  "schema_version": "1.0",
  "share_id": "<string>",
  "created_at": "<iso8601>",
  "sender": {
    "user_id": "<uuid?>",
    "handle": "<string?>",
    "display_name": "<string?>"
  },
  "flow": {
    "flow_id": "<uuid or sender-local id>",
    "name": "<string>",
    "description": "<string?>",
    "nodes": [...],
    "edges": [...]
  },
  "move_descriptors": [
    {
      "move_ref_id": "<string>",
      "canonical_id": "<string?>",
      "primary_name": "<string>",
      "aliases": ["<string>"],
      "family_id": "<string?>",
      "variant_of": "<canonical_id?>",
      "attributes": {...},
      "user_notes": "<string?>",
      "media_links": ["<url>"],
      "uploaded_media_refs": ["<opaque>"]
    }
  ]
}
```

**Inbox Item Persistence:**
- Stores: import_id, received_at, sender, flow_snapshot, resolution_snapshot, conflict_summary, raw_payload blob
- Resolution snapshot: chosen actions per move_ref_id + user selections for ambiguous matches
- When saved to Library: new flow record with embedded flow-local moves + pointer to import_id for audit/revert

**Audit + Revert Hooks:**
- Every import creates audit record (who sent, when, resolution actions)
- If overwrite (Action C): create per-move backup snapshot (references CH11 revert model)
- Expose "Revert changes from import" entry point on import detail screen

### Error States and Copy

**Blocking Errors:**
- Invalid package: "This link can't be imported right now." / "The shared flow is missing required data." / [Close] [Report Problem]
- Network failure: "Connection lost" / "Check your internet and try again." / [Try Again] [Cancel]
- Guest trying to accept: "Create an account to save" / "Guests can preview, but importing requires an account." / [Sign up] [Log in] [Cancel]

**Non-blocking Warnings:**
- Media dropped: "Video not included" / "Uploads are private. Ask the sender for a link."
- Customizations ignored: "Using your existing move details" / "Sender notes won't overwrite your library."

**Cap/Gating Messages:**
- Inbox full: "Inbox is full" / "Free accounts can hold up to 10 imports." / [Manage Inbox] [Upgrade]
- Saved flows full: "You've reached 2 saved flows" / "Delete one or upgrade to save more." / [Manage Flows] [Upgrade]

### Placeholders (CH19)
| ID | Description | Owner |
|----|-------------|-------|
| Import batch size cap | Max imports per batch | CH30 |
| Pro-only overwrite rules granularity | Action C availability | CH08 |
| Canonical matching heuristics thresholds | Fuzzy match thresholds | CH10 |

---

## Cross-References Summary

| From | To | Relationship |
|------|-----|--------------|
| CH17 | CH18 | Import/save actions route to Inbox |
| CH17 | CH19 | Conflict resolution for imports |
| CH17 | CH07 | Guest cannot create links |
| CH17 | CH08 | Entitlement caps for link creation |
| CH17 | CH16 | Share entry point on Flow Detail |
| CH17 | CH28 | Deep-link transport |
| CH17 | CH29 | Uploaded media not shareable |
| CH17 | CH30 | Abuse limits, warning ladder |
| CH18 | CH07 | Account required to save |
| CH18 | CH08 | Inbox cap, saved flow cap, practice gating |
| CH18 | CH17 | Link token fetch |
| CH18 | CH19 | Conflict resolution before Add to Library |
| CH18 | CH29 | Snapshot payload limits |
| CH19 | CH10 | Canonical IDs, aliases, families |
| CH19 | CH11 | Revert model for overwrites |

---

## Open Questions (Unresolved Across Batch)

1. **Deep-link fallback behavior** when app not installed (CH17/CH18) — exact web flow TBD
2. **Pro inbox cap** (CH18-P1) — 50/200/Unlimited decision pending
3. **Link expiry support** (CH17) — Off by default; decision for beta
4. **Overwrite (Action C) availability** by plan state (CH19) — likely Pro only per CH08
5. **Canonical matching heuristics** thresholds (CH19) — exact fuzzy match parameters TBD in CH10

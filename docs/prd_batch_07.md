# PRD Batch 07 — CH23-CH26 (Progress & Monetization)

**Generated:** 2026-01-05
**Chapters:** CH23, CH24, CH25, CH26
**Status:** Extracted

---

## CH23 — Mastery & Gameplans

### Overview
Defines how Handz models "mastery" and "gameplans" for strike decision training: goal types, planning logic, user trust controls, and mastery integration with Practice Mode logging. Core value proposition: convert flows into actionable training plans that help users learn responses, maintain them over time, and see concrete progress.

### Dependencies
- CH03, CH08, CH20, CH21, CH22, CH24, CH26, CH27, CH30

### Related
- CH04, CH05, CH15, CH16, CH17-19, CH25, CH31, CH33

### Definitions (CH23-specific)

#### Path
- Specific ordered route through a flow: Move A → Sequence (optional) → Move B → ... until chosen end node
- Can cross branch nodes; may terminate at leaf or intentionally stop mid-tree
- Atomic unit of mastery and maintenance (users practice paths)

#### Gameplan
- Named bundle of one or more Paths selected by user
- May include paths from multiple flows
- Designed to match user intent: upcoming opponent, curriculum block, or personal "set of reactions"

**Composition rules:**
- Can include: (a) multiple individual paths, (b) entire flow, (c) saved filter-set that expands into paths
- When user selects entire flow, Handz must automatically enumerate paths in background and present as editable items
- Users can rename any Gameplan at any time
- Gameplan can be edited later: add/remove paths, reorder drill priority, change goal type

#### Mastery
- App-level model of how "locked in" a path is for user
- Based on logged practice and time since last reinforcement
- Explicitly NOT a guarantee of fight performance
- A planning and memory-maintenance proxy

**Constraints:**
- Low friction: cannot require constant rating while gloved or mid-round
- Trust: users must be able to correct/override mastery without losing library

#### Mastery States (Path-Level)
Each Path has a MasteryState (exact numerical thresholds are placeholders):
- **Not Started** — never practiced in Practice Mode (or no qualifying logs)
- **In Progress** — actively training; plan has remaining sessions before target readiness
- **On Track** — meeting recommended cadence; forgetting risk is low
- **Needs Review** — drifting; forgetting risk rising; maintenance recommended
- **Maintained** — completed initial plan and maintained at defined cadence
- **Stale** — not reinforced for long period; treat as partially forgotten
- **User-Adjusted** — flag indicating user manually adjusted mastery up/down

### Key Requirements

#### Goal Types (V1)
| Goal Type | Description |
|-----------|-------------|
| Remember (Technique Recall) | Accurate recall of sequence and decision path |
| React Faster (Decision Speed) | Fast selection of correct response; higher volume/cadence |
| Fight Camp (Readiness by date) | User sets target date; plan schedules to reach readiness |
| Coach Curriculum (Teach & retain) | Similar to Recall; optimized for assigning to students; includes share/export hooks (CH17-19) |
| Just Drill (Simple) | Minimal setup; mastery tracking uses default intensity |

*Note: Goal types are user-facing marketing language; exact in-app copy belongs to CH15/CH26. CH23 owns behavior differences.*

#### Intensity Profiles
Each Goal Type maps to an Intensity Profile defining defaults for: sessions per week, sets per path, target repetitions/time, maintenance cadence.

- **Light** — hobbyists or low frequency training
- **Standard** — default for most users
- **Aggressive** — fighters in camp or users seeking faster automation
- **Custom** — user can override defaults (within safety constraints; see CH30)

**Intensity override rules:**
- User can always decrease intensity without warnings
- Increasing above defaults may trigger soft warnings + one-tap confirmation (CH30)
- Overrides remembered at Gameplan level; may be overridden per-path (advanced)

#### Mastery Hub (Entry Surface)
Primary purpose: show concrete progress and next actions without overwhelming.

**Required modules:**
- **Top summary strip**: Counts (Gameplans Active, Paths On Track, Paths Needing Review, Paths Stale); streak/cadence indicators secondary
- **Next Up card**: Single most important recommended action; Buttons: [Start Practice], [View Gameplan], [Snooze]
- **Due for Review list**: Sortable by path or grouped by gameplan; shows Path name, Source flow name, Status badge, Last practiced date, "Why" tooltip
- **Active Gameplans list**: Cards with name, goal type, percent progress, quick actions [Practice], [Edit], [View]

#### Gameplans List
- Must support search and basic sorting
- V1: allow renaming and deletion
- Deletion must have clear choice: remove gameplan only vs remove gameplan and associated custom path configs (never delete flows)
- Actions: [+ New Gameplan], per-item [Practice], [View], [Edit], overflow (Rename, Duplicate, Delete)
- Empty state: explain what gameplan is + CTA to create (copy owned by CH15)

#### Gameplan Builder (Wizard)

**Step A — Choose scope:**
- Option 1: Select whole flow (auto-breakdown into paths)
- Option 2: Select specific paths from one or more flows
- Option 3: Select paths across different flows (advanced) — allowed

**Step B — Name + goal type:**
- Name field (required, editable later)
- Goal Type picker
- If Fight Camp: ask for target date (calendar)

**Step C — Intensity & constraints:**
- Select intensity profile: Light / Standard / Aggressive / Custom
- Custom allows: sessions per week, sets per session, time per set, rest defaults
- "Keep it simple" vs "Tune details" branching

**Step D — Review & create plan:**
- Preview first 7 days (or first 3 sessions)
- Allow toggling path priority order (drag reorder)
- CTA: [Create Gameplan], [Back], [Save Draft]

#### Planning Logic

**Planning Inputs:**
- Selected paths (each has difficulty proxy and length)
- Goal type and intensity profile
- User training frequency preference (self-reported)
- User available days/times (optional; full scheduling in CH24/CH27)
- Existing practice history for same paths (CH22)
- User caps/entitlements (CH08) that may limit practice sessions

**Path Difficulty Proxy (V1):**
- Path length: number of move nodes (and sequence nodes)
- Branch complexity: number of decision points
- Move types included (optional; placeholder until move taxonomy finalized CH09/CH10)
- User-tagged difficulty override (optional advanced): Easy/Medium/Hard

**Recommended Plan Output:**
- **Initial Mastery Phase**: Finite set of planned sessions; target completion condition per path (placeholder thresholds); projected completion date range
- **Maintenance Phase**: Recurring review cadence after path reaches Maintained; cadence adapts based on recency; cap weekly load (handoff to CH24)

**Converting Plan to Practice Setup:**
- Each planned session representable as pre-filled Practice Setup (CH20)
- Preload requirements: selected paths prechecked, order pre-applied (user can reorder unless "Strict camp" mode enabled; placeholder), timer settings and assumed reps prefilled, session labeled with origin metadata for logging (CH22)

#### Trust Controls & Manual Adjustments

**Adjust Mastery (Path-level):**
- Available from Path Detail and Gameplan Detail (per path)
- Options: Mark as Not Started / In Progress / Maintained / Needs Review / Stale
- Reason capture: optional quick-select ("Haven't drilled in real sparring", "Injured", "Too easy", "App overestimated"); one-tap and skippable
- User-Adjusted flag shown with subtle indicator

**Guardrails:**
- Never block user from adjusting (no hard restriction)
- If user increases mastery: show note "Handz will use this to reduce reminders."
- If user downgrades: surface helpful action "Want a quick review session now?"

**Revert Model (Granularity):**
- **Scope A (Single path)**: Revert mastery state to system-estimated; revert custom difficulty tag; revert custom per-path plan config
- **Scope B (Single gameplan)**: Revert all user adjustments within gameplan (paths remain selected); recompute schedule
- **Scope C (Global)**: Reset all mastery estimates (keeps logs); behind confirmation in Settings

#### Progress Metrics

**Path Detail metrics:**
- Mastery state badge + explanation
- Sessions completed toward target (placeholder until thresholds defined)
- Last practiced timestamp + "days since"
- Next recommended review (date or "due now")
- Total completed sets and assumed reps (from CH22)
- Show which gameplans include this path

**Gameplan-level metrics:**
- Paths: total, on track, needs review, stale
- Projected readiness (if Fight Camp)
- Consistency indicator: sessions completed this week (secondary)
- Maintenance load indicator with overload warning if above cap (CH24)

**No gloves-on rating requirement:**
- Must NOT require users to rate performance mid-set
- Use logged completion signals from Practice Mode: set completed, interruptions, early-end actions (CH21)
- Optional post-session reflection allowed but must be skippable and low effort (CH22)

#### Entitlements & Funnel (CH00 Global Locks)

**Known global locks to respect:**
- Practice is paywalled; Free receives 3 monthly practice credits usable only on saved flows
- Free saved flows cap: 2; Free inbox cap: 10 items
- Free can view inbox but cannot practice inbox items
- Share links are unlisted; imports should not be blocked in way that kills sharing funnel

**Mastery visibility vs Mastery action (suggested default, subject to CH08/CH25):**
- Free can: create Gameplans, add paths, view due-for-review list, view limited plan preview (first session)
- Free cannot: start planned sessions unless spending credit and session uses saved flows only
- Pro can: generate full plan schedules, start unlimited practice, use maintenance notifications

**Imported content contradiction avoidance:**
- If Free receives flow via link/import: appears in Inbox (cap 10)
- Can view but Practice CTA disabled: "Save to your library to practice" + upsell if at saved-flow cap
- To practice: must move into Library (counts toward 2 saved flows cap)
- If at cap: must delete existing saved flow or upgrade (CH25)

#### Data Model

**Gameplan:**
- id (UUID), owner_user_id, name, goal_type (enum), intensity_profile (enum), target_date (nullable), created_at, updated_at, status (active/paused/archived), notes (optional), default_session_template_id

**GameplanItem (Path binding):**
- id (UUID), gameplan_id, source_flow_id, path_id (stable path signature), priority_order (integer), difficulty_override (nullable), config_override (nullable), created_at, updated_at

**PathSignature (stable path identity):**
- path_node_ids: [nodeId1, nodeId2, ...]
- branch_conditions: ["if leans back", ...] (optional)
- version_tag: increments when source flow changes shape

**MasteryRecord:**
- id (UUID), owner_user_id, path_signature, mastery_state (enum), user_adjusted_flag (bool), system_estimated_state (enum), last_practiced_at, sessions_completed_count, sets_completed_count, assumed_reps_total, next_review_due_at (computed), maintenance_cadence_profile (computed/selected), created_at, updated_at

#### Edge Cases & Conflict Handling

**Orphaned paths after flow edits:**
- If flow edit removes nodes referenced by Gameplan path: path becomes orphaned
- Label clearly in Gameplan Detail: "Needs update"
- Actions: [Fix Path], [Remove from Gameplan], [Keep as reference] (view-only)
- MasteryRecord frozen; no maintenance reminders until resolved

**Duplicated flows and path identity:**
- Duplicated nodes receive new IDs; paths in duplicate are new identities
- Offer: "Copy mastery progress into duplicate?" (optional; placeholder)
- Default: do not copy mastery automatically

**Multi-gameplan inclusion:**
- Path can belong to multiple gameplans
- Mastery is path-level (global) unless explicitly overridden
- If two gameplans have different intensity configs: use higher intensity when practicing from that gameplan; mastery record aggregates all logs
- UI must show when path influenced by multiple gameplans

#### Maintenance & Notifications Hooks

**Signals emitted by Mastery:**
- DueReview(path_id, due_at, severity)
- OverloadRisk(weekly_reviews_count, recommended_cap)
- StaleRisk(path_id, days_since_last_practice)
- GameplanReadiness(gameplan_id, projected_ready_at, behind_schedule_flag)

**Snooze behavior:**
- Always optional and never shaming
- Options: Later today / Tomorrow / This weekend / Custom (if CH24 allows)
- Snoozing updates due date but does not change mastery state until practiced

### Placeholders (CH23)
| ID | Description | Options | Default | Decide-by |
|----|-------------|---------|---------|-----------|
| - | Exact mastery thresholds | TBD | TBD | before implementation |
| - | Exact spaced-repetition schedule parameters | TBD | TBD | before implementation |
| - | Science citations & wording | owned by CH26 | - | CH26 |
| - | Mastery planning Pro-only vs Free-limited | owned by CH08/CH25 | - | CH08/CH25 |
| - | "Strict camp" mode | enabled / disabled | placeholder | before implementation |
| - | Copy mastery progress into duplicate | allow / disallow | do not copy | post-MVP |

---

## CH24 — Maintenance System

### Overview
Defines the Maintenance System that helps users retain mastered paths/gameplans over time without overwhelm. Includes UI, scheduling, notifications hooks, overload prevention, data model. Maintenance is NOT a separate practice engine; it orchestrates sessions using existing Practice Mode pipeline with additional metadata.

### Dependencies
- CH03, CH08, CH20, CH21, CH22, CH23, CH27, CH28, CH31

### Related
- CH06, CH15, CH26, CH29, CH33, CH36, CH37

### Definitions (CH24-specific)
- **Maintenance Item**: Maintainable unit derived from path or gameplan subset, scheduled for periodic drill
- **Due**: Maintenance item recommended to be drilled within a time window
- **Backlog**: Overdue maintenance items not yet drilled
- **Capacity**: User-selected daily/weekly limit that prevents overload
- **Snooze**: User action to push maintenance item to later without losing progress framing

### Key Requirements

#### Product Intent
- Help users prioritize what matters (most used, most important, soonest needed)
- Prevent maintenance overload via explicit capacity settings and soft warnings
- Keep motivation high by avoiding punitive streak language; emphasize "resume" not "restart"
- Support both simplicity (one tap "Do today's maintenance") and complexity (custom scheduling per gameplan)

#### Core UX Model
- Users select targets (paths/flows/gameplans) to maintain
- Handz generates schedule based on user capacity and adjustable maintenance cadence model
- Each maintenance item launches maintenance session (Practice Mode variant) consuming same entitlements/credits as practice (CH08; CH00 Decision Log)
- Completion updates maintenance record and refreshes "next due" predictions

#### Screen Inventory and Routing

**Maintenance Hub (/maintenance):**
- Primary entry point
- Shows today's maintenance, backlog, recommended items with fast actions
- Entry points: Practice tab sub-tab/CTA, Session Complete screen (CH22), Gameplan detail (CH23), Home/Dashboard card

**Maintenance Plan Builder (/maintenance/setup):**
- Wizard-style flow for first-time setup
- Creating/editing maintenance plan for gameplan

**Maintenance Item Detail (/maintenance/item/:id):**
- Drill preview + controls: start session, snooze, edit cadence, swap target, view history

**Maintenance Session (/maintenance/session):**
- Practice Mode session in "Maintenance" context
- Same core UI as Practice Active (CH21) with maintenance-specific header context and logging metadata

**Maintenance Settings (/maintenance/settings):**
- Global settings: capacity, preferred days, reminder windows, default cadence model, overload behavior

#### Maintenance Hub Detailed Spec

**Layout (mobile portrait):**

*Top area:*
- Header: "Maintenance" + subtext (date + status e.g., "2 due today • 1 overdue")
- Primary CTA: "Start Today's Maintenance" (bundled session of due items) OR "Browse Recommendations" if nothing due
- Status pill: "On Track" / "Behind" / "Paused"

*Main sections (scroll):*
- Due Today list (0–N items)
- Overdue list (collapsed by default if empty)
- Recommended list (always present; personalization-driven)
- Browse entry (search + filters + sort)

**Maintenance item card:**
- Title: Gameplan name or Path name (user-renamable)
- Subtitle: path count + tags (optional)
- Next due: "Due today" / "Due tomorrow" / "Overdue 3d"
- Quick action: "Start" button
- Secondary action: kebab menu (Snooze, Edit cadence, Remove from maintenance, View history)

**Browse panel:**
- Search: by flow/gameplan/path name
- Filters: Most used, Most recently practiced, Most recently added, Overdue, Due soon, By style tag, By folder
- Sort: Due date, Urgency score, Recently practiced, Alphabetical
- Bulk select: Add to maintenance, Remove, Snooze, Set cadence (bulk), Move to different gameplan

#### Maintenance Plan Builder (Wizard)

**When shown:**
- First time user taps Maintenance Hub with no maintenance targets configured
- User taps "Create Maintenance Plan" from Gameplan detail
- User taps "Edit Maintenance" from Maintenance Hub settings
- User imports flow/gameplan and chooses "Add to Maintenance" (CH19)

**Wizard Steps:**

**Step 1 — Choose what to maintain:**
- Pick one: Gameplan (recommended), Whole Flow, or Specific Paths
- Show simplest choice first; advanced behind "More options"
- If Gameplan: pick existing or create new (name required)
- If Flow: system proposes derived paths (owned by CH23)
- If Specific Paths: show path picker with preview

**Step 2 — Choose goal type (affects cadence):**
- "Remember it" (light maintenance)
- "Use it under pressure" (heavier maintenance)
- "Fight camp" (time-bounded; optional future placeholder)
- Goal type changes suggested cadence but users can override

**Step 3 — Set your capacity (overload prevention):**
- User sets: maintenance days per week, target sessions per week OR target minutes per week
- User sets optional daily cap: "No more than X items/day"
- Default: conservative and editable later (placeholder numbers)

**Step 4 — Reminders (opt-in):**
- Preferred reminder time window: "Morning", "Afternoon", "Evening" (exact scheduling owned by CH27)
- Allow "No reminders" option without guilt copy
- If notifications permission not granted: soft explanation + "Enable reminders" button

**Step 5 — Review and create:**
- Summary: items selected, estimated weekly load, schedule preview
- Buttons: "Create Plan" and "Back"
- Include note: "You can change this anytime."

#### Scheduling and Overload Prevention

**Design principles:**
- User-controlled capacity beats algorithmic ambition
- Backlog must not spiral into shame; graceful degradation when user misses days
- Prefer fewer items done consistently over many items done once
- All "recommended" scheduling is editable; never imply medical/scientific certainty (CH26)

**Capacity model (V1):**
- Weekly capacity: user-set target number of items per week OR minutes per week
- Daily cap: optional hard cap for UI
- Carryover policy: defines how missed items roll forward

**Carryover policy (V1 recommended: Soft Carryover with Backlog Compression):**
- Soft carryover: missed items become overdue but not automatically stacked onto next day beyond daily cap
- Backlog compression: system proposes small "catch-up" suggestion; never auto-increases load unless user taps "Catch up"
- Explicit skip: user can mark overdue item as "Skip for now" without penalty; re-enters recommendations later

**Urgency scoring (for sorting, not shaming):**
- Inputs: last practiced date, goal type, user importance rating (optional), recent usage frequency, upcoming "needs" date (future placeholder)
- Output buckets for UI: "Due", "Soon", "Later"

**Overload prevention UI:**
- If too many items vs capacity: soft warning "This is a lot. Want to start with fewer?" + "Keep" / "Reduce"
- If daily scheduled items exceed cap: highlight day + offer "Spread across week"
- If backlog > threshold: supportive banner "You're behind - pick 1 item today and you're back on track."

#### Maintenance Sessions and Entitlements

**Launching maintenance session:**
```
Context = maintenance; source = maintenance_item_id; target_set = derived paths;
mode = user-selected drill mode (Practice vs Review)
```

**Entitlement rules (CH08):**
- Guest: can browse maintenance screens as demo; creating plan or starting session prompts account creation (CH07/CH08)
- Free: starting session consumes practice credit (if available); allowed only for saved flows (not inbox); if no credits, show paywall
- Pro/Trial: unlimited maintenance sessions; enforce anti-abuse/safety limits (CH30)

**Maintenance vs Practice UI differences:**
- Header label: "Maintenance"
- Session goal summary: "Today: 2 items" / "This item: 1 path"
- Post-session summary includes: items maintained, next due estimate, backlog delta

#### Status Framing (No-Shame Design)

**Status pills:**
- **On Track**: completed at least one due item within last X days OR backlog below threshold
- **Behind**: backlog beyond threshold OR no maintenance completed within window
- **Paused**: plan paused by user; no reminders; hub shows "Resume" CTA
- **Not Set Up**: no maintenance plan; hub shows setup CTA

**Copy rules:**
- Never say: "You failed", "You broke your streak"
- Prefer: "Pick up where you left off", "Resume", "You're one session away from being back on track"
- Use neutral metrics: "items completed" and "items due"; streaks optional and secondary (CH22)

#### Data Model (V1)

**MaintenancePlan:**
```json
{
  "id": "string",
  "user_id": "string",
  "name": "string",
  "scope": "global | gameplan",
  "gameplan_id": "string | null",
  "goal_type": "remember | pressure | custom",
  "is_paused": "boolean",
  "created_at": "ISODate",
  "updated_at": "ISODate"
}
```

**MaintenanceItem:**
```json
{
  "id": "string",
  "plan_id": "string",
  "label": "string",
  "target_type": "path | path_set | flow | gameplan",
  "target_ref": { },
  "importance": "low | med | high | null",
  "cadence_model": "auto | fixed | custom",
  "cadence_days": "number | null",
  "next_due_at": "ISODateTime",
  "last_done_at": "ISODateTime | null",
  "created_at": "ISODateTime",
  "updated_at": "ISODateTime",
  "is_archived": "boolean"
}
```

**MaintenanceSchedule:**
```json
{
  "id": "string",
  "user_id": "string",
  "preferred_days": ["mon","wed","fri"],
  "time_window": "morning | afternoon | evening | null",
  "weekly_capacity_type": "items | minutes",
  "weekly_capacity_value": "number",
  "daily_cap_items": "number | null",
  "carryover_policy": "soft_compress",
  "reminders_enabled": "boolean",
  "updated_at": "ISODateTime"
}
```

**MaintenanceLog:**
```json
{
  "id": "string",
  "user_id": "string",
  "session_id": "string",
  "completed_at": "ISODateTime",
  "items": [{
    "maintenance_item_id": "string",
    "status": "done | partial | skipped",
    "notes": "string | null"
  }],
  "backlog_before": "number",
  "backlog_after": "number"
}
```

#### Error States and Offline Behavior

- **Offline browse**: show cached maintenance items; hide "Start" if practice cannot start offline (CH28 rules)
- **Sync conflict**: if target flow/path changed since last schedule calculation, show "Needs update" badge; tapping offers "Recompute"
- **Missing target**: if flow/path deleted, maintenance item becomes "Broken"; offer: Remove from maintenance or Replace target
- **Timezone shift**: rebase due dates to local midnight boundaries to avoid instant overdue spikes
- **Notification permission denied**: non-blocking banner "Reminders are off" with "Enable" action

### Placeholders (CH24)
| ID | Description | Options | Default | Decide-by |
|----|-------------|---------|---------|-----------|
| MAINT_CAPACITY_DEFAULT | Default maintenance capacity | 3 items/week, 6 items/week, minutes-based | 3 items/week | before implementation |
| OVERDUE_THRESHOLD | Backlog threshold for "Behind" status | 3 / 7 / 14 items | 7 items | before release QA |
| CADENCE_PRESETS | Cadence options | 3/7/14 days, 2/5/10 days, user-defined only | 3/7/14 days | before UI copy lock |
| GLOBAL_VS_PER_GAMEPLAN_PLANS | Plan scope options | global only in V1, allow per-gameplan overrides, multiple plans | allow per-gameplan | implementation |
| PRO_GATING_MAINTENANCE_AUTOPLAN | Pro gating for autoplan/reminders | autoplan for all, autoplan Pro-only, reminders Pro-only | autoplan for all | pricing review |

---

## CH25 — Monetization: Pricing, Trial, Paywalls, Upsells

### Overview
Defines the monetization model for Handz V1: what is free vs paid, where and how paywalls appear, exact UX/copy for upgrades. Written to minimize user confusion while keeping share/import funnel healthy.

### Dependencies
- CH05, CH07, CH08, CH17, CH18, CH20-CH22, CH29, CH30, CH33

### Related
- CH01, CH02, CH26, CH31, CH36, CH37

### Non-Goals
- Store screenshots, full App Store metadata, marketing channels (owned elsewhere)
- Does not re-define core Practice behavior (references CH20-CH22)

### Key Requirements

#### Plan Model and Entitlements

**Guest (no account):**
- Can browse default move library (CH09) and view sample flows (CH05/CH09)
- Can build flows as sandbox draft but cannot save (no local, no cloud)
- Can open unlisted share links view-only (CH17) and view imported flows in Inbox (CH18)
- Cannot practice; cannot "Save to Library"
- Hard conversion prompts at key moments

**Free (account created):**
- Create and save flows up to Free cap (locked: 2 saved flows)
- Receive imports in Inbox up to Free inbox cap (locked: 10)
- Can view inbox items but cannot practice inbox items
- Practice is paywalled; Free receives 3 Practice Credits per month usable only on saved flows
- Cannot upload videos; links allowed where applicable (Uploads Pro-only; cap 2GB)

**Trial (7-day free trial):**
- Full Pro access for duration
- Starts only via explicit user action on upgrade surface; never auto-start silently
- Cancellable in Settings; follows Apple subscription standards

**Pro (subscription):**
- Full access to Practice Mode (no credit consumption)
- Higher/removed caps vs Free (share-link creation, inbox storage, etc.)
- Uploads enabled (Pro-only) with 2GB per-account cap
- Uploaded videos private-only; not included in share links

**Global locks referenced:**
- Pricing direction: $9.99/mo with 7-day trial
- Free saved flows cap: 2
- Free inbox cap: 10
- Practice paywalled with 3 monthly credits on saved flows
- Uploads Pro-only with 2GB cap

#### Pricing and Trial Rules

**Locked (V1):** $9.99/month, auto-renewing subscription, 7-day free trial for new subscribers

**UI rules:**
- All pricing UI must use localized StoreKit product pricing (currency + region)
- Never hardcode '$9.99' into UI strings; show "{localizedPrice}/month"

**Trial behavior:**
- Trial begins immediately after successful Apple subscription purchase confirmation (StoreKit)
- If cancelled during trial: Pro entitlements remain active until trial end; then downgrade to Free automatically
- If not eligible for trial (previous trial/subscription): show "Start Pro" (no "Free trial" language)
- Always show disclosure: "Cancel anytime in Settings."
- Renewal disclosure under CTA: "Free for 7 days, then {localizedPrice}/month. Auto-renews until canceled."

**Restore purchases:**
- Settings includes "Restore Purchases" action
- On app launch and returning to foreground: refresh entitlements from StoreKit (with caching)
- If entitlement status unknown (network issue): default to Free entitlements + non-blocking banner "Checking subscription status..." + background refresh (CH31)
- If refresh confirms Pro: entitlements update immediately + subtle toast (no modal)

#### Paywall Surfaces, Triggers, and UX

**Monetized surfaces (authoritative list):**

| Surface | Trigger | Behavior |
|---------|---------|----------|
| Practice Paywall (Primary) | User taps "Start Practice" on saved flow OR Practice tab | Free: allow if has Practice Credit OR running Practice Demo; Guest: cannot practice, show upgrade + account creation |
| Flow Save / Cap Paywall (Secondary) | Free attempts 3rd saved flow OR save inbox import at 2-flow cap | Show paywall with explanation and safe fallback |
| Video Upload Paywall (Tertiary) | User taps "Add Video" and is not Pro | Show paywall; allow link instead; explain shareability difference |
| Share Link Creation Soft Gate | User hits daily link creation cap (CH17/CH30) | Rate-limit message, not paywall (unless strategy changes) |

**Paywall presentation pattern:**
- One consistent paywall component (modal sheet or full-screen)
- Same component with different contexts (Practice, Save Cap, Video Upload)
- Header: context-specific headline
- Subheader: 1-sentence value statement
- 3-5 bullet benefits (context-specific)
- Primary CTA: "Start 7-Day Free Trial" OR "Start Pro"
- Secondary: "Not now", "Restore Purchases", "See terms"
- Disclosure: renewal and cancellation line

**Eligibility-aware CTA rules:**
- If eligible for trial: "Start 7-Day Free Trial"
- If not eligible: "Start Pro"
- Never show multiple confusing CTAs

#### Practice Demo (Value Preview)
- Available to Guest and Free users from onboarding and Practice tab empty-state
- Uses predefined Handz-authored "Sample Gameplan" pack (CH20/CH05) with small set of paths
- Demo sessions do not consume credits
- Logged as "Demo"; do not count toward mastery/maintenance progress
- May count toward "first session complete" onboarding milestone
- Demo content must not be editable in V1 (no 'Save as my flow')

#### Practice Credits (Free)
- 3 Practice Credits per month
- Refresh monthly on user's local calendar date (local time zone)
- Usable only on saved flows (not Inbox items)
- Each Practice session consumes exactly 1 credit when session is started (not when completed)
- If force-close or interrupted: session saved as "Interrupted" (CH21/CH22); credit remains spent (prevents abuse)
- If user backs out before countdown begins: do not spend credit

#### Conversion Prompts (Guest -> Account -> Pro)

**Before building a flow (entry):**
- If Guest enters Flow Builder: one-time banner "Guest mode: flows won't be saved. Create an account to save your work." + "Create account" / "Continue as guest"
- If continues as guest: do not re-show for that session

**When tapping "Save":**
- Hard gate: modal "Create an account to save this flow." + auth options + "Not now"

**When attempting Practice:**
- Hard gate: Guest cannot practice
- Show Paywall that includes account creation step, or combined "Create account + Start trial" flow

#### Cap Handling (Free saved flows cap = 2)
- If Free at cap taps "Create Flow": allow building in temporary draft state + warn "You're at your Free limit (2). Upgrade to save more."
- When attempting save: show Save Cap Paywall (context = 'Save more flows')
- If Free receives import at cap: Inbox item remains view-only; "Save to Library" shows paywall
- Also offer: "Replace an existing saved flow" (optional) - must be explicit and reversible (CH11/CH34)
- Never delete user content automatically; always require confirmation for replacement

#### Upsell Copy Library

**Tone:** minimal, modern, confident. Avoid cringe fight-talk. Feel like calm training tool.

**Practice Paywall Copy (Primary):**
- Recommended V1 headline: "Turn your flows into reactions."
- Subheader: "Practice mode guides reps, timing, and tracking so your decisions get automatic."
- Benefits: Choose paths to drill, Run timed sets with rest and logging, Track what you practiced, Unlock mastery + maintenance plans
- Primary CTA: "Start 7-Day Free Trial"
- Disclosure: "Free for 7 days, then {localizedPrice}/month. Cancel anytime in Settings."

**Save Cap Paywall Copy:**
- Headline: "Save unlimited flows."
- Subheader: "Free accounts can save 2 flows. Pro removes the cap and unlocks Practice."
- Benefits: Keep every gameplan, Duplicate and evolve flows, Practice any saved flow without credits

**Video Upload Paywall Copy:**
- Headline: "Add private video clips."
- Subheader: "Upload short clips to your moves and sequences to capture technique details."
- Benefits: Save videos inside Handz (up to 2GB), Keep uploads private, Share flows via links — links share plan even if videos stay private

**Segment-specific microcopy:**
- Fighters: "Map your opponent's habits. Drill the counters." / "Build a fight-night gameplan and rehearse branches."
- Coaches: "Turn combinations into shareable plans for your athletes." / "Standardize drilling without losing flexibility."
- Students/hobbyists: "Never forget what you learned in class." / "Build a personal library of setups you actually use."

#### Paywall-Adjacent Education
- Onboarding: 1-2 cards explaining Flow -> Practice -> Mastery
- Practice tab empty-state: "Try Demo" and "Why Practice?" link
- Paywall: one short line; deeper explanation on separate info screen (CH26)
- Settings: "How Pro works" page with plan summary and caps
- Scientific claims owned by CH26; paywall should link to CH26 Education page "Why this works"

**Shareability and funnel health rules:**
- Accepting imports should not be paywalled
- Free can view inbox items and save within 2-flow cap (or hit paywall over cap)
- Practice is paywalled; Free cannot practice inbox items
- If coach shares flow to Free user: Free can view and save (if under cap); to drill, must spend credit or upgrade

**Links vs uploads disclosure:**
- On "Add Video" screen: "Uploads are private to your account. If you want others to see the clip, paste a video link instead."
- On Share screen (if flow has uploads): "This share link will not include your private uploads." + "Learn more"
- On Import screen (if incoming references private uploads): omit them; show "Some private uploads were not included."

#### Screen and Route Specifications

**Routes:**
- MODAL.Paywall(context): context ∈ {Practice, SaveCap, Uploads, Generic}; params: sourceScreen, eligibility, featureName, ctaOverride
- SCREEN.ProBenefits: Static page describing Pro benefits + caps + link to CH26
- SCREEN.ManageSubscription: Plan status, renewal date, restore purchases, manage in Apple Settings
- MODAL.CreditConfirm: Confirms 1 credit will be used; shows remaining

**MODAL.Paywall(context) spec:**
- Header region: Title (context headline), Close (X) button
- Body region: Subheader, Benefits list (3-5), Optional "Why this works" link -> SCREEN.ProBenefits
- CTA region: Primary CTA button, Secondary "Not now", Small links for Restore/Terms/Privacy
- Disclosure region: Localized renewal line, "Cancel anytime in Settings."

**MODAL.CreditConfirm spec:**
- Title: "Use 1 Practice Credit?"
- Body: "You have {creditsRemaining} credits left this month. Credits only work on saved flows."
- Primary: "Start Practice"
- Secondary: "Not now"
- Link: "Upgrade to Pro" -> MODAL.Paywall(Practice)

**SCREEN.ManageSubscription spec:**
- Plan status card: currentPlanLabel, renewal date if Trial/Pro, caps summary if Free
- Actions: "Manage in Apple Subscriptions", "Restore Purchases", "How Pro works" link, Cancel instruction for Pro/Trial

#### Entitlement Transitions and Edge Cases

**Upgrade flow (Free/Guest -> Trial/Pro):**
- User triggers paywall, taps primary CTA
- If StoreKit succeeds: immediately unlock Pro, dismiss paywall, show toast "Pro unlocked"
- If came from blocked action: automatically proceed after unlock
- If fails/cancelled: stay on paywall, show non-blocking error (CH31), allow retry

**Downgrade flow (Pro -> Free):**
- On entitlement refresh if Pro expired: immediately revert to Free caps/gates
- If exceeds Free caps (>2 saved flows): do NOT delete; mark extra flows as "Locked (Pro)" view-only until upgrade or delete
- If mid-session when downgrade: allow session to finish; mark future practice as gated

**Offline behavior:**
- Pro offline: allow Pro entitlements for grace window (e.g., 24 hours) using last-known timestamp; after grace, treat as Free until refresh
- Free offline: credit/cap checks use locally cached values; resolve conservatively after sync
- Paywalls should not block app from opening; only block monetized action

#### Abuse Prevention (Monetization-Adjacent)
- **Share link creation**: daily limits enforced (CH17/CH30); rate-limit message when hit
- **Inbox import spam**: Free inbox cap 10; overflow queued or rejected (CH18/CH30); Free cannot practice inbox items
- **Credit abuse**: credits spend on session start; interruptions do not refund; credits tied to account; server-side reset (CH28/CH29)

#### Analytics Hooks (Monetization)
- paywall_viewed {context, sourceScreen, planState, trialEligible}
- paywall_cta_tapped {context, trialEligible}
- purchase_success {productId, trialUsed}
- purchase_failed {reason}
- credit_confirm_viewed {creditsRemaining}
- credit_spent {flowId, pathsCount}
- upgrade_completed_then_auto_resumed_action {actionType}

### Placeholders (CH25)
| ID | Description | Options | Default | Decide-by |
|----|-------------|---------|---------|-----------|
| Annual Plan | Annual pricing option | none in V1, add annual with % discount, add annual + monthly | none in V1 | after initial retention data (CH33) |
| Offline Grace Window | Pro offline grace period | 12h / 24h / 48h | 24h | CH28/CH08 |
| Intro Offer | Offer beyond trial | TBD | TBD | post-MVP |
| Pro share-link daily cap | Share link cap for Pro | TBD | TBD | CH17 |
| Practice Demo content pack | Exact demo content | TBD | TBD | CH20/CH05 |
| Paywall A/B variants | Creative variants | TBD | TBD | once analytics live |

---

## CH26 — Scientific Claims & Education Pages

### Overview
Defines the in-app scientific/education layer supporting Handz positioning ("map decisions, drill outcomes, build automatic responses") without over-claiming or creating trust risk. Includes page inventory, routing, full UI copy blocks, claim rules, disclaimers, and maintainable references system.

### Dependencies
- CH06, CH08, CH20-CH24, CH25, CH15

### Related
- CH01, CH03, CH05, CH09-CH11, CH27, CH31, CH33, CH34

### Key Requirements

#### Goals
- Make Handz understandable to first-time users via short, skimmable education pages and tooltips
- Provide science-backed framing (learning/memory principles) that is honest, non-absolute, "results may vary"
- Create internal structure so claims can be updated later without rebuilding app (remote-config + versioning)
- Support monetization (CH25) with credible reasons to upgrade without misleading
- Protect trust: users must disagree with estimates, adjust them, and see what app actually measures

#### Non-Goals (V1)
- No medical claims, rehabilitation claims, diagnosis language
- No promise of fight outcomes, injury prevention, "guaranteed" reaction-time gains, performance guarantees
- No custom biometric measurement, sensor-based validation, ML-based skill scoring
- No requirement that user reads long articles; content must be modular, skimmable, optional

#### Claim Policy and Safety Rules

**Claim categories (allowed):**
- **Educational statements**: general learning/memory principles (e.g., spaced repetition reduces forgetting); allowed if referenced and framed generally
- **Product-behavior statements**: what Handz does (e.g., "builds a maintenance plan"); must be literally true
- **Quantified performance claims**: any numbers ("2x faster", "30% better"); NOT allowed in V1 unless sources and wording locked; treat as placeholders

**Prohibited language (hard rules):**
- Avoid: "guarantee", "proven to", "will make you", "will prevent", "cure", "treat", "diagnose", "clinically", "medical", "rehab"
- Avoid: implying certification/endorsement by leagues/gyms/coaches unless true and documented
- Avoid: presenting estimates as facts; every estimate must be labeled as estimate and user-adjustable

**Required disclaimers (always-on):**
- **Results may vary**: learning speed and retention vary by person, schedule, experience, training quality
- **Not a substitute for coaching**: Handz supports planning and drilling; does not replace qualified instruction
- **Estimates, not measurements**: mastery/maintenance derived from user-selected plans and logged practice, not direct skill measurement
- **Safety reminder**: practice within your ability; stop if pain/injury; seek coaching/medical care as appropriate

#### Education Page Inventory

| Page ID | Title | Purpose | Entry Points |
|---------|-------|---------|--------------|
| EDU-01 | Why Handz | Core promise: map decisions, drill outcomes, build automatic responses | Onboarding, Help tab, Upsell context |
| EDU-02 | What Is a Flow | Defines flows/paths/branches with examples; mental model for first-time users | Onboarding, Flow Builder empty state, Help |
| EDU-03 | How Practice Builds Automaticity | Rehearsal, decision making, why pre-mapped responses reduce reaction delay | Practice tab intro, Paywall preface |
| EDU-04 | Spaced Repetition and Maintenance | Why skills decay, why brief refreshers help; introduces Maintenance | Maintenance screen header, Upsell |
| EDU-05 | Mastery Scores Explained | What mastery means in-app, what it does not mean, how to adjust | Gameplan/Mastery screens, Settings |
| EDU-06 | Scientific Claims & Disclaimers | Results-may-vary language, non-medical disclaimer, how we source references | Paywall, Settings, App Store compliance |
| EDU-07 | References | Readable citations list (per claim and principle) | Linked from EDU-06 and paywall |
| EDU-08 | Ethical Use | Avoid unsafe guidance; expectation: training tool not substitute; safety reminders | Onboarding, Settings |

**Route keys:**
- edu.why_handz, edu.what_is_a_flow, edu.automaticity, edu.spaced_repetition, edu.mastery_explained, edu.claims_and_disclaimer, edu.references, edu.ethical_use

#### Full Page Copy (Word-for-Word)

**EDU-01 — Why Handz:**
- Title: "Why Handz"
- Subtitle: "Map decisions. Drill outcomes. React without thinking."
- Benefits: Become a smarter striker, Remember every session, Train reactions at home
- How it works: Build a flow, Pick paths, Practice on timer and log
- CTAs: "Start a Flow", "Learn what a Flow is"
- Footnote: "Handz supports planning and practice. Results vary and depend on your training quality and consistency."

**EDU-02 — What Is a Flow:**
- Title: "What is a Flow?"
- Definition: "A flow is a map of decisions: 'If they do X, I do Y.'"
- Building blocks: Move, Sequence, Branch, Path, Merge
- Examples: Simple combo (Jab → Cross → Hook), Decision flow (Jab → if lean back → Head kick, etc.)
- Tip: "You can be simple or complex."
- CTAs: "Build my first flow", "How practice works"
- Footnote: "You can edit and reorder anything later. Nothing is 'locked in.'"

**EDU-03 — How Practice Builds Automaticity:**
- Title: "How practice builds automatic responses"
- Intro: "In sparring, you don't want to pause and 'think.' You want a prepared response."
- What Handz trains: Decision speed, Execution, Consistency
- Timed practice: "Handz uses timed sets so you can focus on movement, not your phone."
- About reps: "Handz logs 'assumed reps' based on your plan... the log reflects what happened."
- Myth vs Reality: Timer helps consistency; quality reps matter
- CTAs: "Try Practice (demo)", "Spaced repetition and maintenance"
- Footnote: "Handz does not measure technique quality. Ask a coach for feedback and train safely."

**EDU-04 — Spaced Repetition & Maintenance:**
- Title: "Spaced repetition (and why maintenance matters)"
- Intro: "Skills fade when you don't revisit them. Maintenance keeps your gameplan available under pressure."
- Plain English: "Instead of grinding the same thing once, you revisit it in short sessions over time."
- What Handz does: Pick paths you care about, builds schedule, reminds you when to refresh
- Overload avoidance: "You control what you maintain. Start with 1-3 paths."
- Callout: "Results vary"
- CTAs: "Build a Gameplan", "How Mastery works"
- Footnote: "References are available in the 'Sources' page."

**EDU-05 — Mastery Scores Explained:**
- Title: "Mastery in Handz (what it means)"
- One-liner: "Mastery is an estimate based on your planned drills and what you log — not a sensor reading."
- What Mastery tracks: paths selected, practice logged, maintenance refreshers
- What Mastery does NOT track: technique quality, fight performance guarantee, off-phone activity
- Adjusting Mastery: lower/raise goal, mark as "needs more work", reset without resetting entire library
- Callout: "Stay in control"
- CTAs: "View my Gameplans", "Science & disclaimers"
- Footnote: "Handz is a training aid. Train safely and use coaching feedback."

**EDU-06 — Scientific Claims & Disclaimers:**
- Title: "Science, sources, and disclaimers"
- Sections (accordion): Results may vary, What Handz measures, Evidence-informed principles, What we will never claim, Sources
- CTA: "View References"
- Footer: "If you notice a source issue, email support from Settings."

**EDU-07 — References:**
- Title: "References"
- Intro: "These references support Handz' educational statements. They do not guarantee your personal results."
- Grouped by claim with citations (placeholders)
- Footer: "Updated references may change over time. See version info below."
- Version label: "Sources version: v1.0"

**EDU-08 — Ethical Use:**
- Title: "Train smart. Train safe."
- Safety first: Warm up, stop if pain, ask a coach
- Training tool: "Handz helps you plan and rehearse decisions. It is not a replacement for coaching or medical advice."
- Respect partners: "Use gameplans to improve skill — not to harm training partners."
- CTAs: "Got it", "Start building"

#### References System

**Data model (SourcesPack JSON):**
```json
{
  "sources_version": "v1.0",
  "last_updated": "2026-01-02",
  "claims": [{
    "claim_id": "CLM-01",
    "claim_text": "Spaced repetition supports longer-term retention vs massed practice.",
    "risk_level": "low",
    "citations": [{
      "citation_id": "CIT-001",
      "authors": "TBD",
      "year": "TBD",
      "title": "TBD",
      "venue": "TBD",
      "link": "https://...",
      "note": "Supports CLM-01"
    }]
  }]
}
```

**Rendering rules:**
- Citations must be readable (authors, year, title)
- Links open externally
- If offline: show cached citations + "updated when online" label

**Versioning and trust:**
- Display Sources version and Last updated on EDU-07
- If sources change: do NOT silently change numeric claims
- When adding numeric claims: include "What changed" log and date

#### Claim Registry

| Claim ID | Claim | Type | Risk | Used In | Notes |
|----------|-------|------|------|---------|-------|
| CLM-01 | Spaced repetition supports longer-term retention vs massed practice | Educational statement (no numeric) | Low | EDU-04, Paywall | Needs citations |
| CLM-02 | Short, repeated review sessions can reduce forgetting over time | Educational statement | Low | EDU-04, EDU-05 | Needs citations; avoid medical language |
| CLM-03 | Handz builds a maintenance plan using evidence-informed principles (not guarantees) | Product claim tied to feature | Medium | Maintenance onboarding, Paywall | Requires precise description + disclaimers |
| CLM-04 | Average mastery timeline estimate for a path (placeholder metric) | Quantified claim (NOT locked) | High | Mastery screens, Upsell | PLACEHOLDER until research finalized |

#### Upsell Support Copy (Science-Layer Snippets)

| Snippet ID | Type | Exact Copy |
|------------|------|------------|
| UPS-SCI-01 | Headline | Train what matters most |
| UPS-SCI-02 | Body | Turn your flows into a maintenance plan. Pick the paths you care about, then rehearse them on a schedule that fits your week. |
| UPS-SCI-03 | Body | Handz uses evidence-informed learning principles like spaced repetition to support long-term recall. Results vary. |
| UPS-SCI-04 | Link | See the science & disclaimers |
| UPS-SCI-05 | Footnote | Handz is a training aid, not a substitute for coaching or medical advice. |

**Link behavior:** "See the science & disclaimers" routes to EDU-06. Any paywall or locked-feature screen referencing science must include this link.

#### Analytics Events (CH26)
- edu_view {page_id, entry_point, is_pro, app_version}
- edu_cta_click {page_id, cta_id}
- edu_reference_open {claim_id, citation_id}
- upsell_science_link_click {surface}

#### QA Checklist
- All education pages load offline (cached) with safe fallback if remote copy missing
- EDU-06 reachable from any science mention on paywall or upgrade gate
- No prohibited language anywhere (guarantee, cure, treat, diagnose, clinically, proven to, will make you)
- Mastery screens always show: "estimate" language + link to EDU-05
- References page shows authors/year/title (no raw URLs only)
- External links open per platform norms

#### App Store Review Risk Mitigations (V1)
- Avoid medical framing; use "training" and "learning" language
- Do not claim injury prevention or medical outcomes
- If adding numeric claims later: ensure substantiation and disclaimers

### Placeholders (CH26)
| ID | Description | Notes |
|----|-------------|-------|
| PH-26-01 | Any numeric performance claim ("2x faster", "30% better") | Do not ship in V1 unless sources finalized and legal-safe wording approved |
| PH-26-02 | Mastery timeline "average reps to subconscious" numbers | Define as estimates with ranges; require citations; must allow user adjustment |
| PH-26-03 | Exact maintenance schedule algorithm mapping goals -> cadence | Owned by CH24; CH26 updates copy once algorithm locked |
| PH-26-04 | Full citation list for EDU-07 | Populate SourcesPack with vetted citations; ensure each supports claim_id |

---

## Cross-References Summary

| From | To | Relationship |
|------|-----|--------------|
| CH23 | CH03 | Glossary definitions |
| CH23 | CH08 | Entitlements, caps |
| CH23 | CH20-CH22 | Practice integration |
| CH23 | CH24 | Maintenance handoff, overload prevention |
| CH23 | CH25 | Paywall, Pro gating |
| CH23 | CH26 | Scientific claims phrasing |
| CH23 | CH27 | Notifications |
| CH24 | CH03 | Definitions |
| CH24 | CH08 | Entitlement rules |
| CH24 | CH20-CH22 | Practice Mode pipeline |
| CH24 | CH23 | Mastery/Gameplan integration |
| CH24 | CH27 | Notifications scheduling |
| CH24 | CH28 | Offline behavior |
| CH24 | CH31 | Error states |
| CH25 | CH05 | Screen inventory |
| CH25 | CH07 | Auth |
| CH25 | CH08 | Entitlements |
| CH25 | CH17/CH18 | Share + Inbox rules |
| CH25 | CH20-CH22 | Practice behavior |
| CH25 | CH26 | Education pages link |
| CH25 | CH29/CH30 | Storage limits, abuse |
| CH26 | CH06 | Design system |
| CH26 | CH08 | Entitlements |
| CH26 | CH15 | Copy pack |
| CH26 | CH20-CH24 | Feature explanations |
| CH26 | CH25 | Paywall support |

---

## Open Questions (Unresolved Across Batch)

1. **Exact mastery thresholds** (CH23) — numerical values TBD
2. **Exact spaced-repetition schedule parameters** (CH23) — TBD
3. **Mastery planning Pro-only vs Free-limited** (CH23) — owned by CH08/CH25
4. **"Strict camp" mode** (CH23) — placeholder
5. **Default maintenance capacity** (CH24) — 3 items/week, 6 items/week, or minutes-based?
6. **Overdue threshold** (CH24) — 3/7/14 items?
7. **Cadence presets** (CH24) — 3/7/14 days or 2/5/10 days?
8. **Per-gameplan plans in V1** (CH24) — global only or allow per-gameplan?
9. **Pro gating for maintenance autoplan/reminders** (CH24/CH25) — autoplan for all or Pro-only?
10. **Annual plan pricing** (CH25) — none in V1 or add?
11. **Offline grace window** (CH25) — 12h/24h/48h?
12. **Practice Demo content pack** (CH25) — TBD
13. **Numeric performance claims** (CH26) — NOT allowed in V1 without finalized sources
14. **Full citation list** (CH26) — TBD

# PRD Batch 06 — CH20-CH22 (Practice Mode)

**Generated:** 2026-01-05
**Chapters:** CH20, CH21, CH22
**Status:** Extracted

---

## CH20 — Practice Mode: Setup

### Overview
Specifies complete Practice Setup experience: how users choose drill content, configure sessions, understand paywalls/credits, and start practice. Output is a Session Plan consumed by CH21 (active session) and CH22 (logging).

### Dependencies
- CH03, CH04, CH05, CH06, CH08, CH12, CH13, CH16, CH18, CH22

### Related
- CH09-CH11 (Moves), CH17-CH19 (Sharing/Inbox/Import), CH21, CH23-CH24 (Mastery)

### Definitions (CH20-specific)
- **Flow:** User-saved flowchart of move nodes and optional sequence nodes (See CH12/CH13)
- **Path:** Concrete traversal through a flow (root to end state); linear ordered list of nodes
- **Drill Item:** One unit drilled during practice; either (A) a Path, or (B) Whole-Flow drill that expands to multiple Paths at session start
- **Session Plan:** Configuration artifact listing ordered Drill Items with timing assumptions
- **Credit:** Free-plan token permitting one paid practice session on saved flows
- **Saved Flow:** Flow in user's library (not inbox or demo-only)

### Key Requirements

#### Entry Points
- `/practice` — Practice Tab Home (launcher)
- `/practice/setup` — Setup Wizard Root (no prefill)
- `/practice/setup?prefillFlowId=<id>` — Prefilled with one flow
- `/practice/setup?prefillGameplanId=<id>` — Prefilled with gameplan (CH23)
- `/practice/setup?prefillPathId=<id>` — Prefilled with specific path

#### Navigation Rules
- Setup is multi-step flow inside Practice tab stack
- Back button moves to previous step
- Leaving Practice tab prompts discard unsaved changes if selections exist
- Setup must be resumable within current app session (local state)
- Persisting setup drafts across app restarts: owned by CH28 (default: do not persist)
- Practice Setup never modifies flows; only reads flow data + writes Session Plan

#### Setup Flow (Wizard Steps)
**Step 0 — Eligibility Gate (conditional)**
- Guest state check and conversion prompt
- Free-plan credit availability check
- If blocked: offer Demo Practice (placeholder) or upgrade/signup routes

**Step 1 — Select Drill Items**
- Pick saved flows, then paths within them (or select full flows)
- Optionally pick gameplan (if exists per CH23)
- Show filters, search, and preview

**Step 2 — Order Drill Items**
- Drag to reorder final drill sequence
- Optionally group by flow or gameplan (toggle)
- Expose per-item quick edits

**Step 3 — Configure Timing**
- Set global defaults (sets, time per set, rest, assumed reps)
- Override per drill item if needed

**Step 4 — Review & Start**
- Summarize what will be drilled and estimated time
- Confirm credit spend if Free
- Start session → passes Session Plan to CH21

#### Practice Tab Home (PRACTICE_HOME)
**Layout:**
- Header: "Practice" + subtitle "Drill decisions until they're automatic."
- Primary CTA Card: "Start Practice" → PRACTICE_SETUP
- Supporting line: "Pick paths from any saved flow."
- If Free: credit badge "Credits: 3 left this month" (or "0 left")
- If Pro/Trial: badge "Unlimited"
- If Guest: badge "Sign up to save & practice"
- Continue (optional): "Continue Setup" or "Resume Session" card
- Quick Picks: Recent Flows (max 5), Recent Gameplans (max 3), "Browse Library"
- Motivation/Progress Snapshot: Streak tile, Last session summary (from CH22)

**Empty States:**
- No saved flows: "No saved flows yet." + CTA "Build a flow" + "View Demo Flow"
- No credits (Free): "Credits used. Upgrade for unlimited practice."

#### Eligibility Gate (PRACTICE_GATE)
**Trigger Conditions:**
- User taps "Start Practice" and is Guest → show gate
- User is Free and has 0 credits → show gate
- User is Free and attempts disallowed items (inbox-only flows) → show gate or inline error

**UI Copy:**
- Title: "Unlock Practice"
- Body: "Practice turns your flows into automatic reactions. To start a session you'll need an account, and Free uses monthly credits."
- Primary: "Create account" → AUTH (CH07)
- Secondary: "Start free trial" → PAYWALL (CH25)
- Tertiary: "Not now" → back to PRACTICE_HOME
- Optional: "Try a demo session" → PRACTICE_DEMO_SETUP (placeholder)

**Non-blocking messaging:** Must avoid guilt/shame; no red warnings; neutral language.

#### Step 1 — Select Drill Items (PRACTICE_SELECT)

**Source Types:**
- **Saved Flows (Library):** Selectable by logged-in users; Guest cannot have saved flows; select as "Whole Flow" or specific Paths
- **Gameplans (CH23):** Prebuilt set of Paths; allow session-only override of paths
- **Inbox Items:** View-only until saved to Library; NOT selectable by default; if prefill from inbox, show blocking banner "Save this flow to practice."
- **Demo Flows:** Do not count toward free saved-flow cap; whether practicable is placeholder

**Screen Layout:**
- Header: "Choose what to drill" + helper: "Select paths or entire flows. You can mix across flows."
- A) Collection Picker: Segment "Flows" | "Gameplans" (hidden if none); Search bar (flow titles/folder names only per CH15); Filter pills (Folder, Style tag, "Recently practiced", "Unpracticed", "Most used")
- B) List of selectable items (Flow Cards)
- C) Selected tray (bottom sticky): "Selected: N items", estimated time, "Next" + "Clear" buttons; Free note: "Credits are used when you start practice."

**Flow Card Selection Levels:**
- Level 1 — Whole flow: Checkbox at card-level; implies paths expanded at session build time; badge "Whole flow selected"
- Level 2 — Specific paths: Expand caret → path list with checkboxes; "Select all paths" helper; selecting any path unchecks "whole flow"
- Level 3 — Preview: Tap flow name → Flow Detail (CH16) view mode

**Path List Behavior:**
- Default label: concatenation of move names with arrows, truncated
- Secondary label: user-defined nickname (from CH16)
- Metadata chips: "Ends at: {move}", "Length: {N moves}", "Last practiced: {date}"
- "Select all" toggles all visible paths
- Long-press: "Preview path", "Select similar" (placeholder), "Add to gameplan" (CH23)
- Branch naming: Show condition snippet "if: leans back" with "+N"

**Validation (Step 1):**
- Flow deleted/unavailable: Gray out with "Unavailable", toast "This flow isn't available right now."
- Free at 2-flow cap with inbox import: Show "Max flows reached" modal (CH18/CH08)
- Free 0 credits: Allow selection, block at Step 4; show "Credits: 0" badge

#### Step 2 — Order Drill Items (PRACTICE_ORDER)

**Default Ordering:**
- Selection order (last selected appended)
- prefillFlowId: that flow's paths first
- prefillGameplanId: follows gameplan's defined order (CH23)

**UI Layout:**
- Header: "Order your drills" + helper: "Drag to reorder. Your session runs top to bottom."
- List: draggable cards (one per Drill Item)
- Footer: Back / Next

**Drill Item Card Fields:**
- Title: Path nickname or default arrow label
- Subtitle: Source flow name + folder tag
- Right-side: "Edit" (per-item config shortcut), "Remove"
- Badge: "Whole flow" if applicable

**Grouping Toggle (optional in V1, off by default):**
- "Group by flow" clusters items under flow headers
- Headers are visual only; session runs overall top-to-bottom order

#### Step 3 — Configure Timing (PRACTICE_CONFIG)

**Configuration Model:**
- Global defaults (apply unless overridden):
  - Sets per item (default: 3)
  - Time per set (default: 3:00 or 5:00; PLACEHOLDER owner CH20)
  - Rest between sets (default: 0:30)
  - Assumed reps per set (default: 15) — label "Assumed reps"
- Per-item overrides: Show "Overrides" pill if customized; fields mirror global

**UI Layout:**
- Top summary bar: Estimated session time range, number of drill items
- Global defaults section: Four compact controls (Sets, Time, Rest, Assumed reps); one tap to edit; steppers + presets (1:00/2:00/3:00/5:00)
- Per-item list: Effective config + "Edit" link → bottom sheet with fields + "Reset to global"

**Reps Language:**
- Label: "Assumed reps per set"
- Tooltip: "Handz doesn't count reps. It assumes you hit your target pace."
- Review: "We'll estimate total reps from your settings."

**Soft Caps (warn, don't block):**
- >40 drill items: "Big session. Consider splitting."
- >90 min estimated: "Long session. Keep it realistic."
- >50 assumed reps/set: "High reps. Make sure your form stays sharp."

**Hard Caps (block for stability):** PLACEHOLDER owner CH30/CH12
- Max drill items per session (default placeholder: 100)
- Max total session plan size in memory

#### Step 4 — Review & Start (PRACTICE_REVIEW)

**Review Summary:**
- Header: "Ready to practice?"
- Summary chips: "Items: N" • "Sets: X" • "Time/set: mm:ss" • "Rest: ss"
- Estimated session time (range)
- Drill items list (collapsed by default)

**Plan Gating:**
- Credits consumed at moment user starts (tap Start), not when configuring

**Guest:**
- Blocking card: "Create an account to practice."
- Buttons: "Create account", "Start free trial"
- Optional: "Try demo session"

**Free with credits > 0:**
- Line above Start: "Uses 1 credit (credits reset monthly)."
- Button: "Start (1 credit)"
- Tap Start → CREDIT_CONFIRM modal

**Free with credits = 0:**
- Button: "Start" disabled; primary CTA "Start free trial"
- Secondary: "See what you'd drill" expands list

**Pro/Trial:**
- Button: "Start practice" (no credits mention, no confirmation modal)

**CREDIT_CONFIRM Modal:**
- Title: "Use a practice credit?"
- Body: "This session will use 1 credit. Credits reset each month."
- Primary: "Use credit & start"
- Secondary: "Cancel"
- Link: "Upgrade for unlimited" → PAYWALL (CH25)

#### Session Plan Output (Data Contract)
```json
{
  "sessionPlanId": "uuid",
  "createdAt": "ISO-8601",
  "createdByUserId": "uuid",
  "source": {
    "entryPoint": "practice_tab | flow_detail | gameplan_detail | deep_link",
    "prefill": {"flowId": "uuid|null", "gameplanId": "uuid|null", "pathId": "uuid|null"}
  },
  "planState": "guest | free | pro | trial",
  "creditSpend": {"willSpendOnStart": true|false, "creditsRemainingBeforeStart": number|null},
  "defaults": {
    "setsPerItem": 3,
    "timePerSetSec": 180,
    "restSec": 30,
    "assumedRepsPerSet": 15
  },
  "items": [{
    "itemId": "uuid",
    "type": "path | whole_flow",
    "flowId": "uuid",
    "flowNameAtBuild": "string",
    "pathId": "uuid|null",
    "pathLabelAtBuild": "string|null",
    "nodeIds": ["..."],
    "configOverride": {...} | null
  }],
  "ui": {"grouping": "none | by_flow", "orderLocked": true},
  "validation": {"warnings": ["..."], "blockedReasons": ["..."]}
}
```

**Notes:**
- flowNameAtBuild/pathLabelAtBuild are snapshots for logging readability
- nodeIds optional in setup; CH21 can expand paths at runtime
- validation.warnings collected for CH22 debug

#### Screen Routing Summary
| Screen | Primary Actions | Secondary Actions |
|--------|-----------------|-------------------|
| PRACTICE_HOME | Start Practice → PRACTICE_SELECT (or GATE) | Browse Library, View Demo Flow |
| PRACTICE_GATE | Create account → AUTH; Start free trial → PAYWALL | Not now → PRACTICE_HOME |
| PRACTICE_SELECT | Next → PRACTICE_ORDER; checkbox selections | Clear, Flow title → CH16 |
| PRACTICE_ORDER | Next → PRACTICE_CONFIG; drag reorder; Remove | Back → PRACTICE_SELECT |
| PRACTICE_CONFIG | Next → PRACTICE_REVIEW; Edit item | Back → PRACTICE_ORDER |
| PRACTICE_REVIEW | Start → CH21 PRACTICE_ACTIVE | Back → PRACTICE_CONFIG |

#### Error States (Setup-Specific)
- **No network loading flows:** Skeleton → timeout → "Can't load your flows right now." + Retry, Go to Library; if cached: show with "Offline — changes may not sync."
- **Path list computation failure:** Allow whole-flow selection only; tooltip "Path list unavailable right now."
- **Credits desync (client=1, server=0):** On Start, server is truth; if denied: "Credits unavailable" + paywall; do not start
- **Flow deleted mid-setup:** At Review, revalidate; disabled items with "Flow removed."; Start disabled until removed

#### Security (Setup Layer)
- Do not trust local credit counts; confirm on start
- Debounce rapid Start taps
- Enforce hard cap placeholders before building plan object
- Credit spend transactional (idempotency key = sessionPlanId)
- Practice eligibility enforced by server rules/Cloud Functions (CH08/CH25)

### Placeholders (CH20)
| ID | Description | Options | Default | Decide-by |
|----|-------------|---------|---------|-----------|
| CH20-P1 | Save-as-Preset for session setups | Off in V1 / Pro-only | Off | CH22/CH23 |
| CH20-P2 | Time per set default | 3:00 / 5:00 | TBD | CH20 |
| CH20-P3 | Demo flow practice | Allow / Block | TBD | before beta |
| CH20-P4 | Max drill items per session | 100 / other | 100 | CH30/CH12 |

---

## CH21 — Practice Mode: Active Session

### Overview
Defines in-session behavior: what user sees once practice starts, how timers/sets advance, controls, how "actual duration" and interruptions are recorded. Setup owned by CH20; logging owned by CH22.

### Dependencies
- CH06, CH08, CH12, CH20

### Related
- CH03, CH04, CH05, CH22, CH23, CH24, CH25, CH29, CH30, CH31, CH33

### Definitions (CH21-specific)
- **Sequence Item:** One drilled unit in session queue (typically a selected path); created in CH20
- **Set:** One timed work block for a Sequence Item
- **Rest:** Recovery block between sets or between Sequence Items
- **Actual Duration:** Wall-clock time from session start to end; includes pauses unless excluded per rule
- **Interrupted:** Session ended early (user ended, app backgrounded too long, crash recovery); partial results preserved

### Primary User Intent
- Usable with minimal taps, large targets, predictable state changes (user wearing gloves, moving)
- Keep interaction count low (most actions = 1 tap)
- Never require typing during active set
- Avoid ambiguous "did it save?" moments; state always obvious (Running / Paused / Rest / Completed / Interrupted)
- Track actual duration and what was accomplished (sets completed, sequences completed)

### Screens & Navigation (In-Session)
| Surface | Type | When Shown | Exit Conditions |
|---------|------|------------|-----------------|
| Practice Active Session | Full screen | Session running (work set) | Auto-advance to Rest when timer hits 0 or user completes; pause or end |
| Practice Rest | Full screen | Between sets (or items) | Auto-advance when rest timer hits 0; skip rest or end |
| Practice Paused | Full screen overlay | User pauses during Active or Rest | Resume returns to prior; End Practice exits to Session Summary (CH22) |
| Confirm End Practice | Modal | User taps End Practice | Confirm ends session; Cancel returns |

**Back Behavior:** OS/back gesture must not silently discard state; triggers Confirm End Practice.

### Active Session UI (PRACTICE_ACTIVE_SESSION)

**Layout Regions:**
- Top bar: back/close (opens Confirm End), session status pill (Running), progress indicator
- Primary focus block: Current Sequence Item (path name), compact path preview (move list)
- Timer block: Large countdown (MM:SS) with total time (e.g., 03:47 / 05:00)
- Set progress: "Set X of Y" and "Item N of M"
- Controls row: Large buttons (Pause, Completed Set, Skip Set); secondary in "More"
- Safety zone: Bottom padding for iPhone home indicator

**Required Visible Fields:**
- Current Item Title (user-defined name or default)
- Context subtitle: source flow name
- Timer (counting down)
- Set counter: "Set {currentSetIndex+1} of {setsForItem}"
- Queue counter: "Item {queueIndex+1} of {queueLength}"
- Assumed reps: "Reps: {assumedReps} (assumed)" — no live rep counting
- Status: Running

**Button Set:**
| Control | Placement | Tap Behavior | Long-press | Notes |
|---------|-----------|--------------|------------|-------|
| Pause | Primary | Pause timer + Paused overlay | No | Also during Rest |
| Completed Set | Primary | End set immediately, start Rest | No | Early completion |
| Skip Set | Primary | Mark skipped, start Rest | No | |
| End Practice | Secondary (More) | Confirm End modal | No | Never immediate |
| Add Set | Secondary (More) | +1 extra set (optional) | Optional | V1 placeholder |

**Glove-friendly:** Primary buttons min 44pt tall, ideally 52-60pt, large touch targets.

**Minimal Clutter:** V1 shows only 3 primary buttons; additional in "More" menu.

### Rest Screen UI (PRACTICE_REST)

**Required Visible Fields:**
- Rest timer: large countdown (SS or MM:SS)
- Next item: title + optional preview
- Progress: "Set complete", queue position
- Status: Resting

**Rest Controls:**
- Skip Rest (primary): ends rest immediately, starts next work set
- Pause (secondary or primary if two buttons): opens Paused overlay
- End Practice (secondary in More): opens Confirm End

### State Machine

**Core States:**
- RUNNING_SET: active work timer counting down
- RESTING: rest timer counting down
- PAUSED: timer halted; overlay blocks accidental taps
- ENDED_COMPLETED: session finished all planned sets/items
- ENDED_INTERRUPTED: session ended early (user ended, forced stop, crash recovery)

**Early Completion (Completed Set):**
- Stop set timer, mark completed, record elapsed work time
- Remaining time not carried forward (saved as "unused set time")
- UI feedback: haptic + "Set complete", then Rest screen
- Undo toast for 3 seconds (v1 recommended): return to RUNNING_SET with prior remaining time

**Skip Set:**
- Mark set as skipped; record elapsed work time
- Transition to RESTING (or next work set if rest=0)
- Skipped sets count toward "planned sets attempted" but not "sets completed"
- UI: "Set skipped" message

**Timer Reaches 0 (Auto-Complete):**
- Work timer: automatically complete set, transition to RESTING
- Rest timer: automatically start next work set
- Timer ticks must clamp at 0 (never negative)

**Progression Rules:**
- Each Sequence Item has planned sets (from CH20)
- Run set → rest → run set → rest... until sets exhausted
- After final set of item: transition to RESTING (if configured) or next item's RUNNING_SET
- Last set of last item completes: transition to ENDED_COMPLETED → CH22 Session Summary

**Confirm End Practice:**
- Always 2-step: tap End → confirm modal
- Copy: "End practice session? Your progress will be saved as Interrupted."
- Buttons: "Keep Practicing" (default), "End Session"
- If confirmed: ENDED_INTERRUPTED → CH22 summary (status=interrupted)

**Actual Duration & Pause Accounting:**
- SessionStartTimestamp: moment user enters RUNNING_SET first time
- SessionEndTimestamp: transition to ENDED_COMPLETED or ENDED_INTERRUPTED
- ActualDuration = SessionEndTimestamp - SessionStartTimestamp (includes pause time)
- ActiveWorkDuration = sum of elapsed time in RUNNING_SET
- RestDuration = sum of elapsed time in RESTING
- PausedDuration = ActualDuration - ActiveWorkDuration - RestDuration

**Backgrounding & Interruptions (iOS):**
- App goes to background during RUNNING_SET or RESTING: immediately pause, show "Paused due to leaving app" on return
- Background longer than threshold: session saved as Interrupted on next app open; PLACEHOLDER: BackgroundTimeoutSeconds (default: 300s / 5min)
- App crashes: on next launch show "Resume interrupted session?" with Resume or Discard

### Haptics, Sound, Wake Lock

**Haptics (V1):**
- Set complete (auto or manual): light haptic
- Rest complete / next set starts: light haptic + optional short chime
- Error: warning haptic (rare)

**Audio Cues (V1):**
- Optional: last 3 seconds countdown ticks (off by default); PLACEHOLDER: AudioCountdownDefault
- Optional: set-start and rest-start short sound (on by default); PLACEHOLDER: AudioCueDefault
- Must respect device mute switch

**Wake Lock:**
- During RUNNING_SET and RESTING: keep screen awake
- Manual screen lock: treat as backgrounding → session pauses

### Edge Cases & Error States

**Timer Drift / Device Lag:**
- Timers must be wall-clock-based (now() vs start timestamp)
- On drift detection: recompute remaining time from wall-clock, clamp >= 0

**Low Battery / Performance:**
- Animations stutter: timers and controls remain responsive
- No heavy flow diagrams in-session; compact path preview only

**Entitlement Mismatch Mid-Session:**
- If entitlement lost mid-session: allow session to finish; apply restrictions next time
- Credit-based session with 0 credits: block start in CH20, not here

**Navigation Accidents:**
- Swipe/back attempt: intercept → Confirm End
- OS call interrupts: auto-pause, show paused banner afterward

### Data Emitted to Logging Layer (Contract for CH22)
**Per Set:**
- start timestamp, end timestamp, planned duration, actual elapsed duration
- completion status (completed/skipped/auto-completed)
- whether ended early

**Per Rest Segment:**
- start timestamp, end timestamp, planned rest duration, actual rest duration
- skippedRest boolean

**Per Session:**
- status (completed vs interrupted)
- actual duration, active work duration, rest duration, paused duration
- queue summary counts

### UX Copy (In-Session)
- Running state label: "Practice" or "Live Practice"
- Completed Set feedback: "Set complete"
- Skipped Set feedback: "Set skipped"
- Rest label: "Rest"
- Auto-advance message: "Next: {ItemTitle}"
- Pause banner on resume: "Paused while you were away"
- End confirm: "End session? Progress will be saved as interrupted."
- Completion: "Session complete"
- Interruption: "Session saved"

### Placeholders (CH21)
| ID | Description | Options | Default | Decide-by |
|----|-------------|---------|---------|-----------|
| BackgroundTimeoutSeconds | Background time before interrupted | 120 / 300 / 600 | 300 | before resume flow |
| AudioCountdownDefault | Last 3s countdown ticks | on / off | off | CH06 audio review |
| AudioCueDefault | Set/rest start sounds | on / off | on | CH06 audio review |
| UndoCompletedSetToast | Undo early complete | on / off | on | usability test |
| AddSetInSession | +1 set control | include / exclude in V1 | include as +1 only | complexity check |

---

## CH22 — Practice Mode: Logging & History

### Overview
Defines logging + history for Practice Mode: what was accomplished, how stored, how shown back (history, streaks, records), and how to prevent inconsistencies when flows change later.

### Dependencies
- CH08, CH20, CH21, CH25, CH27, CH28, CH33

### Related
- CH04, CH05, CH06, CH23, CH24, CH30, CH31, CH34, CH35, CH36, CH37

### Definitions (CH22-specific)
- **Practice Session:** One run of Practice Mode from Start → End (or interruption); contains one or more drill items
- **Drill Item:** One selected path/sequence drilled in session with per-item config
- **Set:** One timed work interval; outcomes defined in CH21, recorded here
- **Planned Duration:** Expected duration based on configuration
- **Actual Duration:** Time actually spent, subtracting paused time
- **Assumed Reps:** Trust-based reps credited when set completed
- **Qualifying Practice Day:** Local-calendar day where user completed at least 1 set in any session
- **Orphaned Session:** Log whose referenced flow/path no longer exists; viewable via frozen snapshot

### Inputs from CH21 (Session Output Contract)
- Session lifecycle timestamps: sessionStartAt, sessionEndAt, totalPausedMs, appBackgroundIntervals[], deviceTimezoneAtStart
- Session status: completed | ended_early | interrupted
- Drill items in order: path/sequence reference + config (setsPlanned, repsPerSetAssumed, secondsPerSet, secondsRestBetweenSets) + per-set outcomes
- Per-set outcomes: startAt, endAt, outcome, completedBy (auto/manual), elapsedMs, restSkipped, userActions[]
- Navigation context: entry point, plan state (Guest/Free/Pro/Trial)

### Pages & Routing
- **PracticeTab (CH04):** Entry point with "Start Practice", "History", "Streak"
- **PracticeSessionSummaryModal:** Shown when session ends; has "Log Session" (if not auto-logged) and "View History"
- **PracticeHistoryList:** Chronological list of past sessions with filters
- **PracticeHistoryDetail:** Detail view for one session log
- **StreakAndRecords:** Streak cards, best streak, total practice days, top drilled paths, records
- **PracticeExport:** Export/share of history data (CH34 owns, CH22 defines included data)

**Routing Rules:**
- PracticeTab → "History" → PracticeHistoryList
- PracticeSessionSummaryModal → "View History" → PracticeHistoryDetail (just-finished session)
- PracticeHistoryList → tap row → PracticeHistoryDetail
- PracticeHistoryDetail → "Open Flow" → FlowDetailView (CH16); if missing, show Orphaned state
- Any history screen → "Export" → PracticeExport (CH34)

### Session Status Model
| Status | Meaning |
|--------|---------|
| completed | Session ran through planned drill items, ended normally |
| ended_early | User intentionally ended via "End Practice"; may or may not have completed sets |
| interrupted | Session didn't end cleanly (app killed, crash, OS kill) |

**Additional Flags:**
- qualifiesForStreak (bool): computed
- creditConsumed (bool): whether Free credit consumed
- hasOrphans (bool): any drill item references missing flow/path

### Core Computations

**Planned Duration:**
- plannedWorkSeconds = sum over items (setsPlanned x secondsPerSet)
- plannedRestSeconds = sum over items ((setsPlanned - 1) x secondsRestBetweenSets) + inter-item rest (if CH21 defines)
- plannedTotalSeconds = plannedWorkSeconds + plannedRestSeconds

**Actual Duration:**
- sessionWallClockMs = sessionEndAt - sessionStartAt
- actualDurationMs = sessionWallClockMs - totalPausedMs
- Clamp at 0; never negative
- Background time = paused if session auto-pauses
- Display: mm:ss (< 1 hour); hh:mm:ss (>= 1 hour)

**Assumed Reps:**
- App does NOT rep-count; credits assumed reps for completed sets only
- For each set: if outcome in {completed_auto, completed_manual} then repsCredited = repsPerSetAssumed; else 0
- itemAssumedReps = sum across set attempts
- sessionAssumedReps = sum across items
- Store both "assumed reps" and "completed sets"

**Completed Set Counting:**
- completedSetCount = count of outcomes completed_auto or completed_manual
- Skipped sets do not count as completed
- Timer hits 0 without action = completed_auto

### Log Immutability, Snapshots, Orphan Handling

**Reference Fields (Pointers):**
- flowId (nullable)
- pathId or pathSignature (nullable)
- flowRevisionAtPractice (optional) or updatedAt timestamp

**Frozen Snapshot Fields:**
- displayNameAtPractice: flow name + path label at practice time
- moveSequenceSnapshot: ordered list of move display names
- nodeIdsSnapshot: list of node IDs
- notesSnapshot: per-item notes (if applicable)

**Flow Edited After Practice:**
- History renders frozen snapshot by default (logs never "change")
- Secondary action: "Open current version" (if flowId exists) → FlowDetailView
- If current differs from snapshot: info note "This log shows what you drilled on {date}. The flow may have changed since."

**Flow Deleted:**
- Practice log remains
- Detail shows "Deleted Flow" where "Open Flow" would be
- hasOrphans=true; filtering can include/exclude
- Export includes snapshot data

### Streaks, Consistency, Records

**Qualifying Practice Day:**
- Day qualifies if completedSetCount >= 1 in any session whose local-date (at sessionEndAt) is that day
- Local-date from device timezone (deviceTimezoneAtStart)
- Multiple sessions on same day = 1 practice day
- Interrupted then resumed: only final logged record counts; no double-count

**Current Streak Computation:**
- currentStreakDays = consecutive qualifying days ending at today (local)
- If yesterday not qualifying: streak resets to 0 (or 1 if today qualifies)
- Store: currentStreakDays, bestStreakDays, lastQualifyingDateLocal, totalPracticeDays

**"Forgiving" UX (No Lying):**
- Streak breaks: "New streak starts today. You've still practiced {N} days total."
- 0 qualifying days: "Run your first practice session to start tracking."
- Practiced but 0 completed sets: "Session saved, but streak starts when you complete at least one set."

**Records (V1 Simple):**
- Longest session (actualDurationMs max)
- Most sets completed in one session
- Most assumed reps in one session
- Most practiced drill item (count of sessions including that item)

### Plan Gating (Credits, Paywall, Logging)

**Credit Reserve/Consume Model:**
- At session start (Free with credits > 0): create "credit reservation" attached to sessionId
- At session end: if completedSetCount >= 1, consume (creditConsumed=true)
- If completedSetCount == 0: release reservation; no credit consumed
- App crashes with reservation: on launch offer "Resume session" or "Discard"; Discard releases
- Reservation expires after window (PLACEHOLDER: 12h)

**What Free Can/Cannot Practice:**
- Free: only saved flows (in library)
- Free: cannot practice inbox items (view-only)
- Free: saved flows cap=2; can view inbox but cannot save more without deleting
- Trial: behaves as Pro (CH08)

**History Visibility by Plan:**
- Guest: no persistence; demo history in-session only
- Free: full history for sessions they ran
- Pro/Trial: same as Free + Pro-only metadata if added

### Screen Specs

**PracticeSessionSummaryModal:**
- When: Immediately after session ends (completed/ended_early); on resume for interrupted
- Header: "Practice complete" or "Practice ended early"
- Always visible: Actual Duration, Planned Duration, Completed Sets, Assumed Reps, Drill Items Completed, Streak
- Optional breakdown (expand/collapse): per-item rows
- Buttons: [Done] → PracticeTab; [View Details] → PracticeHistoryDetail; [View History] → PracticeHistoryList
- Auto-save: Logs created automatically; if fail, non-blocking toast + local pending log for retry (CH28)

**PracticeHistoryList:**
- Top bar: "History", back, overflow (Export)
- List grouped by local date (Today, Yesterday, date label)
- Row: Time, Status badge, Actual duration, Completed sets + assumed reps, Top 1-2 drill item names, Streak day indicator
- Filters: All / Completed / Ended Early / Interrupted; Flow picker (optional V1); Time range (7/30/All)
- Search (V1 optional): across snapshot names
- Buttons: Tap row → PracticeHistoryDetail; Export → PracticeExport (CH34)

**PracticeHistoryDetail:**
- Top bar: back, "Session", overflow (Export Session, Delete Session per CH34)
- Header: status, date/time, streak indicator
- Actual duration (big), Planned duration (small)
- Completed/skipped/ended-early sets (counts)
- Assumed reps (label "assumed")
- Credit consumed badge (Free only: "1 credit used"; Pro hides)
- Drill item timeline: expandable cards per item with per-set details
- Open Flow button (if flowId exists) → FlowDetailView
- Orphan: if flowId missing, disable Open Flow, show "Flow not available"

**StreakAndRecords:**
- Top cards: Current streak (days) with note, Total practice days, Best streak
- Records section: 4 records with values and dates
- Buttons: [View History], [Export]

### Data Model

**practiceSession:**
- sessionId, userId, createdAt, sessionStartAt, sessionEndAt, deviceTimezoneAtStart
- status (completed | ended_early | interrupted)
- plannedTotalSeconds, actualDurationMs, totalPausedMs
- completedSetCount, skippedSetCount, assumedRepsTotal
- qualifiesForStreak, streakDayLocal (YYYY-MM-DD)
- creditConsumed, creditReservationId
- startedFrom (practice_tab | flow_detail | gameplan)
- planStateAtRun (guest | free | pro | trial)
- hasOrphans, items[] (summary array)

**practiceSessionItem:**
- itemId, sessionId, orderIndex
- flowId, pathId/pathSignature (nullable)
- displayNameAtPractice, moveSequenceSnapshot[], nodeIdsSnapshot[]
- config: setsPlanned, repsPerSetAssumed, secondsPerSet, secondsRestBetweenSets
- computed: completedSetCount, assumedReps, actualWorkMs, actualRestMs

**practiceSetAttempt:**
- setId, sessionId, itemId, setIndex (1-based)
- startAt, endAt, elapsedMs
- outcome: completed_auto | completed_manual | skipped | aborted
- completedBy: auto | user
- repsCredited (int)

**streakSummary:** denormalized per-user counters

**Firebase Structure Suggestion:**
```
users/{userId}/practiceSessions/{sessionId}
users/{userId}/practiceSessions/{sessionId}/items/{itemId}
users/{userId}/practiceSessions/{sessionId}/items/{itemId}/sets/{setId}
users/{userId}/streakSummary
```

### Placeholders (CH22)
| ID | Description | Options | Default | Decide-by |
|----|-------------|---------|---------|-----------|
| Reservation Expiry Window | Credit reservation timeout | 1h / 12h / 24h | 12h | before beta |
| Inter-item Rest Inclusion | Include in planned duration | include / exclude | include if CH21 defines | when CH21 finalized |
| Pro-rating Reps on Skipped | Credit partial reps | 0 reps / time-proportional / user-editable | 0 reps | post-MVP |
| History Search in V1 | Search capability | none / basic / advanced | none | scope lock |
| Record Types Expansion | Add mastery-linked records | keep simple / expand | keep simple | after CH23 locked |

---

## Cross-References Summary

| From | To | Relationship |
|------|-----|--------------|
| CH20 | CH03 | Definitions |
| CH20 | CH04/CH05 | Navigation routes |
| CH20 | CH06 | Design tokens |
| CH20 | CH08 | Entitlements, credits, caps |
| CH20 | CH12/CH13 | Flow/path definitions |
| CH20 | CH16 | Flow Detail preview |
| CH20 | CH18 | Inbox restriction |
| CH20 | CH21 | Session Plan output |
| CH20 | CH22 | Logging consumes plan |
| CH20 | CH23 | Gameplans |
| CH20 | CH25 | Paywall |
| CH21 | CH06 | Design system |
| CH21 | CH08 | Entitlements |
| CH21 | CH12 | Flow rendering |
| CH21 | CH20 | Session Plan input |
| CH21 | CH22 | Session output contract |
| CH21 | CH28 | Offline/sync |
| CH21 | CH30 | Limits |
| CH21 | CH31 | Error states |
| CH22 | CH08 | Plan gating |
| CH22 | CH20 | Drill item config source |
| CH22 | CH21 | Session data input |
| CH22 | CH23 | Mastery metrics |
| CH22 | CH24 | Maintenance |
| CH22 | CH25 | Credits |
| CH22 | CH27 | Notifications |
| CH22 | CH28 | Offline persistence |
| CH22 | CH33 | Analytics events |
| CH22 | CH34 | Export |

---

## Open Questions (Unresolved Across Batch)

1. **Time per set default** (CH20) — 3:00 or 5:00?
2. **Demo flow practice** (CH20) — Allow or block?
3. **Background timeout** (CH21) — 120/300/600 seconds?
4. **Audio cue defaults** (CH21) — On or off?
5. **Add Set in V1** (CH21) — Include or exclude?
6. **Credit reservation expiry** (CH22) — 1h/12h/24h?
7. **History search in V1** (CH22) — None, basic, or advanced filters?
8. **Record types expansion** (CH22) — Keep simple or add mastery-linked?

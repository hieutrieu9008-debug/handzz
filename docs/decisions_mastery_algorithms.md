# Mastery Thresholds & Algorithms — PRD Extraction

**Date:** 2026-01-06
**Status:** FINAL — ALL DECISIONS LOCKED
**Related Blockers:** PM-B06, PM-B07, PM-DC03, PM-DC04

---

## LOCKED (Verbatim PRD)

### Mastery States (CH23 L48-56)

Each Path has a MasteryState:
- **Not Started** — never practiced in Practice Mode (or no qualifying logs)
- **In Progress** — actively training; plan has remaining sessions before target readiness
- **On Track** — meeting recommended cadence; forgetting risk is low
- **Needs Review** — drifting; forgetting risk rising; maintenance recommended
- **Maintained** — completed initial plan and maintained at defined cadence
- **Stale** — not reinforced for long period; treat as partially forgotten
- **User-Adjusted** — flag indicating user manually adjusted mastery up/down

Source: CH23 L48-56

### Qualifying Practice (CH22 L600-604)

- **Qualifying Practice Day:** Local-calendar day where user completed at least 1 set in any session
- Day qualifies if `completedSetCount >= 1` in any session whose local-date (at sessionEndAt) is that day
- Local-date from device timezone (deviceTimezoneAtStart)
- Multiple sessions on same day = 1 practice day

Source: CH22 L600-604

### Completed Set Counting (CH22 L569-572)

- `completedSetCount` = count of outcomes `completed_auto` or `completed_manual`
- Skipped sets do not count as completed
- Timer hits 0 without action = `completed_auto`

Source: CH22 L569-572

### Streak Computation (CH22 L606-609)

- `currentStreakDays` = consecutive qualifying days ending at today (local)
- If yesterday not qualifying: streak resets to 0 (or 1 if today qualifies)
- Store: `currentStreakDays`, `bestStreakDays`, `lastQualifyingDateLocal`, `totalPracticeDays`

Source: CH22 L606-609

### Goal Types (CH23 L60-68)

| Goal Type | Description |
|-----------|-------------|
| Remember (Technique Recall) | Accurate recall of sequence and decision path |
| React Faster (Decision Speed) | Fast selection of correct response; higher volume/cadence |
| Fight Camp (Readiness by date) | User sets target date; plan schedules to reach readiness |
| Coach Curriculum (Teach & retain) | Similar to Recall; optimized for assigning to students |
| Just Drill (Simple) | Minimal setup; mastery tracking uses default intensity |

Source: CH23 L60-68

### Intensity Profiles (CH23 L71-77)

Each Goal Type maps to an Intensity Profile defining defaults for: sessions per week, sets per path, target repetitions/time, maintenance cadence.

- **Light** — hobbyists or low frequency training
- **Standard** — default for most users
- **Aggressive** — fighters in camp or users seeking faster automation
- **Custom** — user can override defaults (within safety constraints; see CH30)

Source: CH23 L71-77

### Intensity Override Rules (CH23 L79-82)

- User can always decrease intensity without warnings
- Increasing above defaults may trigger soft warnings + one-tap confirmation (CH30)
- Overrides remembered at Gameplan level; may be overridden per-path (advanced)

Source: CH23 L79-82

### Maintenance Goal Types (CH24 L362-365)

- "Remember it" (light maintenance)
- "Use it under pressure" (heavier maintenance)
- "Fight camp" (time-bounded; optional future placeholder)

Source: CH24 L362-365

### Maintenance Cadence Model (CH24 L467)

`cadence_model`: "auto | fixed | custom"

Source: CH24 L467

### Urgency Scoring Inputs (CH24 L401-403)

- Inputs: last practiced date, goal type, user importance rating (optional), recent usage frequency, upcoming "needs" date (future placeholder)
- Output buckets for UI: "Due", "Soon", "Later"

Source: CH24 L401-403

### MasteryRecord Schema (CH23 L217-218)

```
MasteryRecord:
- id (UUID)
- owner_user_id
- path_signature
- mastery_state (enum)
- user_adjusted_flag (bool)
- system_estimated_state (enum)
- last_practiced_at
- sessions_completed_count
- sets_completed_count
- assumed_reps_total
- next_review_due_at (computed)
- maintenance_cadence_profile (computed/selected)
- created_at
- updated_at
```

Source: CH23 L217-218

### MaintenanceItem Schema (CH24 L458-474)

```
MaintenanceItem:
- id: string
- plan_id: string
- label: string
- target_type: "path | path_set | flow | gameplan"
- target_ref: { }
- importance: "low | med | high | null"
- cadence_model: "auto | fixed | custom"
- cadence_days: "number | null"
- next_due_at: ISODateTime
- last_done_at: "ISODateTime | null"
- created_at: ISODateTime
- updated_at: ISODateTime
- is_archived: boolean
```

Source: CH24 L458-474

### Trust Controls (CH23 L148-157)

**Adjust Mastery (Path-level):**
- Available from Path Detail and Gameplan Detail (per path)
- Options: Mark as Not Started / In Progress / Maintained / Needs Review / Stale
- Reason capture: optional quick-select ("Haven't drilled in real sparring", "Injured", "Too easy", "App overestimated"); one-tap and skippable
- User-Adjusted flag shown with subtle indicator

**Guardrails:**
- Never block user from adjusting (no hard restriction)
- If user increases mastery: show note "Handz will use this to reduce reminders."
- If user downgrades: surface helpful action "Want a quick review session now?"

Source: CH23 L148-157

### No Gloves-On Rating Requirement (CH23 L180-183)

- Must NOT require users to rate performance mid-set
- Use logged completion signals from Practice Mode: set completed, interruptions, early-end actions (CH21)
- Optional post-session reflection allowed but must be skippable and low effort (CH22)

Source: CH23 L180-183

### Status Framing — No-Shame Design (CH24 L428-439)

**Status pills:**
- **On Track**: completed at least one due item within last X days OR backlog below threshold
- **Behind**: backlog beyond threshold OR no maintenance completed within window
- **Paused**: plan paused by user; no reminders; hub shows "Resume" CTA
- **Not Set Up**: no maintenance plan; hub shows setup CTA

**Copy rules:**
- Never say: "You failed", "You broke your streak"
- Prefer: "Pick up where you left off", "Resume", "You're one session away from being back on track"
- Use neutral metrics: "items completed" and "items due"; streaks optional and secondary (CH22)

Source: CH24 L428-439

### Snooze Behavior (CH23 L247-250)

- Always optional and never shaming
- Options: Later today / Tomorrow / This weekend / Custom (if CH24 allows)
- Snoozing updates due date but does not change mastery state until practiced

Source: CH23 L247-250

### Orphaned Paths After Flow Edits (CH23 L221-226)

- If flow edit removes nodes referenced by Gameplan path: path becomes orphaned
- Label clearly in Gameplan Detail: "Needs update"
- Actions: [Fix Path], [Remove from Gameplan], [Keep as reference] (view-only)
- MasteryRecord frozen; no maintenance reminders until resolved

Source: CH23 L221-226

### Timezone Shift Handling (CH24 L515)

- Rebase due dates to local midnight boundaries to avoid instant overdue spikes

Source: CH24 L515

---

## Blocking Decisions — ALL LOCKED

| ID | Decision | Resolution | Status |
|----|----------|------------|--------|
| MA-D01 | Sessions to reach "On Track" | **3 sessions** | **LOCKED** |
| MA-D02 | Sessions to reach "Maintained" | **6 sessions** | **LOCKED** |
| MA-D03 | Days before "Needs Review" | **14 days** | **LOCKED** |
| MA-D04 | Days before "Stale" | **30 days** | **LOCKED** |
| MA-D05 | Stale recovery penalty | **-2 sessions** | **LOCKED** |
| MA-D06 | Light cadence | **14 days** | **LOCKED** |
| MA-D07 | Standard cadence | **7 days** | **LOCKED** |
| MA-D08 | Aggressive cadence | **3 days** | **LOCKED** |
| MA-D09 | Max maintenance interval | **30 days** | **LOCKED** |
| MA-D10 | Light intensity profile | **1x/week, low sets, short duration** | **LOCKED** |
| MA-D11 | Standard intensity profile | **2-3x/week, moderate sets** | **LOCKED** |
| MA-D12 | Aggressive intensity profile | **4-5x/week, high intensity** | **LOCKED** |

---

## LOCKED DECISIONS

### MA-D01: Sessions to reach "On Track" — **LOCKED: 3 sessions**

**Decision:** A path transitions from "In Progress" to "On Track" after 3 qualifying sessions.

**Resolution Date:** 2026-01-06

---

### MA-D02: Sessions to reach "Maintained" — **LOCKED: 6 sessions**

**Decision:** A path transitions from "On Track" to "Maintained" after 6 total qualifying sessions.

**Resolution Date:** 2026-01-06

---

### MA-D03: Days before "Needs Review" — **LOCKED: 14 days**

**Decision:** A path transitions to "Needs Review" after 14 days without practice.

**Resolution Date:** 2026-01-06

---

### MA-D04: Days before "Stale" — **LOCKED: 30 days**

**Decision:** A path transitions to "Stale" after 30 days without practice.

**Resolution Date:** 2026-01-06

---

### MA-D05: Stale recovery penalty — **LOCKED: -2 sessions**

**Decision:** When a user practices a Stale path, their session count is reduced by 2 (penalty for decay). Path transitions to "In Progress" with adjusted count.

**Resolution Date:** 2026-01-06

**Implications:**
- If user had 6 sessions (Maintained) and went Stale, practicing resets to 4 sessions
- Minimum floor is 0 sessions (cannot go negative)

---

### MA-D06: Light cadence — **LOCKED: 14 days**

**Decision:** Light intensity maintenance cadence is 14 days between recommended sessions.

**Resolution Date:** 2026-01-06

---

### MA-D07: Standard cadence — **LOCKED: 7 days**

**Decision:** Standard intensity maintenance cadence is 7 days between recommended sessions.

**Resolution Date:** 2026-01-06

---

### MA-D08: Aggressive cadence — **LOCKED: 3 days**

**Decision:** Aggressive intensity maintenance cadence is 3 days between recommended sessions.

**Resolution Date:** 2026-01-06

---

### MA-D09: Max maintenance interval — **LOCKED: 30 days**

**Decision:** Maximum maintenance interval is 30 days. Even Light profile cannot exceed this.

**Resolution Date:** 2026-01-06

**Implications:**
- Aligns with Stale threshold (MA-D04)
- Ensures no path drifts beyond Stale without reminder

---

### MA-D10: Light intensity profile — **LOCKED: 1x/week, low sets, short duration**

**Decision:** Light intensity profile parameters:
- Sessions per week: 1
- Sets: Low (2-3 per path)
- Duration: Short
- Rest: Standard

**Resolution Date:** 2026-01-06

---

### MA-D11: Standard intensity profile — **LOCKED: 2-3x/week, moderate sets**

**Decision:** Standard intensity profile parameters:
- Sessions per week: 2-3
- Sets: Moderate (3-4 per path)
- Duration: Standard
- Rest: Standard

**Resolution Date:** 2026-01-06

---

### MA-D12: Aggressive intensity profile — **LOCKED: 4-5x/week, high intensity**

**Decision:** Aggressive intensity profile parameters:
- Sessions per week: 4-5
- Sets: High (4-5 per path)
- Duration: Longer
- Rest: Shorter

**Resolution Date:** 2026-01-06

---

## State Transition Summary

Based on locked decisions:

```
Not Started → In Progress: First qualifying session
In Progress → On Track: 3 sessions completed
On Track → Maintained: 6 sessions completed
On Track/Maintained → Needs Review: 14 days without practice
Needs Review → Stale: 30 days without practice
Stale → In Progress: Practice with -2 session penalty
```

---

## References

- CH22 L569-572: Completed set counting
- CH22 L600-609: Qualifying practice day, streak computation
- CH22 L624-629: Credit reservation model
- CH23 L48-56: Mastery states
- CH23 L60-68: Goal types
- CH23 L71-82: Intensity profiles and override rules
- CH23 L148-157: Trust controls
- CH23 L180-183: No gloves-on rating requirement
- CH23 L217-218: MasteryRecord schema
- CH23 L221-226: Orphaned paths
- CH23 L236: Multi-gameplan intensity conflict
- CH23 L247-250: Snooze behavior
- CH23 §Placeholders L255-260: Thresholds and mode placeholders
- CH24 L362-365: Maintenance goal types
- CH24 L401-403: Urgency scoring
- CH24 L428-439: Status framing
- CH24 L458-474: MaintenanceItem schema
- CH24 L467: cadence_model options
- CH24 L515: Timezone shift handling
- CH24 §Placeholders L521-525: Capacity, threshold, cadence placeholders

---

**End of Document — All Mastery Algorithm Decisions LOCKED**

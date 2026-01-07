# Practice Setup Defaults — PRD Extraction

**Date:** 2026-01-06
**Status:** PRD EXTRACTION ONLY — No decisions made
**Related Blockers:** PM-B01, PM-B02, PM-B03, PM-B04

---

## LOCKED (Verbatim PRD)

### Default Configuration Values (CH20 L159-163)

| Parameter | Default Value | Source |
|-----------|---------------|--------|
| Sets per item | 3 | CH20 L160 |
| Rest between sets | 0:30 | CH20 L162 |
| Assumed reps per set | 15 | CH20 L163 |

Source: CH20 L159-163

### Session Plan Default Values (CH20 L232-237)

```
"defaults": {
  "setsPerItem": 3,
  "timePerSetSec": 180,
  "restSec": 30,
  "assumedRepsPerSet": 15
}
```

Note: `timePerSetSec: 180` (3:00) appears in data contract but time per set is marked as PLACEHOLDER in L161.

Source: CH20 L232-237

### Time Per Set Presets (CH20 L168)

Available presets: 1:00 / 2:00 / 3:00 / 5:00

Source: CH20 L168

### Soft Caps — Warnings Only (CH20 L176-179)

| Trigger | Warning Copy | Source |
|---------|--------------|--------|
| >40 drill items | "Big session. Consider splitting." | CH20 L177 |
| >90 min estimated | "Long session. Keep it realistic." | CH20 L178 |
| >50 assumed reps/set | "High reps. Make sure your form stays sharp." | CH20 L179 |

Source: CH20 L176-179

### Reps Language (CH20 L171-175)

- Label: "Assumed reps per set"
- Tooltip: "Handz doesn't count reps. It assumes you hit your target pace."
- Review: "We'll estimate total reps from your settings."

Source: CH20 L171-175

### Backgrounding Behavior (CH21 L418-420)

- App goes to background during RUNNING_SET or RESTING: immediately pause
- Show "Paused due to leaving app" on return
- Background longer than threshold: session saved as Interrupted on next app open
- App crashes: on next launch show "Resume interrupted session?" with Resume or Discard

Source: CH21 L418-420

### Wake Lock Behavior (CH21 L434-436)

- During RUNNING_SET and RESTING: keep screen awake
- Manual screen lock: treat as backgrounding → session pauses

Source: CH21 L434-436

### Timer Behavior (CH21 L392-395)

- Work timer reaches 0: automatically complete set, transition to RESTING
- Rest timer reaches 0: automatically start next work set
- Timer ticks must clamp at 0 (never negative)

Source: CH21 L392-395

### Entitlement Mid-Session (CH21 L448-450)

- If entitlement lost mid-session: allow session to finish; apply restrictions next time
- Credit-based session with 0 credits: block start in CH20, not here

Source: CH21 L448-450

### Demo Flows Cap Exception (CH20 L108)

- Demo Flows: Do not count toward free saved-flow cap

Source: CH20 L108

### Inbox Items Not Selectable (CH20 L107)

- Inbox Items: View-only until saved to Library; NOT selectable by default
- If prefill from inbox, show blocking banner "Save this flow to practice."

Source: CH20 L107

### Credits Consumed at Start (CH20 L193-194)

- Credits consumed at moment user starts (tap Start), not when configuring

Source: CH20 L193-194

### Confirm End Practice (CH21 L403-407)

- Always 2-step: tap End → confirm modal
- Copy: "End practice session? Your progress will be saved as Interrupted."
- Buttons: "Keep Practicing" (default), "End Session"

Source: CH21 L403-407

---

## UNRESOLVED (PRD Options — No Selection Made)

### PM-B01: Time Per Set Default

PRD states this is a placeholder.

| Option | Source |
|--------|--------|
| 3:00 | CH20 L161 |
| 5:00 | CH20 L161 |

PRD Status: PLACEHOLDER (TBD)

Note: Data contract shows `timePerSetSec: 180` (3:00) but this appears to be example data, not a locked default.

Source: CH20 §Placeholders L285 (CH20-P2)

### PM-B02: Demo Flow Practice Allowed

PRD states this is a placeholder.

| Option | Source |
|--------|--------|
| Allow | CH20 L287 |
| Block | CH20 L287 |

PRD Status: PLACEHOLDER (TBD, decide before beta)

- "Demo flow practice: Allow / Block" (CH20 L287)
- "whether practicable is placeholder" (CH20 L108)
- "Optional: 'Try a demo session' → PRACTICE_DEMO_SETUP (placeholder)" (CH20 L98)

Source: CH20 §Placeholders L286-287 (CH20-P3)

### PM-B03: Max Drill Items Per Session

PRD states this is a placeholder owned by CH30/CH12.

| Option | Source |
|--------|--------|
| 100 | CH20 L287 |
| other | CH20 L287 |

PRD Status: PLACEHOLDER (default placeholder: 100)

- "Hard Caps (block for stability): PLACEHOLDER owner CH30/CH12" (CH20 L181)
- "Max drill items per session (default placeholder: 100)" (CH20 L182)

Source: CH20 §Placeholders L287 (CH20-P4), CH20 L181-182

### PM-B04: Background Timeout Before Interrupted

PRD states this is a placeholder.

| Option | Source |
|--------|--------|
| 120 seconds (2 min) | CH21 L485 |
| 300 seconds (5 min) | CH21 L485 |
| 600 seconds (10 min) | CH21 L485 |

PRD Status: PLACEHOLDER (default: 300)

- "PLACEHOLDER: BackgroundTimeoutSeconds (default: 300s / 5min)" (CH21 L419)

Source: CH21 §Placeholders L485 (BackgroundTimeoutSeconds)

### Audio Countdown Default (Last 3s Ticks)

PRD states this is a placeholder.

| Option | Source |
|--------|--------|
| on | CH21 L486 |
| off | CH21 L486 |

PRD Status: PLACEHOLDER (default: off)

- "Optional: last 3 seconds countdown ticks (off by default); PLACEHOLDER: AudioCountdownDefault" (CH21 L430)

Source: CH21 §Placeholders L486 (AudioCountdownDefault)

### Audio Cue Default (Set/Rest Start Sounds)

PRD states this is a placeholder.

| Option | Source |
|--------|--------|
| on | CH21 L487 |
| off | CH21 L487 |

PRD Status: PLACEHOLDER (default: on)

- "Optional: set-start and rest-start short sound (on by default); PLACEHOLDER: AudioCueDefault" (CH21 L431)

Source: CH21 §Placeholders L487 (AudioCueDefault)

### Undo Completed Set Toast

PRD states this is a placeholder.

| Option | Source |
|--------|--------|
| on | CH21 L488 |
| off | CH21 L488 |

PRD Status: PLACEHOLDER (default: on)

- "Undo toast for 3 seconds (v1 recommended)" (CH21 L384)

Source: CH21 §Placeholders L488 (UndoCompletedSetToast)

### Add Set In-Session Control

PRD states this is a placeholder.

| Option | Source |
|--------|--------|
| include | CH21 L489 |
| exclude in V1 | CH21 L489 |

PRD Status: PLACEHOLDER (default: include as +1 only)

- "Add Set: Secondary (More) → +1 extra set (optional) — V1 placeholder" (CH21 L352)

Source: CH21 §Placeholders L489 (AddSetInSession)

### Save-as-Preset for Session Setups

PRD states this is a placeholder.

| Option | Source |
|--------|--------|
| Off in V1 | CH20 L284 |
| Pro-only | CH20 L284 |

PRD Status: PLACEHOLDER (default: Off)

Source: CH20 §Placeholders L284 (CH20-P1)

---

## OPEN QUESTIONS (PRD Silent)

### OQ-1: Max Total Session Plan Size in Memory

PRD mentions this as a hard cap placeholder but provides no options or ranges.

- "Max total session plan size in memory" (CH20 L183)

Source: CH20 L183

### OQ-2: Minimum Values for Configuration

PRD does not specify minimum values for:
- Minimum sets per item
- Minimum time per set
- Minimum rest duration
- Minimum assumed reps per set

Source: None (PRD silent)

### OQ-3: Maximum Values for Configuration

PRD specifies soft cap warnings but not hard maximums for:
- Maximum sets per item (no limit stated)
- Maximum time per set (no limit stated)
- Maximum rest duration (no limit stated)
- Maximum assumed reps per set (soft cap at 50, no hard cap)

Source: CH20 L176-179 (soft caps only)

### OQ-4: Demo Practice Content

PRD marks demo practice as placeholder but does not specify:
- What content is available in demo practice
- Whether demo requires account
- Whether demo sessions are logged

- "Optional: 'Try a demo session' → PRACTICE_DEMO_SETUP (placeholder)" (CH20 L98)

Source: CH20 L98

### OQ-5: Persisting Setup Drafts

PRD states default is "do not persist" but does not specify:
- When/if this behavior changes
- Whether Pro users get different behavior

- "Persisting setup drafts across app restarts: owned by CH28 (default: do not persist)" (CH20 L42)

Source: CH20 L42

### OQ-6: Manual Screen Lock vs Background

PRD states manual screen lock triggers backgrounding but does not specify:
- Whether the same timeout applies
- Whether this differs from app backgrounding

- "Manual screen lock: treat as backgrounding → session pauses" (CH21 L436)

Source: CH21 L436

### OQ-7: Resume Interrupted Session Behavior

PRD describes resume flow but does not specify:
- How long after crash/close the resume option is available
- Whether resume is available across app updates
- What happens if flow was edited between crash and resume

Source: CH21 L420

### OQ-8: Haptic Intensity/Pattern

PRD mentions haptics but does not specify:
- Exact haptic patterns
- Whether haptics are configurable by user

- "light haptic" (CH21 L425-426)
- "warning haptic (rare)" (CH21 L427)

Source: CH21 L424-427

---

## References

- CH20 L42: Setup draft persistence
- CH20 L98: Demo practice placeholder
- CH20 L107-108: Inbox items, demo flows
- CH20 L159-163: Global defaults
- CH20 L168: Time presets
- CH20 L171-175: Reps language
- CH20 L176-179: Soft caps
- CH20 L181-183: Hard caps placeholder
- CH20 L193-194: Credit spend timing
- CH20 L232-237: Session plan data contract defaults
- CH20 §Placeholders L284-287: CH20-P1 through CH20-P4
- CH21 L352: Add Set control
- CH21 L384: Undo toast
- CH21 L392-395: Timer behavior
- CH21 L403-407: Confirm end practice
- CH21 L418-420: Backgrounding behavior
- CH21 L424-427: Haptics
- CH21 L430-431: Audio cue placeholders
- CH21 L434-436: Wake lock
- CH21 L448-450: Entitlement mid-session
- CH21 §Placeholders L485-489: BackgroundTimeoutSeconds through AddSetInSession

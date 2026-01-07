# Decision: Free vs Pro Scope for Mastery & Maintenance

**Date:** 2026-01-06  
**Status:** FINAL — ALL DECISIONS LOCKED  
**Mode:** No assumptions, no decisions, no invented values  
**Resolves:** PM-E11, PM-E12, PM-E13, PM-E14, PM-B08, PM-C03, PM-C04

---

## Source Ambiguity

The PRD explicitly marks Free vs Pro scope for Mastery/Maintenance features as **TBD**:

| Source | Statement |
|--------|-----------|
| CH23 L258 | "Mastery planning Pro-only vs Free-limited — owned by CH08/CH25" |
| CH24 L525 | "Pro gating for autoplan/reminders — autoplan for all, autoplan Pro-only, reminders Pro-only" |
| CH23 L193-196 | "Mastery visibility vs Mastery action (suggested default, subject to CH08/CH25)" |

**PRD does NOT resolve which model to adopt.** Owner must decide.

---

## LOCKED (Verbatim PRD Only)

### Practice Entitlements by Plan — CH08 §4.4 (L421-428)
| Capability | Guest | Free | Pro/Trial | Source |
|------------|-------|------|-----------|--------|
| Practice saved flows | No | Yes (credits only) | Yes (unlimited) | CH08 L424 |
| Practice inbox items | No | No | Yes | CH08 L425 |
| Monthly practice credits | N/A | 3/month | N/A | CH08 L426 |

### Free Caps — CH08 §3.2 (L379-381)
| Cap | Value | Source |
|-----|-------|--------|
| Free saved flows cap | 2 | CH08 L379 (LOCKED) |
| Free inbox cap | 10 | CH08 L381 (LOCKED) |

### Gameplan Definition — CH23 L27-36
| Aspect | PRD Statement | Source |
|--------|---------------|--------|
| Definition | Named bundle of one or more Paths selected by user | CH23 L28 |
| Cross-flow | May include paths from multiple flows | CH23 L29 |
| Composition | Can include: (a) multiple individual paths, (b) entire flow, (c) saved filter-set that expands into paths | CH23 L33 |
| Editing | Users can rename any Gameplan at any time; add/remove paths, reorder drill priority, change goal type | CH23 L35-36 |

### Mastery Definition — CH23 L38-46
| Aspect | PRD Statement | Source |
|--------|---------------|--------|
| Definition | App-level model of how "locked in" a path is for user | CH23 L39 |
| Basis | Based on logged practice and time since last reinforcement | CH23 L40 |
| Disclaimer | Explicitly NOT a guarantee of fight performance | CH23 L41 |
| Purpose | A planning and memory-maintenance proxy | CH23 L42 |

### Mastery States — CH23 L48-56
| State | Definition | Source |
|-------|------------|--------|
| Not Started | Never practiced in Practice Mode (or no qualifying logs) | CH23 L50 |
| In Progress | Actively training; plan has remaining sessions before target readiness | CH23 L51 |
| On Track | Meeting recommended cadence; forgetting risk is low | CH23 L52 |
| Needs Review | Drifting; forgetting risk rising; maintenance recommended | CH23 L53 |
| Maintained | Completed initial plan and maintained at defined cadence | CH23 L54 |
| Stale | Not reinforced for long period; treat as partially forgotten | CH23 L55 |
| User-Adjusted | Flag indicating user manually adjusted mastery up/down | CH23 L56 |

### Mastery Hub Required Modules — CH23 L87-91
| Module | Purpose | Source |
|--------|---------|--------|
| Top summary strip | Counts (Gameplans Active, Paths On Track, Paths Needing Review, Paths Stale); streak/cadence indicators secondary | CH23 L88 |
| Next Up card | Single most important recommended action; Buttons: [Start Practice], [View Gameplan], [Snooze] | CH23 L89 |
| Due for Review list | Sortable by path or grouped by gameplan; shows Path name, Source flow name, Status badge, Last practiced date, "Why" tooltip | CH23 L90 |
| Active Gameplans list | Cards with name, goal type, percent progress, quick actions [Practice], [Edit], [View] | CH23 L91 |

### Maintenance Definition — CH24 L275-280
| Term | Definition | Source |
|------|------------|--------|
| Maintenance Item | Maintainable unit derived from path or gameplan subset, scheduled for periodic drill | CH24 L276 |
| Due | Maintenance item recommended to be drilled within a time window | CH24 L277 |
| Backlog | Overdue maintenance items not yet drilled | CH24 L278 |
| Capacity | User-selected daily/weekly limit that prevents overload | CH24 L279 |
| Snooze | User action to push maintenance item to later without losing progress framing | CH24 L280 |

### Maintenance Hub Layout — CH24 L317-329
| Section | Content | Source |
|---------|---------|--------|
| Header | "Maintenance" + subtext (date + status e.g., "2 due today - 1 overdue") | CH24 L322 |
| Primary CTA | "Start Today's Maintenance" (bundled session) OR "Browse Recommendations" | CH24 L323 |
| Status pill | "On Track" / "Behind" / "Paused" | CH24 L324 |
| Due Today list | 0-N items | CH24 L327 |
| Overdue list | Collapsed by default if empty | CH24 L328 |
| Recommended list | Always present; personalization-driven | CH24 L329 |

### Maintenance Session Entitlement Rules — CH24 L417-421
| Plan | PRD Statement | Source |
|------|---------------|--------|
| Guest | Can browse maintenance screens as demo; creating plan or starting session prompts account creation | CH24 L419 |
| Free | Starting session consumes practice credit (if available); allowed only for saved flows (not inbox); if no credits, show paywall | CH24 L420 |
| Pro/Trial | Unlimited maintenance sessions; enforce anti-abuse/safety limits (CH30) | CH24 L421 |

### No-Shame Copy Rules — CH24 L436-439
| Rule | PRD Statement | Source |
|------|---------------|--------|
| Never say | "You failed", "You broke your streak" | CH24 L437 |
| Prefer | "Pick up where you left off", "Resume", "You're one session away from being back on track" | CH24 L438 |
| Metrics | Use neutral metrics: "items completed" and "items due"; streaks optional and secondary | CH24 L439 |

### Trial Behavior — CH08 §6 (L463)
| Rule | PRD Statement | Source |
|------|---------------|--------|
| Trial = Pro | During trial, all Pro gates are removed (Practice, uploads, etc.) | CH08 L463 |

### Downgrade Behavior — CH08 §6 (L466)
| Rule | PRD Statement | Source |
|------|---------------|--------|
| Data preservation | Do NOT delete data; enforce cap by making flows beyond cap read-only | CH08 L466 |
| UX owner | CH15/CH25 | CH08 L466 |

---

## Blocking Decisions — ALL LOCKED

| ID | Decision | Options | Resolution | Status |
|----|----------|---------|------------|--------|
| MS-D01 | Free can create Gameplans | Yes unlimited / Yes with cap / No | **Yes, unlimited** | **LOCKED** |
| MS-D02 | Free can view Mastery Hub | Full / View-only / Hidden | **Full access** | **LOCKED** |
| MS-D03 | Free can view Maintenance Hub | Full / View-only / Hidden | **Full access** | **LOCKED** |

---

## LOCKED DECISIONS

### MS-D01: Free can create Gameplans — **LOCKED: Yes, unlimited**

**Decision:** Free users can create unlimited Gameplans.

**Resolution Date:** 2026-01-06

**Rationale:** Allow Free users to experience the full planning value. Conversion is driven by execution gates (credits required to start sessions).

**Implications:**
- Free users can create, name, and organize Gameplans without limit
- Starting practice sessions within Gameplans requires credits
- Downgraded users retain all Gameplans (per DG-D07)
- Consistent with "show value, gate execution" philosophy

---

### MS-D02: Free can view Mastery Hub — **LOCKED: Full access**

**Decision:** Free users have full access to view the Mastery Hub (all modules visible), with clear Pro gating language on execution actions.

**Resolution Date:** 2026-01-06

**Rationale:** Show value, gate execution. Free users see mastery states for paths they've practiced, driving conversion when they want to act on recommendations.

**Implications:**
- Free sees all Mastery Hub modules: summary strip, Next Up card, Due for Review list, Active Gameplans list
- "Start Practice" buttons show credit cost or paywall if 0 credits
- Pro features clearly labeled with Pro badge
- Consistent with PRD suggestion (CH23 L194: "Free can view due-for-review list")

---

### MS-D03: Free can view Maintenance Hub — **LOCKED: Full access**

**Decision:** Free users have full access to view the Maintenance Hub (all sections visible), clearly labeled as Pro-powered. Practice actions gated by credits/plan.

**Resolution Date:** 2026-01-06

**Rationale:** Consistent with MS-D02. Show the value of maintenance tracking, gate the execution.

**Implications:**
- Free sees all Maintenance Hub sections: header, status pill, Due Today, Overdue, Recommended lists
- "Start Maintenance" and practice CTAs require credits or show paywall
- Pro-powered label visible on hub
- Maintains consistency with Mastery Hub access model

---

## PRD References

- CH08 §3.2 (L379-381): Free caps
- CH08 §4.4 (L421-428): Practice entitlements by plan
- CH08 §6 (L463-466): Trial and downgrade behavior
- CH23 L27-46: Gameplan and Mastery definitions
- CH23 L48-56: Mastery states
- CH23 L61-78: Goal types and intensity profiles
- CH23 L87-91: Mastery Hub modules
- CH23 L148-162: Trust controls and manual adjustments
- CH23 L185-202: Entitlements & funnel
- CH23 L206-218: Data model
- CH23 L252-260: Placeholders
- CH24 L275-280: Maintenance definitions
- CH24 L317-329: Maintenance Hub layout
- CH24 L417-421: Maintenance session entitlements
- CH24 L436-439: No-shame copy rules
- CH24 L518-525: Placeholders
- CH25 L635-641: Practice credits behavior

---

**End of Document — All Mastery Scope Decisions LOCKED**

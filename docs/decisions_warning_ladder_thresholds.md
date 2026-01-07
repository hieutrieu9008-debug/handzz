# Decision: Warning Ladder Thresholds

**Date:** 2026-01-06  
**Status:** FINAL — ALL DECISIONS LOCKED  
**Mode:** No assumptions, no decisions, no invented values  
**Resolves:** ME-WL01, ME-WL02, ME-WL03, ME-WL04, ME-WL05, related warning ladder blockers

---

## LOCKED (Verbatim PRD Only)

### Threat Model — CH30 (L747-758)

**We protect against (CH30 L747-753):**
| Threat | Description | Source |
|--------|-------------|--------|
| Resource abuse | Automated/excessive creation of share links, imports, flows, or edits | CH30 L748 |
| Spam distribution | Mass generation of unlisted share links | CH30 L749 |
| Storage abuse | Attempts to exceed media/storage caps or upload/delete cycling | CH30 L750 |
| Account farming | Many guest sessions or new accounts from one device/network to bypass caps | CH30 L751 |
| Denial of service vectors | Repeated heavy operations (exporting, importing, huge flows) | CH30 L752 |
| User self-harm via UX | Confusing caps causing lost work or trapped feeling; punishing messages causing churn | CH30 L753 |

**We do NOT protect against (V1) (CH30 L755-758):**
| Threat | Reason | Source |
|--------|--------|--------|
| Public feed toxicity | No public feed in V1 | CH30 L756 |
| DM harassment | No DMs in V1 | CH30 L757 |
| Copyright enforcement on user-linked videos | Out of scope | CH30 L758 |

### Global Principles (Non-Negotiable) — CH30 (L762-768)
| Principle | Specification | Source |
|-----------|---------------|--------|
| Soft caps by default | Limits feel like guardrails, not punishments; warn early, only block when necessary | CH30 L763 |
| No silent failure | When blocked or degraded, show why, what to do next, and how to recover | CH30 L764 |
| Don't destroy work | If user hits cap, preserve drafts and offer clear choices (upgrade, delete, export, later) | CH30 L765 |
| Plan-aware but respectful | Free usable (explore/build basics); Pro smooth but not unlimited | CH30 L766 |
| Reversible states | Provide "revert" path when safety action changes user state | CH30 L767 |
| Transparent counting | Show users what is counted toward cap and what is not | CH30 L768 |

### Warning Ladder Level Definitions — CH30 (L770-804)

#### L0 — Normal (CH30 L772-774)
| Aspect | Specification | Source |
|--------|---------------|--------|
| Warnings | No warnings; user actions proceed normally | CH30 L773 |
| Counting | Light background counting; all counters tracked for potential escalation | CH30 L774 |

#### L1 — Informational Nudge (CH30 L776-780)
| Aspect | Specification | Source |
|--------|---------------|--------|
| Trigger | Early, friendly notice approaching soft cap | CH30 L777 |
| Surface | Non-blocking toast or inline banner | CH30 L778 |
| Content | Provides: current usage + cap + how it resets | CH30 L779 |
| Impact | No slowdown; user can continue | CH30 L780 |

#### L2 — Soft Warning + Friction (CH30 L782-786)
| Aspect | Specification | Source |
|--------|---------------|--------|
| Trigger | User crosses soft cap or repeats risky action quickly | CH30 L783 |
| Surface | Inline warning card + optional cooldown (seconds/minutes) | CH30 L784 |
| Friction | Optional confirmation step for repetitive actions | CH30 L785 |
| Completion | Still allows completion for legitimate users | CH30 L786 |

#### L3 — Temporary Cooldown / Partial Block (CH30 L788-792)
| Aspect | Specification | Source |
|--------|---------------|--------|
| Trigger | Clear misuse pattern or rapid repetition | CH30 L789 |
| Behavior | Action blocked for time window OR reduced rate | CH30 L790 |
| Time examples | e.g., 15-60 minutes OR e.g., 1 link/hour | CH30 L790 |
| UX | User sees countdown + reason + recovery options | CH30 L791 |
| Scope | Other parts of app remain usable | CH30 L792 |

#### L4 — Feature Suspension (CH30 L794-798)
| Aspect | Specification | Source |
|--------|---------------|--------|
| Trigger | Severe or repeated abuse | CH30 L795 |
| Behavior | Suspend feature category (e.g., share-link creation or media upload) | CH30 L796 |
| Duration | 24-72 hours | CH30 L796 |
| Access | User can still view own content | CH30 L797 |
| UX | Clear message + link to support + review steps | CH30 L798 |

#### L5 — Account Security Block (CH30 L800-804)
| Aspect | Specification | Source |
|--------|---------------|--------|
| Trigger | High-confidence automation/fraud signals or repeated L4 | CH30 L801 |
| Behavior | Block high-risk actions (and possibly login) until verification/support | CH30 L802 |
| UX | Show security screen with next steps | CH30 L803 |
| Logging | Audit log entry is mandatory | CH30 L804 |

### Ladder Escalation Rules — CH30 (L806-810)
| Rule | Specification | Source |
|------|---------------|--------|
| Per-vector | Escalation is per-vector (e.g., L3 for links but L0 elsewhere) | CH30 L807 |
| De-escalation | Happens automatically after cooldown windows unless signals persist | CH30 L808 |
| Escalation requires | At least one of: rate threshold exceeded, repeated failures to comply, suspicious signal | CH30 L809 |
| Logging | Always log: vector, level, timestamp, device/session identifier, action counts, plan state | CH30 L810 |

### Core Limits (Enforced by CH30, Owned Elsewhere) — CH30 (L814-821)
| Vector | Guest | Free | Pro/Trial | Owner | Source |
|--------|-------|------|-----------|-------|--------|
| Save flows to library | Blocked | Max 2 | Higher/uncapped (placeholder) | CH07, CH08, CH15 | CH30 L817 |
| Inbox capacity | N/A | Max 10 | Higher/uncapped (placeholder) | CH18 | CH30 L818 |
| Practice sessions | Blocked | 3 monthly credits on saved flows only | Unlimited (fair-use) | CH20-CH22, CH25 | CH30 L819 |
| Outgoing branches per move | N/A | Max 10 | Max 10 (V1) | CH12 | CH30 L820 |
| Video uploads | Blocked | Blocked | Allowed; 2GB total cap | CH29 | CH30 L821 |

### Abuse-Sensitive Vectors (Owned by CH30) — CH30 (L823-830)
| Vector | Abuse Risk | Ladder Behavior | Plan Notes | Source |
|--------|------------|-----------------|------------|--------|
| Share-link creation | Spam distribution & backend load | L1: approaching daily cap. L2: confirmation + cooldown. L3: temporary block. L4: 24-72h suspension. L5: security block | Free/Pro caps are placeholders; owned by CH17/CH25 | CH30 L827 |
| Import acceptance | Bypass flow limits or load app | Free: inbox cap 10 + 2-flow cap. Burst imports trigger L2 confirm + cooldown; L3 if continued | Keep sharing funnel alive | CH30 L828 |
| High-frequency edits | Backend stress from scripts | Server rate-limit; show L2 when hit; L3 cooldown if continued | Thresholds are placeholders | CH30 L829 |
| Video upload churn (Pro) | Storage probing | L1 at ~80% storage. L2 at ~95%. L3 cooldown on repeated failed attempts. L4 upload suspension | Storage cap owned by CH29 | CH30 L830 |

### Required UX Behaviors at Caps — CH30 (L832-854)

**Free hits saved-flow cap (2) — CH30 L834-837:**
| Behavior | Specification | Source |
|----------|---------------|--------|
| Existing flows | Allow viewing/editing existing saved flows | CH30 L835 |
| New flows | Block saving additional flows | CH30 L836 |
| Modal content | Upgrade to Pro, Delete a saved flow, Cancel (plus Export/Copy if CH34 supports) | CH30 L837 |

**Free tries to practice — CH30 L839-842:**
| Scenario | Behavior | Source |
|----------|----------|--------|
| Credits remaining + saved flow | Allow | CH30 L840 |
| Credits = 0 | Show paywall (CH25) | CH30 L841 |
| Flow from inbox (not saved) | Block and explain 'Practice requires a saved flow' + CTA to save | CH30 L842 |

**Free inbox full (10) — CH30 L844-846:**
| Behavior | Specification | Source |
|----------|---------------|--------|
| Trigger | Receiving new imports triggers 'Inbox full' screen | CH30 L845 |
| Options | Delete inbox items, Save one to library (if under cap), Upgrade to Pro | CH30 L846 |

**Guest tries to save — CH30 L848-849:**
| Behavior | Specification | Source |
|----------|---------------|--------|
| Modal | Show conversion wall with Apple/Google/Email options (CH07) + Cancel | CH30 L849 |

**Pro approaches 2GB storage — CH30 L851-854:**
| Behavior | Specification | Source |
|----------|---------------|--------|
| UI | Show storage meter on upload UI | CH30 L852 |
| L1 trigger | Banner at ~80% | CH30 L853 |
| Hard block | At 100% | CH30 L853 |
| Actions | Delete uploads or use links | CH30 L854 |

### Implementation Rules — CH30 (L856-876)

**Single Source of Truth — CH30 L858-860:**
| Rule | Specification | Source |
|------|---------------|--------|
| Config location | All caps/thresholds in single configuration object (server-delivered if possible) | CH30 L859 |
| Enforcement | Client reads config for counters/messaging; server enforces same config | CH30 L860 |

**Required Config Fields (V1) — CH30 L862-867:**
| Field | Content | Source |
|-------|---------|--------|
| planStates | guest/free/pro/trial | CH30 L863 |
| caps | savedFlowsFree=2; inboxFree=10; practiceCreditsFreeMonthly=3; storageProBytes=2GB | CH30 L864 |
| ladder | per-vector thresholds; cooldown durations; suspension durations | CH30 L865 |
| copyKeys | mapping from vector+level to copy template keys | CH30 L866 |
| resets | monthly credit reset; daily link reset; rolling-window counts | CH30 L867 |

**Suspicious-Activity Signals (V1 Minimal) — CH30 L869-875:**
| Signal | Source |
|--------|--------|
| Very high rate actions beyond plausible human use | CH30 L870 |
| Repeated failed attempts to perform blocked actions | CH30 L871 |
| Many new accounts from same device/session in short window | CH30 L872 |
| High-frequency import acceptance with immediate delete cycles | CH30 L873 |
| Unusually large flows or repeated attempts to create extremely large graphs | CH30 L874 |

**Important Guidance — CH30 L876:**
> Treat signals as probabilistic; prefer cooldowns over permanent bans; avoid false positives for real coaches building big gameplans.

### Telemetry Requirements — CH30 (L885-891)
| Event | Source |
|-------|--------|
| limit_counter_incremented (vector, plan, count, window) | CH30 L886 |
| ladder_level_shown (vector, level, plan) | CH30 L887 |
| action_blocked (vector, reason, plan, level) | CH30 L888 |
| cooldown_started / cooldown_ended | CH30 L889 |
| upgrade_cta_clicked (from which limit screen) | CH30 L890 |
| user_deleted_item_to_resolve_limit (flow/inbox/media) | CH30 L891 |

### UX Copy Rules — CH30 (L893-925)

**Mandatory Copy Components — CH30 L895-900:**
| Component | Example | Source |
|-----------|---------|--------|
| What happened | 'You've reached your Free plan limit for saved flows.' | CH30 L896 |
| Why (1 sentence max) | 'This keeps Handz fast and prevents spam.' | CH30 L897 |
| What you can do next | 'Delete one flow, upgrade, or come back later.' | CH30 L898 |
| How counting works | Show count and limit: '2 of 2 saved.' | CH30 L899 |
| Reset info (when applicable) | 'Credits refresh monthly.' 'Link limits reset daily.' | CH30 L900 |

**Reusable Templates — CH30 L902-911:**
| Template | Copy | Source |
|----------|------|--------|
| Approaching soft cap (L1) | Heads up — you're close to the limit for {THING} ({COUNT} of {LIMIT}). | CH30 L905 |
| Soft warning (L2) | You're doing this a lot quickly. To keep Handz fast, we'll add a short cooldown ({COOLDOWN}). | CH30 L906 |
| Cooldown (L3) | Temporarily paused: {THING}. Try again in {TIME_REMAINING}. | CH30 L907 |
| Feature suspension (L4) | {THING} is suspended for {DURATION} due to unusual activity. If this is a mistake, contact support. | CH30 L908 |
| Guest save wall | Create an account to save your work. It takes under a minute. | CH30 L909 |
| Practice paywall (credits 0) | Practice is a Pro feature. You've used your monthly credits. Start a 7-day free trial to keep drilling. | CH30 L910 |
| Inbox full (Free) | Your inbox is full ({COUNT} of {LIMIT}). Delete items or save one to your library. | CH30 L911 |

**UX Surfaces — CH30 L913-918:**
| Surface | Usage | Source |
|---------|-------|--------|
| Toast | L1 informational (non-blocking) | CH30 L914 |
| Inline banner | L1-L2 | CH30 L915 |
| Blocking modal | When action cannot proceed (cap reached) — must include next actions | CH30 L916 |
| Dedicated 'Limit' screen | L3-L5, with countdown and support link | CH30 L917 |
| Settings > Plan | Shows current counters and reset dates | CH30 L918 |

### Accessibility & Fairness — CH30 (L920-925)
| Rule | Specification | Source |
|------|---------------|--------|
| Color | Do not rely on color alone to communicate severity (use icons + text) | CH30 L921 |
| Timers | Cooldown timers readable by screen readers and support dynamic type | CH30 L922 |
| Glove-on users | Do not show non-critical safety prompts mid-practice timer; queue for session end | CH30 L923 |
| Dismissal | Allow dismissal for L1/L2 banners but keep path to re-open details | CH30 L924 |
| Language | Explain plan gating without shaming: 'Free plan includes 2 saved flows' instead of 'You're not allowed' | CH30 L925 |

### Login Throttling — CH07/CH30 Reference
| Rule | Specification | Source |
|------|---------------|--------|
| Throttle login attempts | To reduce brute force (see CH30 for escalation messaging) | CH07 L305, CH08 L552 |
| Threshold | Thresholds owned by CH30 | CH08 L553 |

### Export Rate Limits — CH34 (L871)
| Limit | Value | Source |
|-------|-------|--------|
| Soft limit | 3/day | CH34 L871 |
| Hard limit | 10/day | CH34 L871 |
| Status | PLACEHOLDER — "After CH33 metrics plan" | CH34 L871 |

---

## Blocking Decisions — ALL LOCKED

| ID | Decision | Resolution | Status |
|----|----------|------------|--------|
| WL-D01 | Ladder structure | **Progressive escalation (L0 → L5)** | **LOCKED** |
| WL-D02 | Cooldown escalation | **Linear: 15 → 30 → 45 minutes** | **LOCKED** |
| WL-D03 | Cooldown reset window | **48 hours** | **LOCKED** |
| WL-D04 | Cooldown trigger | **3 rapid attempts after L2** | **LOCKED** |
| WL-D05 | Temporary suspension duration | **24 hours** | **LOCKED** |
| WL-D06 | Suspension trigger | **5 escalations within 7 days** | **LOCKED** |
| WL-D07 | Share creation burst limit | **10 per minute** | **LOCKED** |
| WL-D08 | Share open rate limit | **100 per minute per IP/device** | **LOCKED** |
| WL-D09 | Import L1 threshold | **8 imports per hour** | **LOCKED** |
| WL-D10 | Import L2 threshold | **15 imports/hour → confirmation required** | **LOCKED** |
| WL-D11 | Import L3 threshold | **30 imports/hour → 30-minute cooldown** | **LOCKED** |
| WL-D12 | Import L4 suspension | **24-hour suspension** | **LOCKED** |
| WL-D13 | Abuse philosophy | **Prefer cooldowns over permanent bans** | **LOCKED** |

---

## LOCKED DECISIONS

### WL-D01: Ladder structure — **LOCKED: Progressive escalation (L0 → L5)**

**Decision:** Warning ladder uses progressive escalation from L0 (Normal) through L5 (Account Security Block).

**Resolution Date:** 2026-01-06

**Implications:**
- Each level increases friction/blocking proportionally
- Per-vector escalation (one vector can be at L3 while others at L0)
- Consistent with PRD CH30 ladder definitions

---

### WL-D02: Cooldown escalation — **LOCKED: Linear: 15 → 30 → 45 minutes**

**Decision:** L3 cooldown durations escalate linearly: 15 minutes (first), 30 minutes (second), 45 minutes (third+).

**Resolution Date:** 2026-01-06

**Implications:**
- Predictable escalation for users
- Capped at 45 minutes for L3 (L4 is 24h suspension)
- User sees countdown timer with reason

---

### WL-D03: Cooldown reset window — **LOCKED: 48 hours**

**Decision:** Cooldown escalation state resets to baseline after 48 hours of clean behavior.

**Resolution Date:** 2026-01-06

**Implications:**
- User who triggered L3 cooldown returns to L0 after 48h without violations
- Prevents permanent penalty for one-time burst
- Balances recidivism detection with forgiveness

---

### WL-D04: Cooldown trigger — **LOCKED: 3 rapid attempts after L2**

**Decision:** L3 cooldown triggers after 3 rapid attempts following L2 warning.

**Resolution Date:** 2026-01-06

**Implications:**
- Users get 3 chances after L2 friction before temporary block
- "Rapid" = within the same session/short window
- Prevents accidental L3 from legitimate bursts

---

### WL-D05: Temporary suspension duration — **LOCKED: 24 hours**

**Decision:** L4 feature suspension lasts 24 hours.

**Resolution Date:** 2026-01-06

**Implications:**
- Shorter than PRD max (72h) — starts conservative
- User sees clear message with support link
- Can still view own content during suspension

---

### WL-D06: Suspension trigger — **LOCKED: 5 escalations within 7 days**

**Decision:** L4 suspension triggers after 5 escalations (L3 cooldowns) within a rolling 7-day window.

**Resolution Date:** 2026-01-06

**Implications:**
- Requires persistent pattern, not one-time abuse
- Rolling window prevents gaming by waiting
- Clear threshold for support escalation

---

### WL-D07: Share creation burst limit — **LOCKED: 10 per minute**

**Decision:** Share link creation is burst-limited to 10 per minute before L2 friction triggers.

**Resolution Date:** 2026-01-06

**Implications:**
- Allows rapid legitimate sharing (coach sharing to multiple students)
- Exceeding triggers confirmation + short cooldown
- Separate from daily cap (SL-D01)

---

### WL-D08: Share open rate limit — **LOCKED: 100 per minute per IP/device**

**Decision:** Share link opens are rate-limited to 100 per minute per IP/device.

**Resolution Date:** 2026-01-06

**Implications:**
- Prevents scraping/enumeration attacks
- Generous for legitimate classroom scenarios
- Server-side enforcement; client shows error on exceed

---

### WL-D09: Import L1 threshold — **LOCKED: 8 imports per hour**

**Decision:** L1 informational nudge triggers at 8 imports per rolling hour.

**Resolution Date:** 2026-01-06

**Implications:**
- Non-blocking toast: "You've imported 8 flows this hour"
- Gives user awareness before friction
- Keeps sharing funnel alive

---

### WL-D10: Import L2 threshold — **LOCKED: 15 imports/hour → confirmation required**

**Decision:** L2 friction triggers at 15 imports per hour, requiring confirmation for each additional import.

**Resolution Date:** 2026-01-06

**Implications:**
- User sees: "You're importing a lot quickly. Confirm to continue."
- Still allows completion for legitimate users
- Slows automated abuse

---

### WL-D11: Import L3 threshold — **LOCKED: 30 imports/hour → 30-minute cooldown**

**Decision:** L3 temporary block triggers at 30 imports per hour, with 30-minute cooldown.

**Resolution Date:** 2026-01-06

**Implications:**
- Import blocked for 30 minutes
- User sees countdown + reason + recovery options
- Other app functions remain usable

---

### WL-D12: Import L4 suspension — **LOCKED: 24-hour suspension**

**Decision:** Repeated L3 import violations (per WL-D06) trigger 24-hour import suspension.

**Resolution Date:** 2026-01-06

**Implications:**
- User cannot import for 24 hours
- Can still view existing inbox and library
- Clear message with support link

---

### WL-D13: Abuse philosophy — **LOCKED: Prefer cooldowns over permanent bans**

**Decision:** System prefers temporary cooldowns over permanent bans. Treat signals as probabilistic; avoid false positives for real coaches.

**Resolution Date:** 2026-01-06

**Implications:**
- No permanent bans in automated system (L5 requires manual review)
- Err on side of letting legitimate power users continue
- Support path always available

---

## PRD References

- CH07 AT-07-06: Login throttle reference
- CH08 L552-553: Login throttle ownership
- CH17 L195-200: Share link rate limits reference
- CH30 L730-931: Full CH30 chapter
- CH34 L871: Export rate limits

---

**End of Document — All Warning Ladder Decisions LOCKED**

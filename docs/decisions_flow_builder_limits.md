# Flow Builder Limits — PRD Extraction

**Date:** 2026-01-06
**Status:** PRD EXTRACTION ONLY — No decisions made
**Related Blockers:** FB-B01, FB-E05

---

## LOCKED (Verbatim PRD)

### Max Outgoing Branches Per Node

**Hard cap: Max 10 outgoing edges per node**

- "Branching UX up to 10" (CH12 L17-18)
- "Add Branch (Up to 10)" (CH12 L123)
- "Hard cap: Max 10 outgoing edges per node (LOCKED)" (CH12 L126)
- "Attempting 11th → blocked with message: 'Max 10 branches from a move in V1.'" (CH12 L127)
- "Max 10 outgoing edges per Move Node" (CH13 L278)
- "Attempting 11th → CH30 Warning Ladder (soft caps/upgrade prompts)" (CH13 L295)

Source: CH12 L126-127, CH13 L278, CH13 L295, CH30 L820

### Branch Cap Applies to All Plans

| Plan | Outgoing branches per move |
|------|---------------------------|
| Guest | N/A |
| Free | Max 10 |
| Pro/Trial | Max 10 (V1) |

Source: CH30 L820

### Free Saved Flows Cap

- "2 saved flows max (LOCKED)" (CH12 L86-87)
- "Free cap = 2 flows (locked)" (CH29 L606)
- "Save flows to library: Free = Max 2" (CH30 L817)

Source: CH12 L86, CH29 L606, CH30 L817

### Pro Saved Flows Cap

- "Pro: Higher/uncapped (placeholder)" (CH30 L817)

Source: CH30 L817

### Demo Flows Exception

- "isDemo: bool — Demo flows don't count toward caps" (CH13 L315)

Source: CH13 L315

### Merging: Unlimited Incoming Edges

- "Merging: 0–N incoming edges (unlimited)" (CH13 L279)

Source: CH13 L279

### Cycle Policy

- "V1: Cycles disallowed (no node reachable from itself)" (CH13 L285)
- "Prevents infinite practice loops; simplifies path enumeration" (CH13 L285)
- "Self-loops forbidden: sourceNodeId != targetNodeId" (CH13 L289)

Source: CH13 L285, L289

### Dangling Paths Allowed

- "Dangling paths: leaf nodes allowed; Draft flows may contain Placeholder Nodes" (CH13 L280)

Source: CH13 L280

---

## UNRESOLVED (PRD Options — No Selection Made)

### FB-B01: Max Nodes Per Flow Per Plan

PRD states this is a placeholder owned by CH30/CH29.

- "Max nodes/edges per flow per plan → Owner: CH30/CH29" (CH13 §Open Questions L254)

No options or ranges specified in PRD.

Source: CH13 L254

### FB-B01: Max Edges Per Flow Per Plan

PRD states this is a placeholder owned by CH30/CH29.

- "Max nodes/edges per flow per plan → Owner: CH30/CH29" (CH13 §Open Questions L254)

No options or ranges specified in PRD.

Source: CH13 L254

### Pro Saved Flow Limit

PRD states this is a placeholder.

- "Higher/uncapped (placeholder)" (CH30 L817)

No options or ranges specified in PRD.

Source: CH30 L817

### Zoom Levels

PRD states these are placeholders with recommendations only.

- "Minimum: PLACEHOLDER (recommend 50%)" (CH12 L52)
- "Maximum: PLACEHOLDER (recommend 200–250%)" (CH12 L53)

| Parameter | PRD Status | PRD Recommendation |
|-----------|------------|-------------------|
| Min zoom | PLACEHOLDER | 50% |
| Max zoom | PLACEHOLDER | 200-250% |
| Default zoom | Stated | 100% |

Source: CH12 L51-53

### Autosave Interval

PRD states this is a placeholder.

- "After inactivity (PLACEHOLDER interval) and on app background" (CH12 L89)

No options or ranges specified in PRD.

Source: CH12 L89

### Manual Positions Behavior on Orientation Change

PRD states this is a placeholder.

- "Manual positions behavior: PLACEHOLDER" (CH12 L43)

Source: CH12 L43

---

## OPEN QUESTIONS (PRD Silent)

### OQ-1: Performance Threshold (Node Count)

PRD provides a recommendation only, not a specification:

- "Smooth interaction: ~150 nodes" (CH12 L208)
- "Degrade gracefully: simplify edge rendering, reduce effects" (CH12 L209)
- "Performance thresholds" listed as Open Question (CH12 L24)

PRD does not specify:
- Is ~150 a hard limit or soft target?
- What specific degradation behaviors apply at what thresholds?
- Are there different thresholds for different device capabilities?

Source: CH12 L24, L207-209

### OQ-2: Performance Threshold (Edge Count)

PRD does not specify edge count performance thresholds.

- Only node count (~150) is mentioned as performance target

Source: CH12 L207-209

### OQ-3: Large Flow Handling UX

PRD states "Degrade gracefully" but does not specify:
- What UI/feedback is shown when performance degrades?
- Is the user warned before creating a flow that may cause performance issues?
- Are there soft caps that trigger warnings?

Source: CH12 L209

### OQ-4: Unusually Large Flows Signal

PRD mentions "Unusually large flows" as a suspicious activity signal but does not specify:
- What constitutes "unusually large"?
- What threshold triggers this signal?

- "Unusually large flows or repeated attempts to create extremely large graphs" (CH30 L874)

Source: CH30 L874

### OQ-5: Soft Cycles for Drill Loops

PRD marks this as a placeholder owned by Practice chapters.

- "'Soft cycles' for drill loops: PLACEHOLDER (Practice chapters)" (CH13 L286)
- "Soft cycles for drill loops → Owner: CH20/CH21" (CH13 §Open Questions L255)

Source: CH13 L255, L286

### OQ-6: Edge Count Limits Per Flow

PRD does not specify whether there is a max edge count per flow separate from the per-node branch limit.

- Per-node limit (10) is specified
- Total edges per flow is not specified

Source: None (PRD silent)

### OQ-7: Node Count Limits Per Flow

PRD does not specify whether there is a max node count per flow.

- Marked as placeholder "Max nodes/edges per flow per plan" (CH13 L254)
- No options, ranges, or recommendations provided

Source: CH13 L254 (placeholder only)

### OQ-8: Plan-Based Node/Edge Differences

PRD does not specify whether Free and Pro have different node/edge limits.

- Only states "per plan" as part of placeholder description
- Branch cap (10) is same for Free and Pro

Source: CH13 L254

### OQ-9: High-Frequency Edit Rate Limits

PRD states thresholds are placeholders.

- "Edit-rate limit thresholds allowing 2-hour build sessions" (CH30 L880)
- "Thresholds are placeholders" (CH30 L829)

Source: CH30 L829, L880

---

## References

- CH12 L17-18: Branching UX up to 10
- CH12 L24: Performance thresholds listed as Open Question
- CH12 L43: Manual positions placeholder
- CH12 L51-53: Zoom level placeholders
- CH12 L86-87: Free saved flows cap (LOCKED)
- CH12 L89: Autosave interval placeholder
- CH12 L123-128: Add Branch (Up to 10), hard cap
- CH12 L207-209: Performance targets (~150 nodes)
- CH13 L254-256: Open questions (max nodes/edges, soft cycles)
- CH13 L278-280: Graph rules (0-10 outgoing, 0-N incoming, dangling allowed)
- CH13 L285-286: Cycle policy, soft cycles placeholder
- CH13 L289: Self-loops forbidden
- CH13 L295: 11th branch triggers warning ladder
- CH13 L315: Demo flows don't count toward caps
- CH29 L606: Free cap = 2 flows (locked)
- CH30 L817-821: Limit catalog (flows, inbox, branches, uploads)
- CH30 L829: High-frequency edit thresholds placeholder
- CH30 L874: Unusually large flows signal
- CH30 L880: Edit-rate limit thresholds placeholder

# Handz V1 PRD — Batch 04: Flow Builder (CH12–CH16)

Generated: 2026-01-05
Bundle ID: HZ-V1
Status: Extracted requirements — authoritative

---

## CH12 — Flow Builder: Interaction Model

**Doc ID:** HZ-V1-CH12_Flow_Builder_Interaction_Model_R1
**Depends on:** CH01, CH02, CH03, CH04, CH07, CH08, CH30, CH31
**Related:** CH13, CH14, CH15, CH16, CH18, CH20–CH22, CH28–CH29, CH35–CH37

### Owned Decisions
- Flow builder gestures + selection model
- Branching UX up to 10
- Merges and dangling paths allowed

### Open Questions / Placeholders
- Exact min/max zoom
- Default auto-layout spacing
- Haptic/animation tuning
- Performance thresholds

### 12.1 Scope
- Defines user-facing interaction model: gestures, selection, adding/replacing/deleting nodes, connecting edges, branching (up to 10), merging, dangling paths, pan/zoom, orientation, in-editor guest/paywall gating
- Does NOT define database schema or canonical JSON structures (see CH13)
- V1 behavior: iOS-only, portrait-only (CH02)

### 12.2 Primary User Goal (V1)
- Fast path: create usable flow in under 2 minutes without deep details
- Deep path: power users add branches, merges, labels, transition notes without UI overload
- Predictable editing: every action has obvious result and clear way to undo/back out

### 12.3 Canvas Fundamentals

#### Orientation Modes
- **Horizontal (default):** Flow grows left→right; branches fan out vertically (up/down)
- **Vertical (optional toggle):** Flow grows top→bottom; branches fan out horizontally (left/right)
- Toggle in Builder header overflow menu: "Layout: Horizontal / Vertical"
- Changing orientation re-runs auto-placement (does NOT change graph connections)
- Manual positions behavior: PLACEHOLDER

#### Pan and Zoom
- **Pinch:** zoom in/out around gesture center
- **Two-finger drag:** pan canvas (moves viewport)
- **One-finger drag on node:** drags that node
- **One-finger drag on empty canvas:** long-press (~250ms) then drag to pan OR dedicated pan mode toggle (default: long-press-to-pan)
- Zoom levels:
  - Default: 100%
  - Minimum: PLACEHOLDER (recommend 50%)
  - Maximum: PLACEHOLDER (recommend 200–250%)
  - Transient zoom indicator on change
- Viewport controls:
  - "Fit" action centers/fits entire graph on screen
  - Double-tap empty space: optional shortcut to 100% centered

#### Hit-Target Rules
- Primary controls: iOS 44pt minimum touch target
- Small handles/labels: scale visually but keep invisible padding for reliable taps

#### Node Rendering
- Move Node: rounded card with move name (primary), optional chips (stance/side), affordance for adding connections
- Node states: default, selected, dragging, invalid/incomplete, disabled (gated)

#### Edge Rendering
- **Linear continuation edge:** solid line/arrow, represents "Next move"
- **Branch edge:** dashed line/arrow with label (opponent action/condition), represents decision point "If X then Y"
- Edges are tappable → opens Sequence Detail entry (CH14) or edge action sheet

### 12.4 Builder Entry, Save Model, Guest Gating

#### Entry Points
- From Library: "New Flow" → empty canvas
- From Flow Detail: "Edit" → existing flow in Builder
- From Demo/Onboarding: demo flows open view mode; "Try editing" opens Builder but saving requires account
- From Inbox imports: free users preview; saving gated by account + plan rules

#### Saving Rules (Account Required)
- Saving (draft or final) requires account
- **Guest experience:**
  - Persistent banner: "Guest Mode — changes won't be saved." + CTA "Create Account to Save"
  - Done/Save Draft → account prompt (Apple/Google/Email options per CH07)
- **Free plan cap:**
  - 2 saved flows max (LOCKED)
  - At cap → upsell screen (CH25) + "Manage saved flows" option
- **Autosave (logged-in):**
  - After inactivity (PLACEHOLDER interval) and on app background
  - Status chip: "Saving…" → "Saved"
  - Conflict/offline: CH28

#### Done vs Save Draft
- Header: Back, editable Title, "Done"
- Overflow: "Save Draft", "Fit", "Layout Orientation", "Export (placeholder)", "Delete Flow"
- Done: saves (if possible) → Flow Detail
- Save Draft: saves even if incomplete (dangling allowed)
- Guest: both trigger account prompt

### 12.5 Core Interaction Model

#### Selection and Focus
- Tap node → select (highlight + contextual controls)
- Tap empty canvas → clear selection
- Selection persists through pan/zoom

#### Contextual Actions (Bottom Sheet)
- **Primary:** Add Next (linear), Add Branch, Replace Move
- **Secondary:** Edit Move details (CH11), Delete Node, Manage Branches (reorder)

#### Adding Root Move
- Empty state: CTA "Add First Move"
- Select move → creates root node at origin
- Root is replaceable
- Delete root → empty state

#### Add Next (Linear)
- Select node → Add Next → Move Picker → choose move
- Creates new node (or reuses existing if already on canvas)
- Connect with SOLID edge
- If source has existing solid "next" edge → prompt: Replace next? (Replace / Cancel)

#### Add Branch (Up to 10)
- Select node → Add Branch
- Two-step: Label (opponent action/condition) → Response (move)
- **Hard cap: Max 10 outgoing edges per node (LOCKED)**
- Attempting 11th → blocked with message: "Max 10 branches from a move in V1."
- Branch ordering: drag handles to reorder; order affects visual stacking + practice ordering
- Branches can branch; merges allowed (multiple incoming paths)

### 12.6 Edge Interactions

#### Tapping Edges
- Tap → select (highlight)
- Edge action sheet:
  - Edit Transition Details → CH14
  - Edit Branch Label (branch edges only)
  - Delete Edge (removes connection; leaves nodes)
  - Jump to Target (centers viewport)

#### Branch Labels
- Required and editable
- Inline edit with quick suggestions + free text
- Validation: label cannot be empty

#### Sequence Detail Entry
- Optional; not required between moves (LOCKED)
- Edge sheet always includes "Edit Transition Details"
- Edge tappable at all zoom levels

### 12.7 Deletion, Replacement, Dangling Paths

#### Definitions
- **Leaf node:** No outgoing edges (allowed)
- **Dangling branch:** Conceptually incomplete (future V1/1.1)
- **Orphan node:** No incoming edges except root (allowed; cleanup tools later)

#### V1 Decisions
- Leaves allowed as primary "incomplete" concept
- Optional: branch-stub placeholders (dashed outline node) for "response TBD"

#### Node Replacement
- Replace Move: keeps position/connections; only move reference changes
- No automatic rule adjustments

#### Deleting Nodes
- No edges → delete immediately with undo toast
- Has connections → confirm delete, remove node + connected edges
- Reconnect option: out of scope for V1

#### Undo
- After destructive actions → "Undo" toast for short window
- Full multi-step undo/redo: stretch goal

### 12.8 Move Picker in Builder

#### Entry Points
- Add First Move, Add Next, Add Branch response, Replace Move

#### Picker UI
- Sheet with search bar (autofocus)
- Sections: Recent moves (chips), Essentials (default set), Browse categories
- Selecting move → returns to builder, completes action
- "Create new move" available for logged-in users (guest restrictions per CH08/CH11)

#### Merge vs Duplicate
- Default: selecting existing move reuses node (creates merge)
- Secondary option: "Duplicate node" for separate instances

### 12.9 Layout Behavior at Scale

#### Auto-placement
- **Horizontal:** continuation right, branches staggered up/down by order
- **Vertical:** continuation below, branches staggered left/right by order
- Avoid overlap; nudge by grid increments if needed

#### Manual Repositioning
- Users drag nodes anywhere
- Edges re-route dynamically
- Manual reposition does not change branch order

#### Readability Aids
- Fit to screen action
- Mini-map: optional (not required in V1)
- Path highlight: future

#### Performance Targets (Placeholders)
- Smooth interaction: ~150 nodes
- Degrade gracefully: simplify edge rendering, reduce effects

### 12.10 Error States & Microcopy

| Scenario | Title | Body | CTAs |
|----------|-------|------|------|
| Guest tries to save | "Create an account to save" | "Guest Mode lets you explore, but your flows won't be saved. Create an account to keep this flow." | Sign up (primary), Keep exploring (secondary) |
| Free at cap (2) | "You're at your Free limit" | "Free includes 2 saved flows. Upgrade to save more." | Start Free Trial (primary), Manage saved flows (secondary) |
| Branch cap (10) | — | Toast: "Max 10 branches from a move in V1." | — |
| Empty branch label | — | Inline: "Add a label (what the opponent does)." | — |

### 12.11 Acceptance Tests
1. Empty canvas + "Add First Move" → Move Picker → root node created
2. Selected node + "Add Next" + move → solid edge to chosen node
3. Selected node + "Add Branch" + label + move → dashed labeled edge
4. Node with 10 edges + attempt 11th → blocked with branch-cap message
5. Select already-present node → confirm reuse → merge created
6. Tap branch edge → Edit Transition Details, Edit Label, Delete Edge
7. Guest Mode + Done/Save Draft → signup prompt, nothing saved
8. Pinch gesture → zoom changes, nodes/edges remain interactive
9. Delete connected node + confirm → node/edges removed, Undo toast

### 12.12 Troubleshooting Notes
- Pan/zoom conflicts with node drag: use two-finger pan only or long-press for pan
- Edge label overlap: render labels as pills with background; truncate; show full on tap
- Graph rerenders too often: memoize nodes/edges; native transforms; throttle layout
- Merge vs duplicate confusion: show choice when selecting already-present move
- Guest saving confusion: banner must remain visible; prompts only on explicit save

---

## CH13 — Flow Builder: Node Types & Data Model

**Doc ID:** HZ-V1-CH13_Flow_Builder_Node_Types_And_Data_Model_R1
**Depends on:** CH03, CH12, CH29
**Related:** CH14, CH19, CH20–CH22, CH16

### Owned Decisions
- Canonical graph entity shapes
- Validation rules
- Cycle policy (disallow cycles)
- "Sequence details" stored on edges as transition payload
- Placeholder branch endpoints allowed in Draft state

### Open Questions / Placeholders
- Max nodes/edges per flow per plan → Owner: CH30/CH29
- "Soft cycles" for drill loops → Owner: CH20/CH21
- Edge-label presets that ship by default → Owner: CH10/CH13

### 13.1 Scope and Vocabulary
- **Nodes:** Move Nodes (primary), Placeholder Nodes (draft/incomplete only)
- **Edges:** Connections; linear or conditional (branch)
- **Transition payload ("Sequence details"):** Optional metadata on edge (CH14)
- **Flow Graph container:** Single flow's graph, layout, metadata

### 13.2 Entity Overview

| Entity | Purpose |
|--------|---------|
| Flow | Document/container for user flow; owns graph, layout, metadata, revision history |
| Node | Vertex in directed graph; V1: Move Node or Placeholder Node |
| Edge | Directed connection; may include branch condition label and transition payload |
| Transition (Sequence Details) | Optional structured detail for move-to-move transition; stored on edge |
| Viewport/Layout | Canvas layout data: node positions, viewport transform, UI preferences |

### 13.3 Graph Rules and Validation

#### Directed Graph Characteristics
- **Directed:** every edge from source to target
- **Branching:** 0–10 outgoing edges per Move Node (max 10)
- **Merging:** 0–N incoming edges (unlimited)
- **Dangling paths:** leaf nodes allowed; Draft flows may contain Placeholder Nodes
- **Root replaceable:** rootNodeId stored; any node can become root

#### Cycle Policy
- **V1: Cycles disallowed** (no node reachable from itself)
- Prevents infinite practice loops; simplifies path enumeration
- "Soft cycles" for drill loops: PLACEHOLDER (Practice chapters)

#### Edge Uniqueness + Self-Loop Rules
- Self-loops forbidden: sourceNodeId != targetNodeId
- Duplicate edges allowed only if conditionLabel differs
- Edge IDs globally unique within flow (UUIDs)

#### Max Outgoing Branches
- Max 10 outgoing edges per Move Node
- Attempting 11th → CH30 Warning Ladder (soft caps/upgrade prompts)

### 13.4 Canonical Schemas

#### Flow (Graph Container)

| Field | Type | Req | Description |
|-------|------|-----|-------------|
| id | string (uuid) | Y | Unique flow ID |
| ownerUserId | string | Y | Owner user |
| title | string | Y | User-facing name; can be empty in Draft |
| status | enum | Y | draft \| saved \| archived |
| rootNodeId | string (uuid) | Y | Entry node for path enumeration/practice |
| nodeIds | array | Y | List of node IDs |
| edgeIds | array | Y | List of edge IDs |
| layout | object | Y | Canvas layout/viewport (see 4.4) |
| tags | array | N | User tags for search/filter (CH15) |
| createdAt | timestamp | Y | Creation time |
| updatedAt | timestamp | Y | Last modified time |
| revision | int | Y | Monotonic increment; conflict resolution (CH28) |
| isDemo | bool | N | Demo flows don't count toward caps |

#### Node

| Field | Type | Req | Description |
|-------|------|-----|-------------|
| id | string (uuid) | Y | Unique node ID |
| type | enum | Y | move \| placeholder |
| moveRef | object | Cond | Required when type=move |
| placeholderKind | enum | Cond | Required when type=placeholder: add_response \| add_branch_stub |
| ui | object | Y | UI/layout fields (position, size) |
| meta | object | N | Optional metadata |

#### MoveRef (Inside Move Node)

| Field | Type | Req | Description |
|-------|------|-----|-------------|
| canonicalMoveId | string | Y | Stable canonical ID (CH10) |
| displayName | string | Y | Rendered name (can differ from canonical) |
| userMoveId | string | N | Reference to user's customized move entry |
| stanceContext | enum | N | orthodox \| southpaw \| switch \| any \| unset |
| tags | array | N | Node-local tags |

#### Edge

| Field | Type | Req | Description |
|-------|------|-----|-------------|
| id | string (uuid) | Y | Unique edge ID |
| sourceNodeId | string (uuid) | Y | From node |
| targetNodeId | string (uuid) | Y | To node |
| kind | enum | Y | linear \| conditional |
| conditionLabel | string | Cond | Required when kind=conditional |
| order | int | N | Ordering hint for rendering |
| transition | object | N | Optional Sequence Details payload |
| ui | object | N | Optional style hints |
| meta | object | N | Optional metadata/provenance |

#### Transition (Sequence Details Payload)

| Field | Type | Req | Description |
|-------|------|-----|-------------|
| id | string | Y | Unique within edge |
| isEnabled | bool | Y | Whether shown in Sequence Editor |
| fighter | object | N | Fighter transition fields |
| opponent | object | N | Opponent position/behavior notes |
| notes | string | N | Freeform transition notes |
| attachments | object | N | Video link refs (CH29) |
| updatedAt | timestamp | N | Last edit time |

#### Layout / Viewport

| Field | Type | Req | Description |
|-------|------|-----|-------------|
| nodes | map | Y | nodeId → UI fields |
| viewport | object | N | {x, y, zoom} for resume |
| layoutVersion | int | Y | Bump if layout algorithm changes |

#### NodeUI

| Field | Type | Req | Description |
|-------|------|-----|-------------|
| x | number | Y | Canvas x coordinate |
| y | number | Y | Canvas y coordinate |
| w | number | N | Optional width |
| h | number | N | Optional height |
| collapsed | bool | N | For summary/collapse features |

### 13.5 Optional Node Types

#### Placeholder Nodes (Draft-only)
- **add_response:** Unfinished branch (condition exists, response not chosen)
- **add_branch_stub:** "Tap to add branch" affordance
- Valid in draft status; prompt to resolve on save/share

#### Summary Nodes (Future-friendly)
- Collapse subtree: implement as NodeUI.collapsed=true (not separate type)
- Real Summary Node type: create CH13-R2 if needed

### 13.6 Firebase (Firestore) Storage Shape

#### Collections
- `flows/{flowId}` — Flow document
- `flows/{flowId}/nodes/{nodeId}` — Node documents
- `flows/{flowId}/edges/{edgeId}` — Edge documents

#### Example Flow Document (JSON)
```json
{
  "id": "flow_123",
  "ownerUserId": "user_abc",
  "title": "Pressure Defense A",
  "status": "saved",
  "rootNodeId": "node_root",
  "revision": 7,
  "layout": { "layoutVersion": 1, "viewport": {"x": 0, "y": 0, "zoom": 1.0} },
  "createdAt": "2026-01-02T18:10:00Z",
  "updatedAt": "2026-01-02T19:05:00Z"
}
```

#### Example Node Document
```json
{
  "id": "node_root",
  "type": "move",
  "moveRef": {
    "canonicalMoveId": "move_jab",
    "displayName": "Jab",
    "userMoveId": null,
    "stanceContext": "any"
  },
  "ui": { "x": 120, "y": 240 },
  "meta": { "createdAt": "2026-01-02T18:10:05Z" }
}
```

#### Example Edge Document
```json
{
  "id": "edge_001",
  "sourceNodeId": "node_root",
  "targetNodeId": "node_cross",
  "kind": "conditional",
  "conditionLabel": "leans back",
  "transition": {
    "id": "edge_001",
    "isEnabled": true,
    "notes": "Opponent weight shifts rearward; head stays high."
  }
}
```

### 13.7 Derived Computations

#### Path Enumeration
- Path: sequence from rootNodeId following edges to leaf or Placeholder Node
- DFS from root; stop at leaves; enforce cycle check
- Path IDs: deterministic hashes of ordered node IDs + edge IDs

#### Merge Handling
- Merges don't create ambiguity
- Each full traversal root→leaf is distinct path (even if suffixes shared)

### 13.8 Acceptance Tests
1. Node with 10 edges + attempt 11th → blocked, triggers CH30 warning/upgrade
2. Draft flow with placeholder add_response node → saveable; placeholder visible
3. Create edge A→B → stores sourceNodeId=A, targetNodeId=B; conditionLabel/transition optional
4. Connect node to itself → rejected (self-loop forbidden)
5. Edge completing cycle → rejected with error
6. Move node on canvas → only NodeUI x/y updated
7. Import flow with unknown fields → unknown fields preserved

### 13.9 Troubleshooting Notes
- Edge deletes another edge: non-unique IDs → use UUIDs
- Canvas re-layout resets positions: overwriting NodeUI → partial update only
- Practice path list infinite/hangs: cycle introduced → enforce cycle policy
- Imported flows lose metadata: strict parsing drops unknowns → preserve unknown fields
- Edge labels not showing: conditionLabel on linear edges → ensure kind=conditional when label exists

---

## CH14 — Sequence Detail Editor

**Doc ID:** HZ-V1-CH14_Sequence_Detail_Editor_R1
**Depends on:** CH03, CH06, CH08, CH12, CH13, CH31
**Related:** CH15, CH16, CH19, CH20, CH21, CH22, CH28, CH29, CH30, CH32

### Owned Decisions
- Editor surface and field groups
- Save/cancel rules
- Per-edge detail lifecycle
- UX copy for sequence editing

### Open Questions / Placeholders (See §11)
- Max reference links per sequence → Owner: CH14 → Options: 3/5/10 → Default: 5
- Max characters per note field → Owner: CH14 → Options: 500/1000/2000 → Default: 1000
- Show sequence notes during active practice → Owner: CH21 → Options: Off/View-only/Edit → Default: View-only
- Feint reorder interaction → Owner: CH14 → Options: Up/Down buttons/Drag handle → Default: Up/Down

### 14.1 Scope
- **Sequence:** transition between two move nodes in a flow
- **Sequence Detail Editor:** UI for recording transition metadata (footwork, distance, opponent reaction, cues, notes)
- Owned: view/edit transition metadata, editor UI, validation, save/revert, copy, deletion, copywriting
- Progressive disclosure: supports ultra-simple notes AND deep detail

### 14.2 User Jobs
- **Striker:** Add quick note for transition; describe opponent triggers, cues, response; store micro-details without forced fields
- **Coach:** Encode the "why" between nodes; keep minimal for beginners, expand for advanced
- **Importer:** See sequence details exactly as creator intended

### 14.3 Entry Points and Navigation

#### Primary Entry (V1)
- Flow Builder: tap edge (or inline "details" affordance) → opens Sequence Detail Editor as modal sheet

#### Other Entry Points
- Flow Detail View: "Paths"/"Transitions" list → deep-link to specific edge
- Practice Mode: view-only sequence notes (optional)

#### Route
- `SequenceDetailModal(flowId, edgeId)`
- Implementation: bottom sheet or full-screen modal

### 14.4 Editor Surface and Layout

#### Modal Header (Always Visible)
- Title: "Sequence"
- Subtitle: "<source> → <target>" (auto-generated, non-editable)
- Branch label pill chip (if conditional edge)
- Actions: [Close] left, [Save] right
- Save disabled until dirty; shows "Saved" or check icon when clean

#### Top Summary Block (Always Visible)
- **Quick Notes:** multiline text, optional; placeholder: "Add a quick note about this transition…"
- **Tags:** optional chips (Angle off, Close distance, Counter, Exit, Pressure, etc.)
- **Reference Links:** optional URL list; shareable when flow shared

#### Collapsible Sections (Collapsed by Default)

**Fighter Transition:**
- Footwork
- Distance/Range change
- Angle/Position change
- Guard/Head movement notes
- Rhythm/Timing cues

**Opponent State:**
- What opponent did/is doing (freeform)
- Opponent balance/weight shift (optional)
- Opponent guard/state cues (optional)

**Cues and Intent:**
- My cue to trigger next move (freeform)
- Intent preset: Create space / Close distance / Maintain / Angle off / Custom

**Feints During Transition:**
- List of 0..N feints with timing, notes (reorderable via Up/Down)

**Media (Pro Uploads):**
- Attach private video clips (Pro-only; never shared)
- Required copy: "Uploads stay private and do not travel with shared links."

**Advanced / Metadata:**
- Confidence / Notes (optional)
- Created/Edited timestamps (read-only)
- Revert / Reset controls

### 14.5 Field Specifications

| Field | Type | Notes |
|-------|------|-------|
| edgeId | string (FK) | Identifies connection |
| flowId | string (FK) | Parent flow |
| sourceMoveId / targetMoveId | string (FK) | Read-only; derived from edge |
| quickNote | string | Multiline, optional |
| tags | string[] | Freeform + suggested chips |
| referenceLinks | url[] | Shareable; validate format |
| fighterTransition | object | Structured optional subfields |
| opponentState | object | Structured optional subfields |
| cuesAndIntent | object | My cue + intent preset |
| feints | object[] | 0..N entries; reorderable |
| privateMedia | object[] | Pro-only; private; subject to CH29 |
| audit | object | createdAt, updatedAt, createdBy, updatedBy (read-only) |

#### fighterTransition
- **footwork:** freeform + presets (Pivot, Step-through, Switch stance, Drop step, Shuffle, L-step, Circle out)
- **rangeChange:** slider/enum (Far→Medium→Close) + optional note
- **angleChange:** enum (Center, Inside, Outside, Rear, Custom) + optional note
- **headAndGuard:** freeform note
- **timing:** enum (Beat, Half-beat, Delay, Reactive, Custom) + optional note

#### opponentState
- **opponentAction:** freeform; prefill from branch label if conditional edge
- **weightShift:** optional enum (Lead, Rear, Even)
- **guardState:** optional freeform
- **notes:** extra freeform

#### cuesAndIntent
- **myCue:** freeform
- **intentPreset:** Create space / Close distance / Maintain / Angle off / Custom
- **intentCustom:** only if Custom selected

#### feints[] Entries
- **timing:** Before / During / After / Double
- **type:** freeform
- **purpose:** freeform
- **notes:** freeform
- **reorder:** Up/Down buttons (V1)

### 14.6 Save, Close, Revert, Deletion Rules

#### Save Model
- **Explicit Save:** user taps [Save]
- Save disabled until dirty
- After save: toast "Saved"; keep editor open
- **Offline:** save to local draft queue (CH28); UI shows "Saved locally - will sync when online."

#### Close Behavior
- No unsaved changes → close immediately
- Unsaved changes → confirm sheet:
  - Title: "Discard changes?"
  - Body: "You have unsaved edits to this sequence."
  - Buttons: [Keep Editing] (primary), [Discard] (destructive)

#### Revert Options
- **Revert this sequence:** resets to last saved version
- **Clear all fields:** sets all empty (requires confirmation)
- Restore after clear: discard changes before save; if saved, can only revert to empty

#### Delete Record
- Action: "Delete Sequence Details" in Advanced/Metadata
- Confirm: "Delete details?" / "This removes notes and metadata for this transition. The connection stays."
- Deleting details does not affect practice logs

### 14.7 Branch Labels and Node Types

#### Linear Edges
- Subtitle shows "A → B"; no condition pill
- Opponent State fields blank unless user fills

#### Conditional Edges (Branch)
- Condition label shown as pill chip under subtitle
- Prefill opponentState.opponentAction with label text
- **Label change rule:**
  - If opponentAction still equals old label → update to new label
  - If user edited beyond label → keep their text; show inline note: "Edge label changed - update your opponent note if needed."

#### Optional Sequence Nodes
- If CH13 introduces visible "sequence node," must be view of same edge metadata
- Edge tap or node tap opens same SequenceDetailModal

### 14.8 Media Rules

- **Reference Links (all plans):** add URLs; included in shared flows
- **Uploads (Pro only):** 0..N private videos; never travel with share links/imports
- **Guest:** can attach links in draft; cannot persist until account creation
- Required UI copy: "Uploads stay private and will not appear in shared links."
- Share flow with private uploads → non-blocking reminder in share screen (CH17/CH29)

### 14.9 Error States and Validation

#### Validation (Non-blocking Unless Unsafe)
- Invalid URL → inline error "That link doesn't look right."; disable Save for invalid URLs only
- Text length exceeded → inline counter; prevent further typing; never truncate silently

#### Storage/Sync Failures
- Save fails (network) → toast "Couldn't save right now. Try again." + [Retry]
- Local draft queue full → blocking sheet "Storage full" + [Manage Storage] / [Cancel]
- Edge deleted while editor open → close editor + toast "That transition no longer exists."

### 14.10 Plan Gating

| Plan | Behavior |
|------|----------|
| Guest | Open/edit in draft; cannot save flow; triggers account prompt |
| Free | Edit sequence details for saved flows (within caps); practice paywalled; sequence editing allowed |
| Pro/Trial | Same as Free + attach private uploads (CH29 2GB cap) |

Free taps "Add Upload" in Media → paywall interstitial (CH25) → return to SequenceDetailModal after upgrade

### 14.11 Acceptance Tests

**Basic Open/Close:**
1. Flow with two connected moves + tap edge → SequenceDetailModal opens showing "Sequence" and "A → B"
2. No unsaved changes + tap Close → dismisses immediately

**Save + Dirty State:**
3. Open + type in Quick Notes → Save becomes enabled
4. Tap Save + success → toast "Saved"; Save returns to disabled

**Unsaved Discard:**
5. Unsaved changes + tap Close → "Discard changes?" confirm with Keep Editing / Discard
6. Tap Discard → changes lost, modal closes

**Branch Label Prefill:**
7. Conditional edge with "Leans back" + open → pill visible, opponentAction prefilled "Leans back"
8. Edit opponentAction to longer sentence + edge label changes later → user text unchanged, inline note shown

**Plan Gating:**
9. Free user + tap Add Upload → paywall opens; returning keeps same editor
10. Pro user + attach video → appears in private media list, marked "Private - not shared"

**Edge Deleted:**
11. Editor open + edge deleted in canvas → editor closes + toast "That transition no longer exists."

### 14.12 Troubleshooting Notes
- Modal doesn't open on edge tap: verify edge component emits edgeId/flowId; confirm route registered
- Save stays enabled after saving: recompute dirty-state using last-saved snapshot comparison
- Branch label overwrites user notes: implement label-change rule using "wasEdited" or string comparison
- Performance issues while typing: memoize sections; virtualize large lists; avoid rerendering canvas behind modal
- Uploads appear in shared links: CH29 rule violation; exclude from share payloads; show placeholders

---

## CH15 — Library: Flows / Folders / Search / Sort

**Doc ID:** HZ-V1-CH15_Library_Flows_Folders_Search_Sort_R1
**Depends on:** CH02, CH04, CH08, CH16, CH18
**Related:** CH05, CH17, CH20, CH25, CH30, CH31

### Owned Decisions
- Library IA
- Folder model (V1)
- Flow-only search (V1)
- Sorting options + persistence
- Manage-mode actions + routing rules
- Library plan-gating surfaces

### Open Questions / Placeholders
- Demo_Flows_In_Library → Owner: CH15 → Options: A) 1 built-in demo / B) No demo → Default: A
- Folder_Order_Model → Owner: CH15 → Options: A) Manual drag / B) Always by Name → Default: B
- Duplicate_Title_Rule → Owner: CH15 → Options: A) "(Copy)" / B) "Copy 2/3…" / C) Prompt user → Default: A
- Flow_Title_Max_Length → Owner: CH29/CH31 → Options: 50/60/80 → Default: 60
- Folder_Name_Deduping → Owner: CH15 → Options: A) Allow / B) Auto-append (2) / C) Block → Default: B
- Swipe_Actions → Owner: CH15 → Options: A) Enable / B) No swipe → Default: A

### 15.1 Library Concept
- User's durable collection of saved flows
- Find flow quickly (search + sort)
- Organize into folders
- Enter flow via Flow Detail (CH16)
- Start practice (CH20); show practice metadata
- Manage flows (rename, duplicate, move, delete)
- Imports received in Inbox (CH18); not Library until explicitly saved

### 15.2 Plan-State Behaviors

| Plan | View Library? | Create/Save? | Saved Flow Cap | Folders | Practice Entry |
|------|--------------|--------------|----------------|---------|----------------|
| GUEST | Yes (demo + temp drafts) | No (blocked) | 0 | View-only demo folders (optional) | Demo only |
| FREE | Yes | Yes | 2 (hard cap) | Yes (no cap default) | Credits only on saved flows |
| PRO / Trial | Yes | Yes | Unlimited (soft warnings) | Yes | Unlimited |

Hard-cap behaviors: CH30 §3.1 (Saved Flows)

### 15.3 Navigation & Routes

| Route ID | Screen Name | Params | Entry Points | Back Behavior |
|----------|-------------|--------|--------------|---------------|
| tab.library | Library Home | none | Bottom tab | Back exits app if root |
| library.folders | Folders List | none | Library Home → Folders | Back → Library Home |
| library.folder_detail | Folder Detail | folderId | Folders List; Move-to-folder | Back → Folders List |
| library.search | Flow Search Results | query, scope (optional folderId) | Search bar submit | Back → previous list; clears query |
| library.manage | Manage Mode | context: flows\|folder\|search | Edit/Manage button | Done returns to context |
| flow.detail | Flow Detail View | flowId | Tap flow card | Owned by CH16 |

### 15.4 Library Home (tab.library)

#### Layout
- Top bar: "Library", overflow icon for settings
- Search bar: placeholder "Search flows" (flow-only in V1)
- Primary CTA row: "+ New Flow" (primary), "Folders" (secondary) OR segmented control
- Sort control: "Sort: Recent" (tap opens sheet)
- Flow list: scrollable flow cards
- Empty state: if 0 flows (see §15.8)

#### Flow Card (List Item)
- Title (required)
- Subtitle: last practiced date OR "Never practiced"
- Folder badge (if assigned)
- Metadata chips: "Gameplan" label, node count, tags count
- Overflow menu (…): actions per §15.6
- Tap → Flow Detail (CH16)
- Long-press → Manage Mode with pre-selected
- Swipe actions (optional): quick Delete + Move-to-folder

### 15.5 Folders

#### Model
- Folder contains list of saved flow references
- Flow belongs to **at most 1 folder** in V1
- Deleting folder **never deletes flows** (become "Unfiled")
- Folder order: manual drag OR default by name (see placeholders)

#### Folders List (library.folders)
- Top bar: "Folders", back chevron
- Primary CTA: "+ New Folder"
- List: folder name + flow count
- Row actions: tap → Folder Detail; overflow: Rename, Delete
- Empty state: "No folders yet. Create one to organize flows."

#### Create/Rename Folder (Modal)
- Title: "New Folder" or "Rename Folder"
- Field: "Folder name" (max length placeholder)
- Buttons: "Save" (disabled until non-empty), "Cancel"
- Validation: trim whitespace; collapse multiple spaces; reject only punctuation; allow emojis
- Duplicate name: allow but append "(2)" automatically OR block (see placeholders)

#### Folder Detail (library.folder_detail)
- Top bar: folder name + overflow (Rename, Delete)
- Search bar: optional (within folder)
- Sort control: same options as Library Home
- Flow list: same Flow Card component
- Manage: bulk select to remove from folder or move to another
- Empty state: "This folder is empty." + CTA "Move flows here"

### 15.6 Actions, Menus, Routing

#### Library Home Actions
- **+ New Flow:** routes to Flow Builder; gate before creation if saving blocked
- **Folders:** routes to library.folders
- **Sort:** opens Sort Sheet
- **Search:** routes to library.search
- **Edit/Manage:** enters library.manage

#### Flow Card Overflow Menu

| Action | Shown When | Tap Behavior | Gating |
|--------|-----------|--------------|--------|
| Open | Always | Route: flow.detail(flowId) | CH16 |
| Practice | Saved + access | Route: practice.setup(flowId) | FREE 0 credits → paywall; GUEST → account/demo gate |
| Share | Plan allows | Route: sharing.create(flowId) | Share limits (CH17+CH30) |
| Duplicate | Always | Creates copy → Library with toast | Counts against cap; FREE at cap → hard-block |
| Move to folder | Folders enabled | Folder picker sheet | If no folders, show create-folder inline |
| Rename | Always | Rename modal | Title validation |
| Delete | Always | Confirm delete modal | Destructive; Undo toast optional |

#### Manage Mode (Bulk Select)
- Enter: "Manage" button OR long-press flow card
- UI: top bar "Select" + count; Buttons: "Move", "Delete"; Optional: "Remove from folder"
- Selection: tap toggles; "Select all" only if ≤50 items
- Exit: "Done" clears selection
- Bulk Delete confirmation shows count

### 15.7 Sorting

#### Sort Options (V1)
- **Recent (default):** updatedAt desc → createdAt desc → flowId tie-breaker
- **Name:** A→Z normalized title; tie-breaker createdAt desc
- **Last Practiced:** lastPracticedAt desc; nulls bottom; tie-breaker updatedAt desc

#### Sort Sheet
- Title: "Sort flows"
- Radio list: Recent / Name / Last practiced
- Toggle (optional): Ascending/Descending
- Select immediately applies
- Persist in user settings (CH29)

### 15.8 Search (Flow-only V1)

#### Scope and Matching
- Returns flows only (not moves, sequences, notes)
- Default scope: all saved flows + demo
- Optional: within folder
- Matching: case-insensitive substring on flow title after normalization
- Normalization: trim, collapse whitespace, remove diacritics (display original)
- Ranking: shortest distance match first (prefix higher), then updatedAt desc

#### UI Behavior
- Live results after 2+ chars with 150ms debounce (recommended)
- Empty query: show recent flows
- No results: "No flows found for '<query>'." + CTA "Clear search"
- Back clears query

### 15.9 Empty States

#### Empty Library (0 Flows)
- Title: "No flows yet."
- Body: "Create your first flow to start planning your striking decisions."
- Primary CTA: "+ New Flow"
- Secondary: "See Demo" or "Learn how it works"
- Guest variant: "Create an account to save flows." + "Create Account"

#### Free at Cap (2 Flows)
- Lists 2 flows normally
- "+ New Flow" triggers hard-cap flow (CH30)
- Helper: "Free plan: 2 saved flows."
- Updates dynamically on delete

### 15.10 Validation Rules

#### Flow Title
- Cannot be empty after trimming
- Max length: PLACEHOLDER (default 60 chars)
- Allowed: letters, numbers, emojis, punctuation
- Duplicates allowed

#### Folder Name
- Cannot be empty after trimming
- Max length: PLACEHOLDER (default 40 chars)
- Same normalization as flows

### 15.11 Plan Gating Integration

#### Guest: Saving Blocked
- "+ New Flow" → blocking gate: "Create an account to save flows."
- Builder demo + Save attempt → same gate
- Demo flows viewable (Flow Detail view-only)

#### Free: 2-Flow Cap Enforcement
- Triggers: create, duplicate, save inbox item, save demo-as-copy, convert draft
- At cap → CH30 hard block: Upgrade / Manage (delete) / Not now
- Manage: Library Home in Manage Mode with helper "Delete one to save a new flow."

#### Free: Inbox Save at Cap
- "Save to Library" at 2 flows → hard block: "Library full (2 flows on Free)."
- Choices: Upgrade / Manage Library / Cancel
- Inbox item stays until removed or upgraded (CH18)

### 15.12 Accessibility & Performance
- Tap targets ≥ 44×44pt
- Dynamic Type: title wraps 2 lines; metadata collapses gracefully
- VoiceOver: flow row announces title + last practiced + folder; overflow announces actions
- Avoid heavy thumbnails; lazy-load/cache if added (CH29)
- Virtualize lists for performance at high counts (PRO)

### 15.13 Analytics Hooks
- library_opened: planState, flowCount, folderCount
- library_create_flow_tapped: planState, atCap
- library_flow_opened: flowId, planState, source
- library_flow_menu_action: actionName
- library_search_performed: queryLength, resultsCount, scope
- library_sort_changed: sortType, direction
- library_manage_bulk_action: action, countSelected

### 15.14 Acceptance Tests
1. Library loads <2s (cold start) with 0, 2, 100 flows
2. Search returns correct results (case-insensitive, substring); respects folder scope
3. Sort order matches spec; persists across restarts
4. Folder create/rename/delete works; deleting never deletes flows
5. Move flow to folder → appears in folder detail; remove → Unfiled
6. Overflow actions route correctly (Open→CH16, Practice→CH20, Share→CH17)
7. FREE at 2-cap: + New Flow, Duplicate, Save-from-Inbox all hard-block
8. GUEST cannot save; create prompts account before builder; no local persistence
9. Accessibility: VoiceOver reads correctly; tap targets meet minimum; Dynamic Type doesn't clip
10. Empty states appear with correct CTAs

### 15.15 Troubleshooting Notes
- FREE creates 3rd flow via duplicate/inbox: gating must check all save paths
- Deleting folder deletes flows: folder deletion must only remove folderId references
- Search returns inconsistent ordering: apply ranking rules + deterministic tie-breakers
- Library list janks with many flows: use virtualization, avoid heavy previews, memoize
- Sort resets unexpectedly: persist sort choice in user settings

---

## CH16 — Flow Detail View

**Doc ID:** HZ-V1-CH16_Flow_Detail_View_R1
**Depends on:** CH04, CH05, CH08, CH12, CH15, CH17, CH18, CH20–CH22, CH29, CH30
**Related:** CH03, CH09–CH11, CH13–CH14, CH23–CH24, CH25–CH28, CH31, CH33–CH37

### Owned Decisions
- Flow Detail layout and sections
- Action entry points
- Gating/UI behavior for edit/practice/share/duplicate/export
- Required confirmations
- Surface-level flow metadata presentation

### Open Questions / Placeholders
- Exact upsell copy strings → Owner: CH15
- Exact export formats and limits → Owner: CH29/CH25
- Exact share-link daily caps for Pro → Owner: CH17/CH30
- Exact analytics event names → Owner: CH33
- Thumbnail generation approach → Owner: CH12/CH29

### 16.1 Purpose and Scope
Flow Detail answers:
1. What is this flow and what's inside it?
2. What can I do next (practice, edit, share, duplicate, export)?
3. What am I allowed to do given plan state and context?
4. How do I avoid mistakes?

**Out of scope:** flow-building (CH12/CH13/CH14), share-link limits (CH17/CH30), practice mode behavior (CH20–CH22), gameplans (CH23/CH24)

### 16.2 Entry Points and Route Contract

**Route ID:** FlowDetail
**Params:** flowId, source (enum), readOnly (bool, optional), inboxItemId (optional), deepLinkToken (optional)
**Source enum:** library | practice | inbox | shared_link | demo | search | folder

#### Entry Points
- **Library:** tap Flow Card → FlowDetail(flowId, source='library')
- **Practice tab:** tap flow → FlowDetail(flowId, source='practice')
- **Inbox:** tap item → FlowDetail(flowId or inboxItemId, source='inbox', readOnly=true until saved)
- **Shared link:** open deep link → FlowDetail(flowId via token, source='shared_link', readOnly=true)
- **Demo:** browse demo → FlowDetail(flowId, source='demo', readOnly=true)

Back behavior: owned by CH04; returns to previous list context

### 16.3 Data Model Inputs

#### Required Flow Fields
- flowId, ownerUserId, title, createdAt, updatedAt
- visibility: private | unlisted
- rootNodeId, nodes[], edges[] (CH13)
- branchCount (derived), maxOutDegree (derived; ≤10)
- tags[], folders[]
- notes (optional; plain multiline text)
- attachments summary: linkAttachmentsCount, localUploadsCount (CH29)
- practiceSummary (derived): lastPracticedAt, totalSessions, totalSets, totalAssumedReps, streakContinuation

#### Context Fields
- **Inbox:** inboxItemId, senderHandle, receivedAt, preview-only for Free
- **Shared link:** token metadata (revoked/active), expiration, source user
- **Demo:** isDemo=true; doesn't count toward cap; readOnly enforced

### 16.4 Screen Layout

#### Top App Bar
- Left: Back chevron
- Center: Flow title (1 line, ellipsize; tap to rename if editable)
- Right: Overflow menu (kebab)
- Optional: plan badge in overflow

#### Primary Action Strip (Always Visible)
- Buttons: **Practice**, **Edit**, **Share**
- Practice: if not allowed → locked state, tap opens paywall/save/upgrade path
- Edit: opens Flow Builder (CH12); Guest → account prompt
- Share: opens Share Sheet modal

#### Flow Preview Card
- Static preview (thumbnail), not interactive canvas
- Tap: opens Flow Builder in view mode (readOnly) or edit mode
- If thumbnail can't render: fallback icon + "Open flow"
- Branch count badge

#### Summary Metadata
- Last practiced + total sessions
- Complexity: node count, path count, max outgoing branches
- Attachments: "Links: X" and "Local uploads: Y (not shareable)" + info tooltip
- Visibility badge: Private or Unlisted

#### Paths List (V1)
- Derived list: linear sequences root → terminal (including merges)
- Each row: path name/label (auto-generated), terminal move, length, branch labels breadcrumb
- Tap path → Path Detail subview (full sequence + sequence notes from CH14)
- Multi-select: V1 single-select; add "Select in Practice Setup" entry

#### Notes Section
- Simple multiline plain text
- Edit allowed if editable and not Guest
- Empty state: "Add notes for this flow"

#### History Teaser (Optional V1)
- If practice history: last 3 sessions + "View all history" (CH22)
- If CH22 not implemented: omit entirely

### 16.5 Overflow Menu (Secondary Actions)

#### Standard Items (Library-owned, Editable)
- Rename
- Duplicate (creates new saved flow)
- Move to Folder (if folders exist; CH15)
- Export (opens Export modal)
- Delete (destructive)

#### Read-only Items (Inbox/Shared/Demo)
- Save to Library (account required; free cap applies)
- Duplicate to My Library
- Report issue (optional)
- View share info (shared link: "Created by" + last updated)

#### Disabled Items
- Show short reason + single next step
- Example: "Export (Pro) - Upgrade to export this flow"
- Tap opens relevant upsell

### 16.6 Permissions and Plan Gating

| Context / Action | Guest | Free | Pro/Trial |
|------------------|-------|------|-----------|
| View flow detail | Yes (demo/shared/inbox preview) | Yes | Yes |
| Save flow to library | No (account required) | Yes (cap: 2) | Yes (higher caps) |
| Edit flow | No | Yes (saved only) | Yes |
| Practice | No | Paywalled; 3 monthly credits on saved flows only | Yes (unlimited) |
| Share link creation | No | Limited (CH17/CH30) | Higher limits |
| Export | No | TBD (placeholder) | TBD (placeholder) |

#### Saved Flow Cap (Free: 2)
- At cap + save/duplicate → blocking modal: "Flow limit reached (2/2)"
- Options: Upgrade / Manage flows
- Manage flows → Library filtered view + hint "Delete one to save a new one"
- Never silently replace/overwrite
- Return to same FlowDetail after Manage flows

#### Inbox View-Only (Free)
- source='inbox' + not saved → view only, cannot Practice
- Practice locked: "Practice requires saved flow" → modal: "Save to practice" + "Upgrade for unlimited"
- Inbox cap (Free: 10) warning only when accepting/saving new items

#### Practice Credits (Free: 3/month)
- Practice button shows: "Practice (X credits left)" when credits > 0
- First credit spend each month → confirmation: "Use 1 Practice Credit?" + "Use credit" / "Not now" + "Learn more"
- Credits only on saved flows (CH00 lock)
- credits == 0 → Practice locked; tap opens paywall + "Restore purchase"

### 16.7 Core Actions

#### Practice CTA
- Always routes to PracticeSetup (CH20), never directly to CH21
- If allowed: navigate with preselected flow=flowId
- If not allowed: show gating modal (account, save, credits, paywall)
- Local uploads: practice allowed (uses local assets); exclude from share previews

#### Edit CTA
- Editable: open Flow Builder edit mode
- readOnly: open Flow Builder view mode + banner "Read-only preview"
- Guest: account creation modal "Create an account to build and save flows"

#### Share CTA
- Opens Share Sheet modal (unlisted link sharing V1)
- Elements: Create/Copy Link, System share sheet, Revoke Link
- Existing link: show link + "Copy" + "Revoke"
- No link: "Create link" → success toast + link
- Share limit warning (CH30) → warning modal + escalation

#### Duplicate
- Creates new flow owned by current user, new flowId
- Copies nodes/edges/notes/labels
- Default title: "<title> (Copy)" → allow rename
- Free at cap → block with modal
- readOnly: label "Duplicate to My Library"

#### Export
- Opens bottom sheet with formats (examples: .hzf, PDF, Image)
- Shows: availability (Free/Pro), size estimate, private media inclusion
- **Local uploads excluded from shareable exports (default)**

#### Delete
- Confirm modal with flow name
- If practice history coupled: "This will also delete its practice history"
- If history separate: "History will remain"
- After delete: toast "Deleted" → navigate back

### 16.8 Share Sheet Modal

#### Elements
- Header: "Share Flow" + close
- Flow identity: title + thumbnail
- Status line: "Unlisted link" + "Anyone with the link can view and import."
- Primary: Create link (if none) OR Copy link
- Secondary: "Share via..." (OS share sheet)
- Danger: "Revoke link" (if exists)

#### Media Clarity
- If local uploads: "Local videos are private and will not be included when shared. Use links if you want recipients to see videos."

#### Revoke Behavior
- Invalidates old link immediately
- Old link shows: "This link has been revoked."
- After revoke: modal returns to "Create link" state
- Confirmation: "Revoke link? Anyone with the old link will lose access."

### 16.9 Edge Cases

#### Large Flows
- Node count > threshold → simplified preview + "Open flow" button
- Paths list: paginate/virtualize; don't freeze UI
- Lazy path calculation + skeleton state

#### Missing Moves
- Reference to missing move → "Missing move" placeholder name
- Tap → resolution prompt "Add missing move to library" (CH19)

#### Conflicting Customizations
- Imported flow with custom metadata → small "Custom" badge on affected moves/paths
- Detail resolution: CH19

### 16.10 Acceptance Tests
1. Free (1 saved) + Duplicate library flow → new flow "(Copy)"; can rename
2. Free (2 saved) + "Save to Library" from inbox → "Flow limit reached (2/2)" modal with Upgrade / Manage
3. Free (inbox, not saved) + tap Practice → blocked; modal explains save/upgrade; no credit spent
4. Free (credits remaining, saved) + tap Practice first time this month → "Use 1 Practice Credit?" confirm → credits decrement; lands in Practice Setup
5. Pro + tap Share (no link) → link created + displayed; OS share works
6. Flow with local upload + Share Sheet → note that local uploads not shared
7. readOnly shared-link + tap Edit → Flow Builder view mode; no editing controls
8. Delete flow + confirm → removed from Library; navigate back; no crash

**Checklist:**
- Header/action strip render across iPhone sizes
- Disabled actions show reason + next step
- Share link create/copy/revoke works + updates UI immediately
- Practice credit confirmation appears only when credit would be spent
- No UI freezes on large flows; thumbnail degrades gracefully

### 16.11 Troubleshooting Notes
- Credits decrement when practice blocked: move decrement to confirmed navigation after gating check
- Thumbnail crashes on large flows: add size threshold; compute off main thread
- Share link opens blank: verify token→flowId lookup; check revoked-link handling
- Free stuck at cap: "Manage flows" must return to same FlowDetail after deletion
- Paths list incorrect with merges: path derivation must support multiple incoming edges

---

## Cross-Reference Summary

### Dependencies (Batch 04 → Prior Batches)
- CH01, CH02, CH03, CH04, CH06, CH07, CH08 → Batch 01/02
- CH09, CH10, CH11 → Batch 03

### Dependencies (Batch 04 → Future Batches)
- CH17 (Sharing) → Batch 05
- CH18 (Inbox) → Batch 05
- CH19 (Import Conflict Resolution) → Batch 05
- CH20, CH21, CH22 (Practice Mode) → Batch 06
- CH23, CH24 (Gameplans) → Batch 06
- CH25 (Paywalls) → Batch 07
- CH28 (Offline/Sync) → Batch 07
- CH29 (Data Storage & Limits) → Batch 07
- CH30 (Warning Ladder) → Batch 07
- CH31 (Error States) → Batch 07
- CH32 (Accessibility) → Batch 08
- CH33 (Analytics) → Batch 08

### Key Locked Decisions (Batch 04)
| Decision | Value | Owner |
|----------|-------|-------|
| Max outgoing branches per node | 10 | CH12/CH13 |
| Cycles allowed | No | CH13 |
| Free saved flow cap | 2 | CH08 (referenced) |
| Free practice credits | 3/month | CH08 (referenced) |
| Practice only on saved flows | Yes | CH00 (locked) |
| Uploads shareable | No (private-only) | CH29 (referenced) |
| Self-loops | Forbidden | CH13 |
| Flow belongs to max folders | 1 | CH15 |
| Folder deletion deletes flows | No | CH15 |

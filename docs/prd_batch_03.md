# PRD Batch 03: Move Library System (CH09-CH11)

**Processed:** 2026-01-05
**Chapters:** CH09, CH10, CH11
**Status:** Pending Approval

---

## CH09 - Default Move Library System

### Overview
CH09 defines the default move library that ships with Handz V1, enabling immediate usability without requiring users to create custom moves. The library is organized into selectable Move Packs across striking disciplines, with style-neutral naming and no technique coaching content.

### Key Requirements

#### Core Principles (Non-Negotiable)
- **Instant usability:** Users can build flows immediately using shipped moves
- **Style neutrality:** Default moves avoid style-specific technique claims
- **Terminology flexibility:** Similar-looking strikes exist as separate moves (e.g., "Teep" and "Push Kick" are distinct canonical moves)
- **Progressive complexity:** Beginners use basics; advanced users add nuance via CH10-CH11
- **Organization over explanation:** No technique coaching content ships in V1
- **Reversible and user-owned:** Users can modify packs and override names/aliases

#### Default Library Architecture
Three-tier structure:
1. **Canonical Catalog:** Static list shipped with app (~120 core moves target), updatable via app updates
2. **Active Move Set (per user):** Subset of catalog moves user chooses to actively use
3. **Custom Moves (per user):** User-created moves (CH11), gated by plan state (CH08)

#### Move Data Fields (V1)
- `canonical_id` (string/UUID) - stable identifier
- `display_name` (string) - default name shown in UI
- `disciplines` (array) - pack membership/filtering
- `categories` (array) - Punch/Kick/Defense/etc.
- `targets` (array) - Head/Body/Legs
- `range` (enum) - Long/Mid/Close
- `side_default` (enum) - Lead/Rear/Either
- `tags` (array) - additional filtering tags
- `description` (string) - **shipped empty in V1**

#### Move Packs (V1)
- **Mixed Striking Essentials** - default for Guest, suggested for new users
- **Boxing Essentials** - punches, basic defense, boxing footwork
- **Kickboxing Essentials** - punches + kicks + checks + basic defense
- **Muay Thai Essentials** - punches, kicks, knees, elbows, checks, clinch-entry basics (names only)
- **Karate/TKD Essentials** - stance-based kicks, side kicks, blitz entries (names only)
- **Defense & Footwork Essentials** - slips, rolls, blocks/guards, checks, pivots, steps, angles, stance switches

#### Pack Rules
- A pack = list of canonical IDs + pack label + optional description
- Moves can belong to multiple packs (overlap allowed/expected)
- Selecting a pack adds all moves to Active Move Set
- Users can remove individual moves after selection
- Users can skip packs and manually choose individual moves (power users)
- Guests receive fixed default pack (Mixed Striking Essentials) only

#### Tag Taxonomy (Shipped)
- **Discipline:** Boxing, Kickboxing, Muay Thai, Karate/TKD, Mixed
- **Category:** Punch, Kick, Knee, Elbow, Defense, Footwork, Clinch/Entry
- **Subtype:** Jab, Cross, Hook, Uppercut; Front Kick, Round Kick, Side Kick; Slip, Roll, Parry, Guard, Check; Pivot, Step, Switch, Angle
- **Target:** Head, Body, Legs (multi-select)
- **Range:** Long, Mid, Close
- **Handedness/Side (optional):** Lead, Rear, Either

#### Onboarding Flow (Account Users)
1. **Step A:** Choose disciplines (1+ or "Mixed / I cross-train")
2. **Step B:** Recommended packs based on selection
3. **Step C:** Optional manual refine ("Review Moves")
4. **Step D:** Done - Active Move Set created, land in app with "Create Flow" CTA

#### Guest Mode Behavior
- Receives Mixed Striking Essentials automatically
- Can browse, build, and experiment
- **Cannot save flows** (even locally)
- **Cannot create custom moves**
- Save attempt triggers account creation prompt (hard gate)
- Persistent non-annoying reminder about account benefits

#### Post-Onboarding Management (Settings > Moves)
- View Selected Packs list
- Add/Remove packs
- "Review individual moves" editor
- Removing a pack only removes moves not in other selected packs AND not user-pinned
- Pinned moves remain regardless of pack changes
- Moves referenced by flows remain in flows even if removed from active set

#### Move Picker UX (Flow Builder)
- Entry points: Add First Move, Add Next Move, Add Branch response, Replace Move
- Layout: Search bar, Category chips, Secondary chips (Target, Range) collapsible, Recents section
- Tap to select and return to builder
- Long-press for quick preview
- Performance: snappy with >150 moves, local in-memory search/filter, debounced input

#### Lead/Rear Handling
- V1 avoids forcing lead/rear into default names
- Optional Side attribute (Lead/Rear/Either) shown in move detail
- Users can duplicate moves into personal variants ("Front Kick (Lead)", "Front Kick (Rear)") via CH11
- "Create variant / split this move" action in move detail screen

### Explicit Non-Goals (V1)
- Technique instructions, coaching descriptions, cue words
- Public move sharing marketplaces or community libraries
- Mandatory stance/lead/rear variants in move names

---

## CH10 - Moves: Canonical IDs, Aliases, Families, Variants

### Overview
CH10 defines the identity system for moves ensuring consistency across default moves, user-created moves, flow nodes, sharing/imports, and analytics even when names differ across arts, gyms, and personal preferences.

### Key Requirements

#### Move Identity Fields
- `move_id`: immutable UUID (primary key)
- `canonical_key` (default moves only): immutable stable string (e.g., `STRIKE.PUNCH.JAB`)
- `display_name`: user-visible label (can be user-preferred for defaults)
- `alias_group_id` (optional): pointer to synonym/alias set
- `family_ids`: list of families move belongs to
- `variant_of_move_id` (optional): parent move reference
- `origin`: enum {default, user, imported_snapshot}

#### Move Record Types
1. **Default (Shipped) Move:** Stable canonical identity, user can customize personal fields via overlay only
2. **User Move (Custom):** Created by user, has stable move_id, may declare variant-of or family membership
3. **Flow-local Move Snapshot (Import only):** Exists inside imported flow when receiver doesn't add to library

#### Display Rules
- Primary label shown is always `display_name`
- Default moves: display_name is default label, can be overridden by user preferred label
- Custom moves: display_name is user-chosen at creation, editable later
- Ambiguous contexts (imports, conflict resolution): show "Family: " and "Also known as: " lines

#### Alias System
- Alias group = set of labels mapping to single canonical concept
- Each group has: `alias_group_id`, `canonical_key`, `labels[]`, `default_label`
- Aliases reduce friction and improve search - NOT for forcing agreement
- **When NOT to use aliases:** When users treat terms as different moves, when distinction matters to planning, when community is split

**V1 Lock:** Teep and Push Kick are **separate canonical moves** (not aliases); may belong to same family.

#### Search Behavior with Aliases
- Search matches `display_name` AND all alias labels
- Results show move using user's preferred `display_name`
- Subtle hint: "Matched: [alias]" only when displayed name differs from query

#### User Preferred Labels (V1)
- "Naming Preferences" screen exists
- Single-select choice per eligible alias group
- Preview shows how labels will appear
- Changes apply instantly across UI
- **Guests cannot personalize** - see default shipped labels only

#### Families & Variants

**Families:**
- Taxonomy node (e.g., Punches > Straight Punches > Jab)
- Move can belong to multiple families
- Families can be nested (tree)
- Purpose: onboarding browsing, library browsing, search fallback

**Variants:**
- Lightweight "this move is a specific version of that move" relationship
- Optional `variant_of_move_id` field
- Primarily for custom moves in V1
- Visible in move detail: "Variant of: [parent]" with tap-to-open
- Example: "Check Hook" as variant-of "Hook"

#### Families vs Aliases
- **Aliases:** collapse names into one concept
- **Families:** group multiple concepts together
- Two separate canonicals can be in same family if closely related
- Example: Teep and Push Kick are separate canonicals but may live under "Front Linear Kicks" family

#### Data Model (Conceptual)

**MoveDefault:**
```
move_id: UUID (immutable)
canonical_key: string (immutable)
default_label: string
alias_group_id?: string
family_ids: string[]
created_by: 'system'
is_deletable: false
```

**MoveUser:**
```
move_id: UUID (immutable)
display_name: string (editable)
origin: 'user'
variant_of_move_id?: UUID
family_ids: string[] (editable)
notes?: string
media?: (link or upload reference)
```

**MoveSnapshot (Import):**
```
snapshot_id: UUID
suggested_canonical_key?: string
suggested_alias_group_id?: string
sender_display_name: string
sender_family_hints?: string[]
sender_variant_of_hint?: string
```

#### Renaming Rules
- Renaming updates `display_name` only; `move_id` unchanged
- Flow nodes referencing move_id automatically show new display_name
- Practice history references move_id, remains intact across renames
- Default move `canonical_key` is never altered

#### Import Conflict Resolution (CH10-owned fields)

Three receiver actions:
1. **Map to existing:** Select existing move_id in library
2. **Add as new:** Create new MoveUser entry
3. **Keep flow-local:** Keep as snapshot inside imported flow only

Mapping rules:
- If sender references default move canonical_key that exists in receiver's library, propose auto-mapping
- If sender uses alias group label, propose mapping to canonical move for that group
- Receiver always has final control

**Media sharing:** Links can be shared; uploads are private-only and must not be shared (CH29 lock)

---

## CH11 - Moves: Custom Moves + Editing + Revert Model

### Overview
CH11 defines how users create and manage custom moves, how edits apply to defaults vs user-defined moves, and how reverting works without breaking flows, practice sets, or imports.

### Key Requirements

#### Owned Decisions (Locked)
- Users can create Custom Moves in personal library for use in flows
- Default moves are canonical; users cannot edit canonical record, only create User Overlay
- Revert supports: per-move, per-flow scoped, library-wide (with confirmation ladder)
- Guests cannot save flows or persist customizations
- No technique descriptions by default; user notes are personal content

#### Move Types
1. **Canonical Move:** Shipped move with canonical ID, immutable to users
2. **Custom Move:** User-created, owned by user account, may declare variant-of
3. **User Overlay:** User customization layer on canonical move (affects only that user)
4. **Flow-local Override:** Scoped customization for one flow node/path only

#### Editing Scope Hierarchy (Resolution Order)
1. Flow-local Override (if present)
2. User Overlay (if present)
3. Base Move (canonical or custom)

This hierarchy prevents accidental global changes and makes revert deterministic.

#### Data Model

**Move (base record):**
```
id: string (canonical ID or UUID)
owner_user_id: string | null (null = canonical)
is_canonical: boolean
name: string
primary_art_tags: string[]
families: string[]
aliases: string[]
variant_of_move_id: string | null
tags: string[]
created_at, updated_at
```

**MoveOverlay (user customization for canonical):**
```
user_id: string
canonical_move_id: string
custom_display_name: string | null
hidden: boolean
custom_tags: string[] | null
custom_notes: string | null
custom_media: (links only) | null
created_at, updated_at
```

**FlowNodeMoveRef:**
```
flow_id: string
node_id: string
move_id: string
local_override: { display_name?, notes?, tags?, media? } | null
resolved_display_name_cache: string (optional, never authoritative)
```

#### Create Custom Move

**Entry Points:**
- Moves Library: "+ New Move"
- Flow Builder move picker: "Create new move"
- Import resolution (CH19): "Create missing move"

**Minimum Fields (fast path):**
- Required: Move Name
- Optional on first save: Art tags, tags, family, variant-of, notes, media

**Progressive Disclosure Sections:**
- A - Identity: name, aliases, language helpers
- B - Classification: art tags, categories, body part tags
- C - Relationships: family membership, variant-of linkage
- D - Personal notes/media: notes, media (links shareable, uploads private-only)

#### Variant Creation (Quick-Create)
1. User selects base move > taps "Create Variant"
2. Prompt: Variant name (required) + "What's different?" (optional note)
3. System stores: `variant_of_move_id = base move id`
4. UI shows: "Variant of: [parent]" with jump-to-parent

#### Editing Moves

**Editing Custom Move (owned):**
- Edits update record directly
- All flows referencing it reflect updates automatically (unless node has local override)

**Customizing Canonical Move (overlay):**
- Cannot edit canonical directly; create/edit MoveOverlay instead
- Entry: "Customize" button on move detail for canonical moves
- Overlay fields (V1): custom display name, hidden toggle, custom tags, custom notes, custom media links
- Overlay never alters canonical ID, family placement, or canonical aliases

**Flow-local Overrides (node-scoped):**
- For cases like "Jab but in this plan means pawing jab"
- Entry: Node Details > "Override Move Details for this node only"
- Supported fields (V1): display name override + node notes override (optionally tags)
- UI badge: "Local" to indicate override is active
- User can remove: "Revert node to move defaults"

#### Revert Model

**Revert Actions:**
- **Revert Overlay:** Deletes user overlay; returns to canonical defaults immediately
- **Revert Local Override:** Clears node's local_override; returns to overlay/base resolution
- **Revert Custom Move Edit:** "Undo" toast for recent edits; version history placeholder for later

**Revert UX Requirements:**
- Every customization screen has visible "Revert" action
- Confirmation only if removing substantial user content (notes/media) - warning ladder style
- Copy explicit about scope: "Revert this move in your library" vs "Revert only this node"

**Safety Rails:**
- Info affordance: "Why does this move look different?" explaining resolution order
- Entry point for mastery-related labeling downgrade (owned by CH23)

#### Deletion & Tombstones
- If custom move is referenced by flow node, deletion shows options:
  - A) Cancel
  - B) Replace references (user selects replacement move)
  - C) Delete anyway (creates tombstone)
- Tombstone behavior: Node renders as "Deleted Move", retains flow-local override text

#### Guest Rules (V1)
- Cannot save flows or persist custom moves (global lock)
- Create attempt shows conversion gate: "Create an account to save your library..."
- Guest editing stores temporary in-memory draft only until app close
- No offline persistence for guests

#### Import Interactions (CH17-CH19)
- Missing move in import: receiver can create quickly via Section 3 flow
- Custom overlays/notes/media on canonical: receiver chooses scope
  - Keep only inside imported flow (node local overrides)
  - Add as overlay in their library (MoveOverlay)
- Uploads not shareable; receiver sees: "Media not shared (private upload)"

#### Edge Cases
- Teep vs Push Kick: both remain available; user can hide one via overlay
- Rename to existing alias name: warning shown, not blocked
- Variant chains (A > B > C): UI shows parent chain breadcrumb; cap display depth
- Hidden move in existing flow: still renders; hidden only affects pickers/search

---

## Open Questions / Placeholders

### CH09-Owned
| Placeholder | Owner | Options | Default | Decide-by |
|-------------|-------|---------|---------|-----------|
| Default_Catalog_Final_List | CH09 | (A) ~120 essentials, (B) 150 with more defense/footwork, (C) 90 ultra-basics | A | Before App Store submission |
| Pack_Composition_Tuning | CH09 | (A) fewer packs broader, (B) more packs narrower | A | Before onboarding copy lock (CH15) |

### CH10-Owned
| Placeholder | Owner | Options | Default | Decide-by |
|-------------|-------|---------|---------|-----------|
| Search_Keywords_Per_Move | CH10 | none, minimal synonyms, expanded synonyms | minimal synonyms | Before QA pass on move search |
| Finalize which alias groups ship in V1 | CH09/CH10 | TBD | TBD | TBD |
| Variant-of exposure during V1 onboarding | CH10 | During onboarding / Only in custom move creation | TBD | TBD |

### CH11-Owned
| Placeholder | Owner | Options | Default | Decide-by |
|-------------|-------|---------|---------|-----------|
| Custom move count cap per plan | CH08/CH30 | None / Soft cap / Hard cap | None | TBD |
| Which custom move fields are shareable (privacy) | CH17/CH19/CH29 | (A) minimal, (B) include notes, (C) include media links only | A | TBD |
| Flow-local override support scope | CH12/CH13/CH14 | Which fields can be overridden | display name + notes only | TBD |
| Variant creation UX | CH10/CH11 | quick-create vs full editor | quick-create with optional deep editor | TBD |

### Other Placeholders Referenced
| Placeholder | Owner | Options | Default | Decide-by |
|-------------|-------|---------|---------|-----------|
| Recents_Count_N | CH12 | 8/12/20 | 12 | During performance testing |

---

## Cross-References

### CH09 References
- **Depends on:** CH03, CH07, CH08, CH10, CH11, CH29
- **Related:** CH05, CH12, CH13, CH14, CH17, CH18, CH19, CH30

### CH10 References
- **Depends on:** CH00, CH03, CH09, CH08
- **Related:** CH11, CH19, CH05, CH13, CH15

### CH11 References
- **Depends on:** CH03, CH08, CH09, CH10, CH29, CH30
- **Related:** CH12, CH18, CH19, CH20-CH22, CH31

---

## Ambiguities & Flags

### Vague/Non-Committal Language
1. **CH09 Section 6.1:** Range and Handedness/Side tags described as "best-effort defaults" - criteria for assignment not specified
2. **CH09 Section 8.3:** "may suggest" creating lead-only variant when user repeatedly sets Side=Lead - trigger threshold undefined
3. **CH10 Section 4.5:** Guest-to-account conversion offers "one-time" naming preferences step - exact trigger and skippability unclear
4. **CH11 Section 5.1:** Version history for custom move edits described as "placeholder for later" - not scoped for V1

### Explicit TBD/Placeholders
1. Final move list contents (~120 target, not finalized)
2. Exact alias groups shipping in V1
3. Custom move count caps per plan
4. Which custom move fields are shareable by default
5. Variant creation UX (quick-create vs full editor final decision)

### Potential Conflicts
1. **Guest Warning Copy:** CH09 states copy is "owned by CH15" but requires specific concepts - verify CH15 aligns
2. **Move Picker Screen:** CH09 states "inventoried in CH05 and used in CH12" - verify consistency across chapters

### Implementation Dependencies
1. Move Picker performance requires in-memory filtering - may need optimization for offline-first architecture
2. Tombstone behavior on deletion requires careful flow rendering handling
3. Import snapshot identity fields owned by CH10 but full flow owned by CH19

---

## Acceptance Test Summary

### CH09 Key Tests
- Onboarding pack selection creates correct union of moves
- Guest receives Mixed Striking Essentials without selection
- Guest save blocked with account prompt
- Pack overlap handling (shared moves remain if in other pack or pinned)
- Teep and Push Kick searchable and addable as distinct moves
- Move picker speed (<150ms for 150 moves)
- Flow integrity after move removal from active set

### CH10 Key Tests
- Preferred label change updates display_name without changing move_id
- Variant-of shows in header with tap-to-open parent
- Rename updates all flow nodes automatically
- Search finds moves by alias label, shows "Matched:" hint
- Import offers Map/Add New/Keep Flow-Local with upload warning

### CH11 Key Tests
- Custom move creation from all entry points
- Canonical customize creates overlay, not edit
- Node local override removal triggers resolution hierarchy
- Delete custom move with references shows Replace/Cancel/Delete
- Import custom notes as flow-only stores in node overrides
- Hidden move still renders in existing flows
- Guest save attempts trigger account gate, no persistence

---

**End of Batch 03**

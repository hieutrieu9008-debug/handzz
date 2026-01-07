# PRD Batch 02A2: Design System

**Source:** CH06 - Design System (R1)  
**Generated:** 2026-01-05  
**Status:** Awaiting Approval

---

## Chapter Overview

CH06 defines the complete visual language and interaction feel for Handz V1, covering:

- Design principles and north star vision
- Color system with tokenized values
- Typography system (two-font approach)
- Spacing, layout, and shape tokens
- Motion and haptics rules
- Component library specifications
- Handz-specific UI components
- Standard UI states (loading, empty, error)
- Content style guidelines

The chapter emphasizes **token-based design** (no hard-coded values), **progressive disclosure**, **fast UI**, and **system consistency**. It is written to enable implementation teams (including AI coding agents) to build consistent UI without guessing or inventing styles screen-by-screen.

---

## Key Requirements

### 1. Design North Star

**Core Vibe:** Minimal + modern (Notion-like clarity), slightly playful (Duolingo-like friendliness), progress/game oriented (SupaBetter-like momentum) without looking generic.

**Vibe Keywords:** clean, punchy, confident, fast, friendly, focused

**Striking Context:** Use striking metaphors subtly (chips, tags, streaks, "round" language), but avoid gritty/aggressive visual tropes.

#### 1.1 Design Principles (Non-Negotiable)

1. **Progressive disclosure:** Show only what's needed now; advanced detail always available one tap away
2. **Fast UI:** Most interactions should feel instantaneous; avoid heavy transitions and long loaders
3. **One primary action per screen:** Main CTA is visually dominant; secondary actions are quiet
4. **Readable at a glance:** Large headings, spacious line-height, strong contrast; no dense walls of text
5. **System consistency:** Reuse same components and states everywhere; do not hand-style one-off UIs

---

### 2. Color System

**Architecture:** Light, clean base (white/off-white) with bold, high-energy orange as brand anchor. All colors defined as tokens; screens must reference tokens only (no hard-coded hex values).

#### 2.1 Brand Anchor (LOCKED)

**Primary Orange:** `#FF7F11`  
- Used in brand accents and app icon background
- **Immutable:** Remains the anchor unless CH06 is revised (R2+)

#### 2.2 Light Theme Tokens (Default)

| Token | Value | Usage |
|-------|-------|-------|
| `color.brand.primary` | #FF7F11 | Primary CTA, active states, key highlights |
| `color.brand.primaryHover` | #E86F05 | Pressed state for primary CTA (iOS: pressed/active) |
| `color.brand.primarySoft` | #FFE6D3 | Subtle highlight backgrounds (badges, callouts) |
| `color.text.primary` | #0F172A | Main text |
| `color.text.secondary` | #475569 | Secondary / helper text |
| `color.text.tertiary` | #64748B | Placeholder / meta text |
| `color.surface.base` | #FFFFFF | Main surface |
| `color.surface.alt` | #F8FAFC | Alt surface (lists, grouped backgrounds) |
| `color.surface.elevated` | #FFFFFF | Cards / sheets; use elevation for separation |
| `color.border.subtle` | #E2E8F0 | Hairline borders |
| `color.border.strong` | #CBD5E1 | Stronger dividers / emphasized outlines |
| `color.action.disabledBg` | #E5E7EB | Disabled control background |
| `color.action.disabledText` | #9CA3AF | Disabled label |
| `color.semantic.success` | #16A34A | Success states |
| `color.semantic.warning` | #F59E0B | Warning states |
| `color.semantic.danger` | #EF4444 | Destructive / error states |
| `color.semantic.info` | #2563EB | Informational states / links |

#### 2.3 Usage Rules

1. **Primary Orange for one thing at a time:** The primary CTA OR the primary focus element on screen
2. **Never use red/orange together** as two competing accents on same screen; danger red reserved for errors/destructive actions
3. **Backgrounds must remain calm:** Prefer `color.surface.*` and `color.brand.primarySoft` for subtle emphasis
4. **Always meet accessibility contrast:** Body text on surface should be WCAG AA or better; if token fails, adjust token values in CH06 (not per-screen overrides) - see CH32
5. **Use semantic tokens for status:** success/warning/danger/info - never invent new status colors in a feature

---

### 3. Typography

**System:** Two-font approach - bold display face for headings, clean sans for body/UI. Font usage tokenized so it can be swapped later if needed.

#### 3.1 Font Families (LOCKED for V1)

- **Display / headings:** Bebas Neue (all-caps friendly, punchy)
- **Body / UI:** Nunito Sans (high readability, friendly, modern)
- **System fallback:** iOS San Francisco (SF Pro) if custom fonts fail to load - **do not block app usage if fonts fail**

#### 3.2 Type Scale (iOS points)

| Token | Spec | Usage |
|-------|------|-------|
| `type.display.h1` | 34/38, tracking +1% | Hero headings (rare; onboarding only) |
| `type.display.h2` | 28/32, tracking +1% | Screen titles (most top-level screens) |
| `type.display.h3` | 22/26, tracking +0.5% | Section headers inside a screen |
| `type.ui.title` | 17/22 | Primary labels (cards, list headers) |
| `type.ui.body` | 15/20 | Standard reading text |
| `type.ui.bodySmall` | 13/18 | Helper text, captions |
| `type.ui.meta` | 11/14 | Timestamps, subtle counters |
| `type.ui.mono` | 13/18 | Optional: IDs/diagnostics in debug builds only (see CH36) |

#### 3.3 Text Rules

1. **Headings use Bebas Neue:** Avoid using it for paragraphs or long labels
2. **Body text uses Nunito Sans:** Maintain generous line height for readability
3. **Sentence case for UI labels** (not ALL CAPS), except display headings that are intentionally styled
4. **Numbers:** Use tabular numbers where supported for timers and counters (Practice mode)
5. **Do not truncate critical labels:** If truncation unavoidable, provide secondary line or tooltip

---

### 4. Spacing & Layout

**Grid:** Simple 4pt spacing grid. Padding and component sizes standardized for consistency.

#### 4.1 Spacing Tokens

| Token | Value | Usage |
|-------|-------|-------|
| `space.1` | 4pt | Micro spacing |
| `space.2` | 8pt | Tight spacing (chips, small gaps) |
| `space.3` | 12pt | Standard spacing between related elements |
| `space.4` | 16pt | Default screen padding (most screens) |
| `space.5` | 24pt | Section breaks |
| `space.6` | 32pt | Major breaks (between major sections) |

#### 4.2 Shape Tokens

| Token | Value | Usage |
|-------|-------|-------|
| `radius.sm` | 10pt | Chips, small cards |
| `radius.md` | 14pt | Buttons, inputs, cards |
| `radius.lg` | 18pt | Modals, large sheets |
| `border.hairline` | 1pt | Standard border |
| `shadow.elev1` | — | Subtle card shadow; use sparingly, prefer borders |
| `shadow.elev2` | — | Bottom sheets / modals |

#### 4.3 Screen Layout Rules

1. **All screens are portrait-only** (see CH02)
2. **Default content inset:** 16pt left/right; top inset respects Safe Area
3. **Primary CTA placement:** Bottom sticky bar for creation flows (builder, practice setup) when appropriate
4. **Lists:** Use grouped sections with subtle separators; avoid heavy dividers
5. **Use whitespace to separate ideas:** Never cram multiple dense controls into one viewport without grouping

---

### 5. Iconography & Illustration

**Approach:** Prefer SF Symbols on iOS for speed and consistency. Use filled icons for active states and outline icons for inactive states.

#### 5.1 Icon Rules

- **Stroke weight:** Match SF Symbols default; avoid mixing line styles
- **Icon size:** 20pt for toolbar, 24pt for primary actions; 16pt for inline
- **Color:** 
  - Inactive uses `color.text.tertiary`
  - Active uses `color.brand.primary` or `color.text.primary` depending on context
- **Illustration:** Avoid illustration-heavy onboarding in V1; keep it clean. Use small friendly mascot-style touches only if they do not add layout complexity

#### 5.2 Brand Mark Usage (Logo / App Icon)

**App Icon:** Existing black-and-white Handz mark (no color fill) on Primary Orange background (#FF7F11)
- No additional text in app icon
- Maintain generous padding so mark breathes inside iOS icon masks

---

### 6. Motion & Haptics

**Philosophy:** Motion should feel fast and functional. Use subtle animation to communicate state changes. Avoid long transitions. Use haptics to reinforce key actions.

#### 6.1 Motion Tokens

| Token | Value | Usage |
|-------|-------|-------|
| `motion.fast` | 120ms | Button press feedback, micro transitions |
| `motion.base` | 200ms | Expand/collapse, modal fade/slide |
| `motion.slow` | 320ms | Rare: onboarding transitions only |
| `easing.standard` | — | Ease-out for entrances; ease-in-out for toggles |

#### 6.2 Haptic Rules (iOS)

- **Light impact:** Add move / add branch / reorder confirm
- **Success notification:** Flow saved, session completed, upgrade success
- **Warning notification:** Hitting soft cap, offline warning
- **Error notification:** Save failed, invalid action blocked
- **Never overuse haptics:** No haptic on every scroll/tap

---

### 7. Component Library (V1)

**Principle:** All UI built from shared component set. If new component required, add to CH06 before implementing one-off UI.

#### 7.1 Buttons

**Primary Button:**
- Filled, background `color.brand.primary`, label `color.surface.base`
- `radius.md`, height 48pt
- States: default, pressed (`primaryHover`), disabled, loading (spinner), success (temporary check)

**Secondary Button:**
- Outline (`border.subtle`), background transparent or `surface.alt`
- Label `color.text.primary`

**Tertiary Button:**
- Text-only, label `color.brand.primary`
- Used for low-priority actions

**Destructive:**
- Text or outline using `color.semantic.danger`
- Confirm dialogs required for destructive actions (see CH31)

**Disabled:**
- Use `color.action.disabledBg` and `color.action.disabledText`
- Keep label readable

#### 7.2 Inputs

**Text Field:**
- Height 44pt, `radius.md`, `border.subtle`, background `surface.base`
- Placeholder `text.tertiary`

**Multiline:**
- Auto-expands up to 6 lines before scrolling
- Always show remaining character count only if explicit caps added

**Search Bar:**
- Integrated icon left, clear button right
- Uses `surface.alt` background

**Dropdown / Picker:**
- Uses bottom sheet on iOS
- Selected value appears as pill-row input

**Validation Rules:**
- Inline error text (`text.semantic.danger`) appears below field; border becomes danger
- Do not block typing with modal alerts; errors shown inline unless submission attempted
- Success state may show subtle checkmark for fields that validate asynchronously (e.g., username)

#### 7.3 Chips, Tags, and Pills

**Tag Pill:**
- Rounded `radius.lg`, background `brand.primarySoft`, text primary
- Used for filters and quick labels

**Status Pill:**
- Semantic colors for success/warning/danger/info
- Small meta text

**Branch Label Pill:**
- Used on edges or below nodes
- Max 24 chars, then wraps to 2 lines

#### 7.4 Cards and List Rows

**Cards:**
- `surface.elevated`, `border.subtle`, `radius.md`
- Internal padding `space.4`

**List rows:**
- Full-width tap target (min 44pt)
- Use divider `border.subtle` between rows

**Swipe actions:**
- Use native iOS patterns
- Destructive swipe is red

#### 7.5 Modals / Bottom Sheets

**Bottom sheets:**
- For pickers, add-move, add-branch, reorder selection
- `radius.lg`, drag handle visible

**Full-screen modals:**
- Only when user is in focused creation flow (e.g., move editor)

**All modals must support:**
- Cancel, close (X), and swipe-to-dismiss where appropriate
- Keyboard behavior: fields must remain visible; auto-scroll to focused input

#### 7.6 Toasts / Snackbars

**Usage:**
- Confirm non-blocking actions: "Saved", "Link copied", "Added to library"

**Specs:**
- Position: bottom above tab bar
- Auto-dismiss 2.5s
- Include Undo only when meaningful

**Error toast:**
- Used only for transient network errors
- Blocking errors use inline or dialogs

---

### 8. Handz-Specific UI Components

**Note:** These components unique to Handz and must be consistent everywhere they appear.

#### 8.1 Move Node Card (Flow Builder)

**Shape:**
- Rounded rectangle `radius.md`
- Min width 120pt, height auto (2 lines max by default)

**Contents:**
- Move name (title)
- Optional micro-tags row (stance, range) if enabled later

**Node menu:**
- Tap node opens quick actions (Replace, Add Next, Add Branch, Delete)

**Selected state:**
- Outline with `brand.primary` and subtle glow
- **Do not fill node with orange (too loud)**

**Drag:**
- Long-press to drag
- Show haptic on drag start
- Keep 60fps target

#### 8.2 Edge / Connector UI

- **Solid edge** = linear continuation
- **Dashed edge** = conditional branch (see CH12)
- **Edge label** uses Branch Label Pill; position near midpoint; auto-avoid overlaps where possible
- **Tapping edge** opens Sequence Detail Editor entry point (see CH14) if enabled for that edge

#### 8.3 Canvas Controls

- **Zoom in/out buttons** bottom-right
- Show current zoom % on long-press
- **Pan:** one-finger drag; select node by tap
- **Lasso selection NOT required for V1**
- **Mini-map optional:** If implemented, must be toggleable and not always visible

#### 8.4 Practice Timer Card

**Timer:**
- Uses large numerals (`type.display.h2` or h3 depending on screen)

**Primary controls:**
- Pause/Resume; secondary: Skip; destructive: End (requires confirm)

**Rest screen:**
- Uses calm background
- Highlights upcoming sequence name

**Completion moment:**
- Subtle confetti micro-animation allowed (<= 1s)
- **Must not lag older devices**

#### 8.5 Paywall & Upsell Surfaces

**Paywall:**
- Calm sheet with clear benefits and one primary CTA
- **Avoid dark patterns**

**Benefits:**
- Use check-list cards
- 3–6 bullets max per screen
- Deeper scientific detail goes to CH26

**Always include:**
- Restore purchases, manage subscription, privacy link

---

### 9. Standard UI States

#### 9.1 Loading

- **Use skeleton loaders** for lists/cards; avoid spinners for list fetches
- **Use spinner only** for blocking actions (saving, upgrading)
- **Loading must never block navigation back**

#### 9.2 Empty States

**Structure (all empty states include):**
1. Short headline
2. One-sentence explanation
3. One primary CTA

**Visual:**
- May use small icon
- No large illustrations required for V1

**Tone:** Encouraging, not shamey

#### 9.3 Error States

**Display patterns:**
- **Inline** for field errors
- **Toast** for transient network issues
- **Modal** only for destructive or blocking flows

**Error copy must:**
- Say what happened and what to do next
- Avoid "Something went wrong" alone
- Include Retry whenever possible

---

### 10. Content Style Guide

**Voice:** Confident, coach-like, supportive. Avoid cringe/corny fight talk.

**Writing Rules:**
- **Be specific:** "Save this flow to practice later" instead of "Proceed"
- **Use plain language:** "Branch" can be explained as "If they do X, you do Y"
- **Keep labels short:** Explanations go in helper text or onboarding overlays
- **Never promise outcomes as guarantees:** Frame as tools and principles (see CH26)

---

### 11. Implementation Guardrails (Non-Negotiable)

1. **All colors, typography, spacing referenced via tokens** (central theme file)
2. **No custom per-screen styling** unless it becomes a component and is documented in CH06
3. **All new UI patterns must be added to this chapter before building**
4. **Accessibility is not optional:** Minimum tap targets and contrast must be met (see CH32)

---

## Open Questions / Ambiguities / Placeholders

### From Section 12: "Placeholders / Items to Decide Later"

**Note:** Anything not explicitly locked is a placeholder. Defaults below are recommended but may change in CH06_R2.

1. **PLACEHOLDER: Dark mode support**
   - **Options:** Off in V1 / Limited / Full
   - **Default:** Off in V1
   - **Decide-by:** Before App Store submission
   - **Impact:** If implemented, requires full token set for dark theme

2. **PLACEHOLDER: Illustration/mascot style**
   - **Options:** None / Subtle / Full mascot
   - **Default:** Subtle icons only
   - **Impact:** Onboarding and empty state visuals

3. **PLACEHOLDER: Sound design**
   - **Options:** None / Subtle / More game-like
   - **Default:** None
   - **Impact:** Audio feedback for actions

4. **PLACEHOLDER: Confetti / celebration intensity**
   - **Options:** None / Subtle / Medium
   - **Default:** Subtle
   - **Impact:** Practice session completion, upgrade success
   - **Constraint:** Must not lag older devices (mentioned in 8.4)

### Technical Specifications Needed

5. **Dynamic Type Scaling**
   - Accessibility mention in section 11 and CH32 cross-reference
   - **Question:** How should type tokens scale with iOS Dynamic Type?
   - **Impact:** All typography components need responsive scaling behavior

6. **Skeleton Loader Specs**
   - Section 9.1 requires skeleton loaders for lists/cards
   - **Specification needed:** Animation timing, shimmer effect, exact styling
   - **Impact:** Loading state implementation

7. **Canvas Interaction Details**
   - Section 8.3 mentions "60fps target" for drag
   - **Question:** What are the performance budgets for canvas operations?
   - **Question:** How many nodes before performance optimization required?
   - **Cross-reference:** CH12 (Flow Builder Interaction Model)

8. **Tooltip Implementation**
   - Section 3.3 mentions "provide tooltip" for truncated text
   - **Specification needed:** Tooltip styling, timing, trigger behavior
   - **Impact:** Long text handling across app

9. **Multi-line Expansion Logic**
   - Section 7.2 states "auto-expands up to 6 lines before scrolling"
   - **Question:** What's the exact line-height calculation for 6 lines?
   - **Question:** Should this be configurable per context?

10. **Character Count Display**
    - Section 7.2: "show remaining character count only if we add explicit caps"
    - **Question:** Which fields will have character caps?
    - **Cross-reference:** CH29 (Data Storage and Limits)

### Design Token Completeness

11. **Missing Shadow Specifications**
    - `shadow.elev1` and `shadow.elev2` have no actual values
    - **Specification needed:** iOS shadow offset, blur, opacity, color
    - **Impact:** Card and modal elevation appearance

12. **Motion Easing Curves**
    - `easing.standard` mentioned but not fully specified
    - **Specification needed:** Exact cubic-bezier values or iOS curve names
    - **Impact:** Animation consistency

13. **Success Button State Duration**
    - Section 7.1 mentions "success (temporary check)"
    - **Question:** How long does success state display before reverting?
    - **Impact:** Button state management

14. **Undo Toast Behavior**
    - Section 7.6: "include Undo only when meaningful"
    - **Question:** What defines "meaningful" undo?
    - **Question:** How long does undo remain available?
    - **Impact:** Toast functionality and data architecture

### Cross-Platform Considerations

15. **SF Symbols Fallback**
    - Heavy reliance on SF Symbols for iOS
    - **Question (for Android later):** What's the icon strategy for Android?
    - **Note:** Not a V1 blocker (iOS first per context.md)

16. **Custom Font Loading Failure**
    - Section 3.1: "do not block app usage if fonts fail"
    - **Question:** Should there be telemetry to track font loading failures?
    - **Impact:** Font delivery strategy, debug visibility

---

## Cross-References to Other Chapters

### Foundation Chapters
- **CH00 (Manifest):** Source of chapter structure
- **CH01 (Product Definition):** Product vision alignment
- **CH04 (IA & Nav):** Navigation patterns that design system must support
- **CH05 (Screen Inventory):** All 58 screens must use this design system

### Feature-Specific Design
- **CH12 (Flow Builder Interaction Model):** Move Node Card and Edge UI specs (section 8.1-8.2)
- **CH20-CH22 (Practice Mode):** Practice Timer Card specs (section 8.4)
- **CH25 (Monetization):** Paywall & Upsell surfaces (section 8.5)

### Quality & Compliance
- **CH31 (Error States):** Error state patterns referenced in sections 7.1, 9.3
- **CH32 (Accessibility):** Contrast requirements, tap targets, dynamic type (sections 2.3, 11)

### Supporting Content
- **CH26 (Scientific Claims):** Deeper scientific detail for paywall benefits (section 8.5)
- **CH36 (Troubleshooting):** Debug builds and mono font usage (section 3.2)

---

## Explicit Assumptions Detected

1. **iOS-First Design System**
   - All specs are iOS-native (SF Symbols, SF Pro fallback, iOS haptics)
   - No Android-specific considerations in V1

2. **Light Theme Only in V1**
   - Dark mode explicitly off in V1 (placeholder section 12)
   - All token values are light theme

3. **Single Language Support**
   - No mention of RTL (right-to-left) layout considerations
   - Text direction assumed LTR

4. **Token-Based Architecture is Mandatory**
   - Hard-coded values forbidden (section 11)
   - Tokens are single source of truth

5. **Performance First**
   - 60fps target for drag operations (section 8.1)
   - Avoid heavy transitions (section 1.1)
   - Skeleton loaders over spinners (section 9.1)

6. **Progressive Disclosure Philosophy**
   - One primary action per screen (section 1.1)
   - Advanced detail one tap away
   - No dense walls of text

7. **Minimal Illustration Approach**
   - Keep onboarding clean (section 5.1)
   - Small friendly touches only
   - No illustration-heavy UI

8. **Native iOS Patterns Preferred**
   - SF Symbols for icons (section 5.1)
   - Native swipe actions (section 7.4)
   - Bottom sheets over custom modals (section 7.5)

9. **Accessibility Non-Negotiable**
   - Minimum 44pt tap targets (sections 7.4, 11)
   - WCAG AA contrast (section 2.3)
   - See CH32 for full requirements

10. **Haptics for Key Moments Only**
    - No haptic on every scroll/tap (section 6.2)
    - Reserved for meaningful actions

---

## Acceptance Test Checklist

### Given/When/Then Tests (from Section 13)

**Test 1: Primary CTA Dominance**
- **Given:** Any screen
- **When:** A primary CTA is present
- **Then:** Only one element uses `color.brand.primary` prominently and all secondary CTAs are visually quieter

**Test 2: Form Validation Error Display**
- **Given:** A form field validation error
- **When:** User submits invalid input
- **Then:** Field border and helper text use `semantic.danger` and error copy explains the fix

**Test 3: Loading State Navigation**
- **Given:** A list screen
- **When:** Data is loading
- **Then:** Skeleton rows appear and screen remains usable (back navigation works)

**Test 4: Modal Dismissal**
- **Given:** A modal
- **When:** User swipes down or taps Cancel
- **Then:** Modal dismisses without losing already-saved data

**Test 5: Flow Builder Selected Node**
- **Given:** Flow builder canvas
- **When:** A node is selected
- **Then:** Selected outline uses `brand.primary` and does not rely on color fill alone

**Test 6: Minimum Tap Target**
- **Given:** Any tappable control
- **When:** Measured
- **Then:** It meets minimum 44pt tap target

### Implementation Checklist (from Section 13)

- [ ] **Theme tokens exist for:** colors, typography, spacing, radius, borders, motion
- [ ] **Core components implemented:** Button, TextField, Chip/Pill, Card, ListRow, BottomSheet, Toast
- [ ] **Handz-specific components implemented:** Move Node Card, Branch Label Pill, Canvas Controls
- [ ] **Default light theme applied** across all screens (no mixed ad-hoc hex values)
- [ ] **Accessibility checks:** contrast, tap target, dynamic type scaling behaviors defined

---

## Replit Build Prompt (from Section 14)

```
You are implementing Handz V1. Read HZ-V1-CH00 and HZ-V1-CH06. Implement ONLY CH06.

Goal: Create a reusable design system and component library in the codebase so all later screens can reuse it without guessing.

Tasks:
1) Create a Theme/Tokens module:
   - Define tokens for colors, typography, spacing, radius, borders, and motion exactly as listed in CH06.
   - Export both raw tokens and semantic helpers (e.g., colors.text.primary).

2) Build core components:
   - Button (Primary/Secondary/Tertiary/Destructive/Disabled/Loading)
   - TextField (single + multiline) with inline validation
   - SearchBar
   - Chip/Pill (Tag/Status/Branch label)
   - Card and ListRow
   - BottomSheet modal pattern
   - Toast/Snackbar

3) Build Handz-specific components:
   - Move Node Card (selected state, quick actions hook, draggable-ready)
   - Branch Label Pill
   - Canvas Controls (zoom buttons placeholder)

4) Add basic a11y:
   - Ensure 44pt tap targets
   - Use accessible labels
   - Avoid low-contrast text

Rules:
- Do not hardcode hex values in components; reference tokens.
- If you must invent anything not in CH06, write it into a PRD Assumptions comment block and STOP.

Deliverables:
- theme/tokens.ts (or .js)
- components/ directory with the components above
- a demo screen that renders every component/state for QA.
```

---

## Troubleshooting Notes (from Section 15)

- **UI looks inconsistent across screens:** Search for hard-coded styles; replace with tokens
- **Text truncates unexpectedly:** Verify lineHeight and allow flex wrapping; avoid fixed heights on text containers
- **Contrast feels weak:** Adjust token values in CH06 (do not patch per screen)
- **Performance drops:** Ensure shadows are not overused; prefer borders and flat surfaces

---

## Summary

CH06 provides a comprehensive, token-based design system that serves as the visual foundation for Handz V1. The chapter successfully:

✅ **Defines complete visual language** - Colors, typography, spacing, motion, all tokenized  
✅ **Specifies full component library** - 7 core components + 5 Handz-specific components  
✅ **Establishes clear design principles** - Progressive disclosure, fast UI, one primary action per screen  
✅ **Provides implementation guidance** - Replit prompt, acceptance tests, troubleshooting  
✅ **Enforces consistency** - Token-based architecture, no hard-coded values  

**Strengths:**
- Very detailed token specifications with exact values
- Clear usage rules and anti-patterns
- Handz-specific components well-documented
- Strong accessibility foundation (44pt targets, WCAG AA)

**Primary blockers for full implementation:**
1. **Missing shadow specifications** (elev1, elev2 have no values)
2. **Dark mode decision** (placeholder - needs decision before App Store)
3. **Easing curve details** (easing.standard needs exact values)
4. **CH32 accessibility specs** (referenced but not yet reviewed)

**Recommendation:** Approve this batch. The design system is production-ready for light theme iOS implementation. Dark mode and other placeholders can be deferred to post-V1 or clarified in CH06_R2. Proceed with component library implementation per Replit prompt.

---

**End of PRD Batch 02A2**

# Decision Register: Flow Builder (Domain 01)

Generated: 2026-01-05
Source: PRD Batch 04 (CH12–CH16)
Status: Flagged items — awaiting resolution

---

## 1. Unresolved Decisions

### 1.1 Blocker

| ID | Item | Options | Default | Owner | Source |
|----|------|---------|---------|-------|--------|
| FB-B01 | Max nodes/edges per flow per plan | TBD | TBD | CH30/CH29 | CH13 §Open Questions |
| FB-B02 | Soft cycles for drill loops | TBD | TBD | CH20/CH21 | CH13 §Open Questions |
| FB-B03 | Show sequence notes during active practice | Off / View-only / Edit | View-only | CH21 | CH14 §Open Questions |
| FB-B04 | Export formats and limits | TBD | TBD | CH29/CH25 | CH16 §Open Questions |

### 1.2 Important

| ID | Item | Options | Default | Owner | Source |
|----|------|---------|---------|-------|--------|
| FB-I01 | Min zoom level | TBD | 50% (rec) | CH12 | CH12 §Open Questions |
| FB-I02 | Max zoom level | TBD | 200-250% (rec) | CH12 | CH12 §Open Questions |
| FB-I03 | Default auto-layout spacing | TBD | TBD | CH12 | CH12 §Open Questions |
| FB-I04 | Autosave inactivity interval | TBD | TBD | CH12 | CH12 §12.4 |
| FB-I05 | Performance threshold (nodes) | TBD | ~150 (rec) | CH12 | CH12 §12.9 |
| FB-I06 | Max reference links per sequence | 3 / 5 / 10 | 5 | CH14 | CH14 §Open Questions |
| FB-I07 | Max characters per note field | 500 / 1000 / 2000 | 1000 | CH14 | CH14 §Open Questions |
| FB-I08 | Feint reorder interaction | Up/Down buttons / Drag handle | Up/Down | CH14 | CH14 §Open Questions |
| FB-I09 | Demo flows in library | 1 built-in / None | 1 built-in | CH15 | CH15 §Open Questions |
| FB-I10 | Folder order model | Manual drag / Always by Name | By Name | CH15 | CH15 §Open Questions |
| FB-I11 | Duplicate title rule | "(Copy)" / "Copy 2/3…" / Prompt | "(Copy)" | CH15 | CH15 §Open Questions |
| FB-I12 | Flow title max length | 50 / 60 / 80 | 60 | CH29/CH31 | CH15 §Open Questions |
| FB-I13 | Folder name deduping | Allow / Auto-append (2) / Block | Auto-append | CH15 | CH15 §Open Questions |
| FB-I14 | Swipe actions enabled | Enable / No swipe | Enable | CH15 | CH15 §Open Questions |
| FB-I15 | Share-link daily caps for Pro | TBD | TBD | CH17/CH30 | CH16 §Open Questions |
| FB-I16 | Thumbnail generation approach | TBD | TBD | CH12/CH29 | CH16 §Open Questions |

### 1.3 Nice-to-have

| ID | Item | Options | Default | Owner | Source |
|----|------|---------|---------|-------|--------|
| FB-N01 | Haptic/animation tuning | TBD | TBD | CH12 | CH12 §Open Questions |
| FB-N02 | Edge-label presets (default set) | TBD | TBD | CH10/CH13 | CH13 §Open Questions |
| FB-N03 | Analytics event names | TBD | TBD | CH33 | CH16 §Open Questions |
| FB-N04 | Upsell copy strings | TBD | TBD | CH15 | CH16 §Open Questions |

---

## 2. Placeholders

| ID | Location | Placeholder | Impact | Source |
|----|----------|-------------|--------|--------|
| FB-P01 | CH12 §12.3 | Manual positions behavior on orientation change | Unclear if positions reset or preserved | CH12 L43 |
| FB-P02 | CH12 §12.4 | Autosave interval | No timing defined | CH12 L89 |
| FB-P03 | CH12 §12.9 | Performance targets (~150 nodes) | Only recommendation, no hard spec | CH12 L207-209 |
| FB-P04 | CH13 §13.3 | Soft cycles for drill loops | Blocked on Practice chapters | CH13 L285-286 |
| FB-P05 | CH15 §15.10 | Folder name max length | Listed as PLACEHOLDER (default 40) | CH15 L896 |

---

## 3. Contradictions / Ambiguities

| ID | Issue | Location A | Location B | Severity |
|----|-------|------------|------------|----------|
| FB-C01 | Folder name duplicates: §15.5 says "allow but append (2)" OR "block" as options; §15.10 validation says same with no resolution | CH15 §15.5 (L801) | CH15 §15.10 (L896) | Important |
| FB-C02 | Mini-map: §12.9 says "optional (not required in V1)" but no decision on inclusion | CH12 §12.9 (L204) | — | Nice-to-have |
| FB-C03 | Full undo/redo: §12.7 says "stretch goal" but no decision on V1 inclusion | CH12 §12.7 (L173) | — | Important |
| FB-C04 | Export availability per plan: §16.6 says "TBD (placeholder)" for both Free and Pro | CH16 §16.6 (L1082-1083) | — | Blocker |

---

## 4. Cross-Chapter Dependencies

### 4.1 Blocking Dependencies (Batch 04 needs future batches)

| ID | Blocked Item | Depends On | Batch | Severity |
|----|--------------|------------|-------|----------|
| FB-D01 | Max nodes/edges per plan | CH30 Warning Ladder | Batch 07 | Blocker |
| FB-D02 | Soft cycles for drills | CH20/CH21 Practice Mode | Batch 06 | Blocker |
| FB-D03 | Share link limits | CH17 Sharing + CH30 | Batch 05/07 | Blocker |
| FB-D04 | Offline save queue behavior | CH28 Offline/Sync | Batch 07 | Important |
| FB-D05 | Storage full handling | CH29 Data Storage | Batch 07 | Important |
| FB-D06 | Paywall flows | CH25 Paywalls | Batch 07 | Blocker |
| FB-D07 | Import conflict resolution | CH19 Import Conflict | Batch 05 | Important |
| FB-D08 | Practice entry gating | CH20 Practice Setup | Batch 06 | Blocker |

### 4.2 Internal Dependencies (Within Batch 04)

| ID | Source | Target | Nature |
|----|--------|--------|--------|
| FB-D09 | CH12 (Builder) | CH13 (Data Model) | Builder must respect graph validation rules |
| FB-D10 | CH14 (Sequence Editor) | CH13 (Edge schema) | Transition payload structure |
| FB-D11 | CH15 (Library) | CH16 (Flow Detail) | Flow card → Flow Detail routing |
| FB-D12 | CH16 (Flow Detail) | CH12 (Builder) | Edit CTA → Builder entry |
| FB-D13 | CH16 (Flow Detail) | CH14 (Sequence Editor) | Path detail → Sequence notes |

---

## 5. Edge Cases Requiring Decision

| ID | Scenario | Question | Source | Severity |
|----|----------|----------|--------|----------|
| FB-E01 | Orientation toggle with manual positions | Reset positions or preserve with re-layout? | CH12 §12.3 L43 | Important |
| FB-E02 | Edge deleted while Sequence Editor open | Close editor + toast (spec'd) but no undo | CH14 §14.9 L664 | Important |
| FB-E03 | Branch-stub placeholder in saved (non-draft) flow | Allowed or forced resolution? | CH13 §13.5 L387 | Important |
| FB-E04 | Free user at inbox cap (10) + save attempt | Warning vs block behavior unclear | CH16 §16.6 L1094 | Important |
| FB-E05 | Large flow (>150 nodes) performance degradation | Specific fallback behaviors undefined | CH12 §12.9 L208-209 | Blocker |
| FB-E06 | Path enumeration with complex merges | Performance/display limits undefined | CH16 §16.9 L1162-1166 | Important |
| FB-E07 | Missing canonical move in imported flow | Resolution flow incomplete (defer to CH19) | CH16 §16.9 L1168-1169 | Important |
| FB-E08 | Duplicate edge labels on same node | Allowed or rejected? | CH13 §13.3 L289-290 | Important |

---

## 6. Validation Gaps

| ID | Area | Gap | Source | Severity |
|----|------|-----|--------|----------|
| FB-V01 | Flow title | Max length defined as placeholder (60 default) | CH15 §15.10 | Important |
| FB-V02 | Folder name | Max length defined as placeholder (40 default) | CH15 §15.10 | Important |
| FB-V03 | Quick notes (Sequence) | Max chars placeholder (1000 default) | CH14 §Open Questions | Important |
| FB-V04 | Reference links count | Max count placeholder (5 default) | CH14 §Open Questions | Important |
| FB-V05 | Edge condition label | No max length specified | CH12 §12.6 | Nice-to-have |

---

## Summary Statistics

| Category | Blocker | Important | Nice-to-have | Total |
|----------|---------|-----------|--------------|-------|
| Unresolved Decisions | 4 | 16 | 4 | 24 |
| Placeholders | — | — | — | 5 |
| Contradictions | 1 | 2 | 1 | 4 |
| Cross-Dependencies | 5 | 3 | — | 8 |
| Edge Cases | 1 | 6 | — | 7 |
| Validation Gaps | — | 4 | 1 | 5 |
| **Total** | **11** | **31** | **6** | **48** |

---

## Next Steps

1. Resolve Blockers (11 items) before implementation
2. Coordinate with Batch 05–07 owners on dependencies
3. Finalize placeholders with engineering input
4. Clarify contradictions in CH15 folder handling

# Decision: Export Formats & Availability

**Date:** 2026-01-06  
**Status:** FINAL — ALL DECISIONS LOCKED  
**Mode:** No assumptions, no decisions, no invented values  
**Resolves:** ME-C03, related export blockers

---

## LOCKED (Verbatim PRD Only)

### Export Plan Gating — CH08 §4.1 (L397)
| Plan | Export Data | Source |
|------|-------------|--------|
| Guest | No | CH08 L397 |
| Free | Optional (placeholder) | CH08 L397 |
| Pro/Trial | Yes (placeholder) | CH08 L397 |

**Note:** CH08 marks both Free and Pro export as "placeholder" — formats and scope undefined.

### Guest Cannot Export — CH34 §4.4 (L625)
| Rule | Behavior | Source |
|------|----------|--------|
| Guest taps Export | Blocking prompt "Create an account to export data." | CH34 L625 |

### Email Verification Required for Export — CH07 (L186, L536)
| Rule | Behavior | Source |
|------|----------|--------|
| Unverified email | Sharing and export require verification | CH07 L186 |

### Export Entry Points — CH34 §4.1 (L600-602)
| Entry Point | Path | Source |
|-------------|------|--------|
| Primary | Settings Tab -> Data & Privacy section -> Export My Data | CH34 L601 |
| Alternative | Account screen (if separate) -> Data & Privacy -> Export My Data | CH34 L602 |

### Required Export Categories — CH34 §2.1 (L505-516)
| Category | Included Data | Source |
|----------|---------------|--------|
| Account | Identifiers, sign-in methods, account creation date, last login date, plan state | CH34 L506 |
| Profile | Display name, username/handle, training styles/filters from onboarding | CH34 L507 |
| Settings | App settings, notifications toggles, default filters, UI preferences, privacy toggles | CH34 L508 |
| Moves library | Default-move selections, custom moves, user customizations (notes, tags, videos/links, aliases) | CH34 L509 |
| Flows | All saved flows, folders/organization, flow metadata, flow graphs (nodes/edges), sequences, per-edge detail | CH34 L510 |
| Sharing | Unlisted share links (active + revoked), link metadata (created_at, revoked_at) | CH34 L511 |
| Inbox | Received imports, statuses (viewed, saved to library, expired) | CH34 L512 |
| Practice | Practice sessions history, per-session configuration, completion state, summary metrics | CH34 L513 |
| Mastery/Gameplans | Gameplans, selected paths/flows, mastery status per path, user adjustments | CH34 L514 |
| Maintenance | Schedules/plans, load preferences, reminder settings, history | CH34 L515 |
| Notes | Freeform notes tied to moves, sequences, paths, flows, gameplans | CH34 L516 |

### Media Export Rules — CH34 §2.2 (L518-520)
| Media Type | Export Behavior | Source |
|------------|-----------------|--------|
| Link-based media (shareable) | Export includes URLs and attachment context | CH34 L519 |
| Uploaded media (Pro-only, private-only) | Export includes either (1) media download links + manifest (default) OR (2) media files in zip if user explicitly requests and size allows | CH34 L520 |

### Export Package Structure — CH34 §3.1 (L540-566)
```
handz_export_<timestamp>/
  manifest.json
  README.txt
  data/
    account.json
    profile.json
    settings.json
    moves.json
    flows.json
    flow_graphs.json
    sequences.json
    sharing_links.json
    inbox.json
    practice_sessions.json
    mastery_gameplans.json
    maintenance.json
    notes.json
  csv/
    practice_sessions.csv
    practice_sets.csv
    maintenance_tasks.csv
  media/
    media_manifest.json
    (optional media files or empty)
```
Source: CH34 L541-566

### Empty Category Handling — CH34 §3.1 (L568)
| Rule | Behavior | Source |
|------|----------|--------|
| Category has no items | Include file with empty array (not omit) | CH34 L568 |

### manifest.json Required Fields — CH34 §3.2 (L570-575)
| Field | Content | Source |
|-------|---------|--------|
| export_id | Unique identifier | CH34 L571 |
| generated_at | Timestamp | CH34 L571 |
| app info | name, bundle_id, export_schema_version | CH34 L571 |
| user info | user_id, timezone, plan_state | CH34 L572 |
| counts | moves_total, flows_total, practice_sessions_total, gameplans_total, media_items_total | CH34 L573 |
| media | includes_media_files, media_delivery, expires_at | CH34 L574 |
| integrity | sha256 for each major file | CH34 L575 |

### JSON Files Rules — CH34 §3.3 (L577-583)
| Rule | Specification | Source |
|------|---------------|--------|
| Encoding | UTF-8 | CH34 L578 |
| Collections | Arrays | CH34 L579 |
| Identifiers | Include stable identifiers (canonical IDs) | CH34 L580 |
| Timestamps | ISO-8601 in UTC | CH34 L581 |
| Secrets | Never export secrets (auth tokens, payment receipts) | CH34 L582 |
| Profile | Minimum fields only (display name, username, email if email signup; no password hashes) | CH34 L583 |

### CSV Files Rules — CH34 §3.4 (L585-590)
| Rule | Specification | Source |
|------|---------------|--------|
| Delimiter | Comma | CH34 L586 |
| Header | Header row required | CH34 L587 |
| Escaping | Escape quotes; wrap cells with commas/newlines in double-quotes | CH34 L588 |
| Files | One CSV per major time-series: practice sessions, practice sets, maintenance tasks | CH34 L589 |
| Nesting | Avoid nested JSON inside CSV cells | CH34 L590 |

### README.txt Required — CH34 §3.5 (L592-594)
| Rule | Content | Source |
|------|---------|--------|
| Purpose | Explains what is in export, what files mean, how to interpret counts | CH34 L593 |
| Disclaimer | Include disclaimer that results vary for training heuristics (owned by CH26) | CH34 L594 |

### Export UX Screens — CH34 §4.2 (L604-610)
| Screen ID | Purpose | Source |
|-----------|---------|--------|
| CH34-EX1 | Data & Privacy (Settings subsection) | CH34 L605 |
| CH34-EX2 | Export Options (scope + include-media toggles) | CH34 L606 |
| CH34-EX3 | Confirm Export (summary) | CH34 L607 |
| CH34-EX4 | Export Progress (generating zip; cancel; background behavior) | CH34 L608 |
| CH34-EX5 | Export Ready (share sheet; save to Files; copy export_id) | CH34 L609 |
| CH34-EXE | Export Error (actionable message + retry) | CH34 L610 |

### Export Options Screen (V1) — CH34 §4.4 (L619-623)
| Option | V1 Behavior | Source |
|--------|-------------|--------|
| Export scope | Everything only (V1); Practice only and Flows and Moves only hidden but reserved | CH34 L620 |
| Include media toggle | "Include uploaded media" (default OFF) | CH34 L621 |
| Delivery method | On-device only (V1); Email link reserved | CH34 L622 |

### Re-authentication Required — CH34 §5.1 (L657-660)
| Rule | Specification | Source |
|------|---------------|--------|
| When required | Before generating export, if user has not re-authenticated within last 10 minutes (default) | CH34 L658 |
| Methods | Match account method: Apple Sign-In, Google reauth, or password entry | CH34 L659 |
| If fails/canceled | Return to CH34-EX3 with "Export canceled." | CH34 L660 |

### Export Rate Limits — CH34 §5.2 (L662-665)
| Limit Type | Value | Behavior | Source |
|------------|-------|----------|--------|
| Soft limit | 3 exports per 24 hours per account | Warning + extra confirmation | CH34 L663 |
| Hard limit | 10 exports per 24 hours per account | Block with "For safety, exports are limited. Try again tomorrow." | CH34 L664 |

### Export Size Safeguards — CH34 §5.3 (L667-670)
| Rule | Specification | Source |
|------|---------------|--------|
| Size warning | Show estimated size; warn if > 250MB | CH34 L668 |
| Large media warning | If include-media ON and total media > 1GB: blocking warning | CH34 L669 |
| Media cap | Never exceed user's 2GB stored media cap | CH34 L670 |

### Export Snapshot Semantics — CH34 §6.1 (L680-683)
| Rule | Specification | Source |
|------|---------------|--------|
| Point in time | Export is snapshot at point in time with single timestamp (generated_at) | CH34 L681 |
| V1 editing | Allow editing during export; export is start-of-export snapshot | CH34 L682 |

### Export Ordering and Determinism — CH34 §6.2 (L685-687)
| Rule | Specification | Source |
|------|---------------|--------|
| Sorting | Items sorted deterministically (created_at asc then id asc) | CH34 L686 |
| File names | Do not randomize file names or order inside zip | CH34 L687 |

### Export Integrity — CH34 §6.3 (L689-691)
| Rule | Specification | Source |
|------|---------------|--------|
| Hash | Compute SHA-256 for each major file; include in manifest.json | CH34 L690 |
| Partial failure | If file fails to write: fail export; do not produce partial export | CH34 L691 |

### Export Partial Failure Policy — CH34 §6.4 (L693-695)
| V1 Policy | Behavior | Source |
|-----------|----------|--------|
| Recommended | Fail export with clear message | CH34 L694 |
| Alternative (not recommended V1) | Produce export with missing-file note | CH34 L695 |

### Export Offline Behavior — CH34 §10.1 (L798-801)
| Scenario | Behavior | Source |
|----------|----------|--------|
| Offline | Block with "Connect to the internet to export your data." | CH34 L799 |
| V1 recommendation | Require connectivity to avoid incomplete exports | CH34 L800 |
| Connectivity drops mid-export | Error with retry; retry re-generates fresh export | CH34 L801 |

### Export Error States — CH34 §11.1 (L817-825)
| Error | Copy | Actions | Source |
|-------|------|---------|--------|
| No internet | "You're offline. Connect to the internet and try again." | [OK] | CH34 L820 |
| Re-auth canceled | "Export canceled." | [OK] | CH34 L821 |
| Insufficient storage | "Not enough space to create the export. Free up storage and try again." | [Retry] [Cancel] | CH34 L822 |
| Zip generation failed | "Export failed while generating the file. Please try again." | [Retry] [Cancel] | CH34 L823 |
| Media export too large | "Media makes this export too large for your device. Export without media instead." | [Export Without Media] [Cancel] | CH34 L824 |
| Unexpected | "Something went wrong. Try again." | [Retry] [Cancel] | CH34 L825 |

### Privacy Warning on Share — CH34 §5.4 (L672-674)
| Rule | Behavior | Source |
|------|----------|--------|
| When | User taps Share on CH34-EX5 | CH34 L673 |
| Content | One-time interstitial: "This export may contain personal data. Only share it with people you trust." [Cancel] [Continue] | CH34 L673 |
| Disable option | User can disable warning in Settings (optional) | CH34 L674 |

---

## Blocking Decisions — ALL LOCKED

| ID | Decision | Resolution | Status |
|----|----------|------------|--------|
| EX-D01 | V1 export formats | **PDF, JSON** | **LOCKED** |
| EX-D02 | Free export access | **PDF only, watermarked** | **LOCKED** |
| EX-D03 | Pro export access | **PDF + JSON, no watermark** | **LOCKED** |
| EX-D04 | Export rate limits | **3/day soft, 10/day hard** | **LOCKED** |
| EX-D05 | Export size limit | **10 MB** | **LOCKED** |
| EX-D06 | Watermark policy | **Free only** | **LOCKED** |

---

## LOCKED DECISIONS

### EX-D01: V1 export formats — **LOCKED: PDF, JSON**

**Decision:** V1 supports two export formats: PDF and JSON.

**Resolution Date:** 2026-01-06

**Implications:**
- PDF: Visual representation of flows, suitable for printing/sharing
- JSON: Machine-readable data export per CH34 structure
- CSV files included as part of JSON export package

---

### EX-D02: Free export access — **LOCKED: PDF only, watermarked**

**Decision:** Free users can export PDF format only. PDF exports include a "Handz Free" watermark.

**Resolution Date:** 2026-01-06

**Implications:**
- Free users cannot export JSON/full data package
- Watermark placement: footer or corner (non-intrusive but visible)
- Provides basic export capability while differentiating Pro

---

### EX-D03: Pro export access — **LOCKED: PDF + JSON, no watermark**

**Decision:** Pro users can export both PDF and JSON formats. No watermark on any exports.

**Resolution Date:** 2026-01-06

**Implications:**
- Full data portability for Pro users
- JSON export includes complete CH34 package structure
- No branding/watermark on Pro exports

---

### EX-D04: Export rate limits — **LOCKED: 3/day soft, 10/day hard**

**Decision:** Export rate limits:
- Soft limit: 3 exports per 24 hours (warning + confirmation)
- Hard limit: 10 exports per 24 hours (blocked)

**Resolution Date:** 2026-01-06

**Note:** Matches PRD CH34 L663-664

---

### EX-D05: Export size limit — **LOCKED: 10 MB**

**Decision:** Maximum export file size is 10 MB (excluding media).

**Resolution Date:** 2026-01-06

**Implications:**
- Media files handled separately per CH34 media export rules
- If export exceeds 10 MB, show warning and suggest excluding categories
- Does not apply to media-included exports (those follow CH34 L669 rules)

---

### EX-D06: Watermark policy — **LOCKED: Free only**

**Decision:** Watermark ("Handz Free") applies only to Free user exports. Pro exports have no watermark.

**Resolution Date:** 2026-01-06

**Implications:**
- Watermark on PDF exports from Free accounts
- Clear visual differentiation
- Upgrade incentive for users who share exports

---

## PRD References

- CH07 L186: Email verification required for export
- CH08 §4.1 (L397): Export entitlements by plan (placeholder)
- CH34 §1 (L491-497): Purpose and scope
- CH34 §2 (L505-534): Data inventory
- CH34 §3 (L538-595): Export package, formats, and schema
- CH34 §4 (L598-652): Data export UX
- CH34 §5 (L655-674): Export security, limits, and abuse controls
- CH34 §6 (L678-695): Export generation behavior
- CH34 §10 (L796-811): Offline behavior and reliability
- CH34 §11 (L815-840): Error states
- CH34 Placeholders (L865-870): P1 (scope), P2 (delivery method)

---

**End of Document — All Export Format Decisions LOCKED**

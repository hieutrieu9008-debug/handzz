# PRD Batch 09 - CH33-CH34 (Analytics & Data Management)

---

## CH33: Analytics and Metrics Spec

**Doc ID:** HZ-V1-CH33_Analytics_and_Metrics_Spec_R1  
**Revision:** R1 (2026-01-02)  
**Status:** Draft  
**Depends on:** CH01, CH02, CH03, CH04, CH05, CH06, CH07, CH08, CH12, CH15, CH16, CH17, CH18, CH20, CH21, CH22, CH23, CH24, CH25, CH30, CH31, CH34  
**Related:** CH26, CH27, CH28, CH29, CH35, CH36, CH37, CH38  
**Owned Decisions:** Analytics event naming + schema; required event properties; funnel definitions; dashboard minimum set; privacy-by-default analytics rules; analytics QA checklist for V1

### Overview

Defines what Handz measures in V1, how measurements are structured, and how analytics must respect privacy. Produces canonical event catalog, funnel definitions, and dashboard requirements. Non-goal: picking a specific analytics vendor (placeholder).

---

### Requirements

#### 1. Analytics Principles (Must-Follow)

**Minimum Necessary Data:**
- Collect only what is needed to ship/iterate V1: adoption, engagement, paywall conversion, reliability, abuse signals
- Default posture: no PII; no content text; no raw media; no opponent names; no free-form notes

**Privacy-by-Default:**
- No advertising tracking; no cross-app tracking; no IDFA usage in V1
- Analytics must be first-party: event data used only to improve Handz
- Provide user-facing toggle to disable analytics collection (PLACEHOLDER: default state)

**Deterministic Identifiers:**
- Use stable internal IDs (flow_id, move_id, path_id, gameplan_id) for measurement
- Identifiers must be random UUIDs; never derived from user text

**Event Catalog Authority:**
- Every event must be listed in canonical catalog or marked as future/placeholder
- No "random logging" in code; dev logs must never ship to production

**Attempt Events Required:**
- When user hits a gate (Guest restriction, Free cap, Pro-only feature), emit BOTH gate_attempt and gate_result (blocked/allowed)

---

#### 2. Privacy, Compliance, and User Controls

**2.1 Data Categories - Allowed (V1):**
- Product usage events: screens visited, button taps, feature usage, counts, durations
- Structural metrics: number of flows, nodes, branches; practice session timing; credits usage
- Plan state + entitlement state (Guest/Free/Trial/Pro) - See: CH08 S1
- Performance telemetry: load time buckets, canvas FPS bucket, crash-free sessions
- Error codes and non-sensitive diagnostics (no raw stack traces unless redacted)

**Not Allowed (V1):**
- Any user-entered free-form text (notes, custom move descriptions, sequence detail text)
- Exact flow graphs or path sequences (can measure counts/shape, not content)
- Media files, thumbnails, or URLs beyond domain-level classification
- Contacts, address book, precise location, microphone audio, camera footage
- Advertising identifiers (IDFA) or third-party trackers

**2.2 User-Facing Settings + Defaults:**

Settings > Privacy:
- **Analytics toggle:** Label: "Share anonymous usage data to improve Handz"
  - If OFF: stop sending analytics events; keep local-only counters for on-device UX (e.g., streak display) but do not upload
  - If ON later: resume sending; do NOT backfill historical events by default (PLACEHOLDER option)
- **Crash reporting toggle:** Label: "Share crash reports"
  - May be separate from analytics
  - If OFF: do not send crash logs; still show user-friendly error messages
- **Data export + delete:** Must link to CH34 flows

**2.3 Consent Surfaces:**
- First launch: short disclosure in onboarding (non-blocking)
- Settings > Privacy: full details + toggle(s)
- Privacy Policy link in Settings and during signup (copy owned by CH07/CH08/CH34)

---

#### 3. Core Analytics Objects + Identifiers

**3.1 Identifier Glossary:**
| Identifier | Definition |
|------------|------------|
| install_id | Random UUID at first app launch; resets on reinstall |
| user_id | Random UUID after account creation; never email-based |
| session_id | Random UUID per app session (foreground until background > threshold) |
| flow_id | UUID per saved flow (library) |
| flow_draft_id | UUID per unsaved draft (if supported; see CH28/CH15) |
| move_id | UUID for canonical/default move OR user custom move (canonical also has canonical_id; see CH10) |
| path_id | UUID for selected path instance |
| gameplan_id | UUID for user-defined bundle of paths/flows (see CH23) |
| share_link_id | UUID for unlisted link instance (see CH17) |
| import_id | UUID for inbox item (see CH18) |
| practice_session_id | UUID for practice session (see CH20-CH22) |
| mastery_plan_id | UUID for mastery plan (see CH23) |
| maintenance_plan_id | UUID for maintenance schedule bundle (see CH24) |

**3.2 Global Event Properties (Required on Every Event):**
| Property | Type | Notes |
|----------|------|-------|
| event_name | string | snake_case, must exist in catalog |
| event_time_utc | ISO-8601 | device time in UTC |
| app_version | string | marketing version |
| build_number | string/int | internal build |
| platform | string | iOS |
| os_version | string | e.g., iOS 18.6 |
| device_model | string | iPhone model string |
| locale | string | e.g., en_US |
| timezone | string | IANA tz |
| session_id | uuid | current session |
| install_id | uuid | install-scoped |
| user_state | enum | guest | free | trial | pro |
| analytics_opt_in | bool | true/false at time of event |
| network_state | enum | online | offline | flaky |

**Optional Common Properties:**
- screen_name (string): current screen route identifier (See: CH04 S2)
- entry_point (enum/string): where action started
- duration_ms (int): for timed actions
- error_code (string): for errors (See: CH31 S1)
- gate_name (string): for paywalls/caps (See: CH08 S3)

---

#### 4. Event Naming Standard + Categories

**Format:** `<domain>_<action>[_<object>]` in snake_case

**Domains (authoritative list V1):**
app, auth, onboarding, nav, moves, flows, builder, sequence, library, share, inbox, practice, mastery, maintenance, paywall, settings, notif, export, error, perf, abuse

**Action verbs:** viewed, tapped, created, saved, started, completed, failed, blocked, allowed, exported

**Rules:**
- Do not include user content in names or properties
- If event emitted from multiple screens, include screen_name property rather than duplicating event names

---

#### 5. Funnels and KPIs

**5.1 Core Funnels (V1):**

**F0 - Activation (first value):**
1. app_open
2. onboarding_viewed
3. flow_builder_opened (or library_viewed)
4. flow_draft_created
5. flow_saved_success
- Success: user has at least 1 saved flow

**F1 - Account conversion:**
1. guest_mode_entered
2. guest_restriction_hit (any)
3. signup_viewed
4. signup_success
- Success: user_id created + user_state becomes free

**F2 - Sharing growth loop (unlisted links):**
1. share_link_create_tapped
2. share_link_created
3. share_link_opened (by receiver)
4. import_received
5. import_saved_to_library (receiver)
- Success: receiver has saved flow and later creates their own

**F3 - Practice paywall conversion:**
1. practice_entry_attempt
2. paywall_viewed (if blocked)
3. trial_started or subscription_purchased
4. practice_session_started
- Success: first completed practice session after upgrade

**F4 - Mastery plan adoption:**
1. mastery_plan_created
2. mastery_plan_scheduled
3. mastery_practice_started
- Success: plan has ongoing maintenance activity

**5.2 KPI Definitions (Minimum Set):**

*Activation:*
- Activation rate: % installs reaching flow_saved_success within 24h / 7d
- Time-to-first-flow: median time from app_open to flow_saved_success

*Retention:*
- D1/D7/D30 retention: % users returning on day N after install
- Practice retention: % users running practice at least once per week

*Engagement:*
- Weekly flows edited per active user
- Weekly practice minutes per practicing user (See: CH22)
- Branches-per-flow distribution

*Monetization:*
- Paywall view rate: % users seeing paywall per week
- Trial start rate and trial-to-paid conversion
- Churn rate: monthly subscription cancellations

*Sharing Loop:*
- Share link create rate per active user
- Share link open-to-import rate
- Import-to-saved conversion rate

*Quality:*
- Crash-free sessions percentage
- Canvas performance: % sessions with builder_fps_bucket=good

---

#### 6. Canonical Event Catalog (V1)

**Authority:** If event not listed here, it must not ship unless explicitly marked as placeholder/disabled by default.

**6.1 App + Session:**
- `app_open` - foreground entry (cold/warm start, push, deep_link)
- `app_background` - foreground_duration_ms, open_screens_count
- `screen_viewed` - screen_name, previous_screen_name, entry_point

**6.2 Onboarding + Education:**
- `onboarding_viewed` - variant (placeholder for experimentation), user_state
- `onboarding_completed` - completion_path (guest|signup), duration_ms
- `education_flow_concept_opened` - entry_point, time_in_view_ms

**6.3 Authentication + Account Conversion:**
- `guest_mode_entered` - entry_point (first_launch|logout|skip_signup)
- `signup_viewed` - entry_point, reason
- `signup_method_selected` - method (apple|google|email), entry_point
- `signup_success` - method, duration_ms, previous_user_state
- `signup_failed` - method, error_code, error_step, duration_ms
- `login_success` - method, duration_ms

**6.4 Moves (Default + Custom):**
- `moves_library_viewed` - entry_point, mode
- `move_selected` - move_canonical_id, context
- `custom_move_create_started` - entry_point
- `custom_move_create_saved` - move_id, has_media_link, has_media_upload, alias_count, tag_count, family_assigned, variant_of_move_id
- `custom_move_edit_saved` - move_id, edit_type, revert_used
- `move_revert_used` - target, field_name, scope

**6.5 Flow Builder (Creation + Editing):**
- `flow_builder_opened` - entry_point, flow_id, flow_draft_id
- `flow_draft_created` - flow_draft_id, template_used, template_source
- `builder_node_added` - node_type, move_canonical_id, move_id, via, total_nodes_after
- `builder_edge_created` - edge_type, from_node_type, to_node_type, branch_index, total_outgoing_from_source_after
- `builder_branch_limit_hit` - flow_id, flow_draft_id, source_node_id, attempted_outgoing_count (also emit gate_attempt/gate_result with gate_name=branch_limit) - See: CH12 S3.4
- `builder_node_reordered` - method, nodes_moved_count
- `builder_root_replaced` - previous_root_move_id, new_root_move_id, new_root_canonical_id
- `sequence_editor_opened` - entry_point, has_existing_details - See: CH14
- `sequence_editor_saved` - fields_used_count, has_media_link, has_media_upload
- `flow_save_attempt` - user_state, flow_draft_id, current_saved_flow_count
- `flow_saved_success` - flow_id, nodes_count, edges_count, branches_count, dangling_paths_count, sequence_nodes_count, duration_ms
- `flow_saved_blocked` - gate_name (guest_save_required|free_saved_flows_cap), current_saved_flow_count - See: CH08 S3

**6.6 Library + Organization:**
- `library_viewed` - tab, flows_count, folders_count
- `folder_created` - folder_id, parent_folder_id
- `flow_opened` - flow_id, entry_point - See: CH16
- `flow_duplicated` - source, source_flow_id, new_flow_id, user_state

**6.7 Sharing + Imports:**
- `share_sheet_opened` - flow_id, entry_point
- `share_link_create_tapped` - flow_id, current_links_created_today, user_state
- `share_link_created` - share_link_id, flow_id, expires_in_days, user_state - See: CH17
- `share_link_create_blocked` - gate_name, current_links_created_today - See: CH17 S3, CH30
- `share_link_opened` - share_link_id, entry_point, is_logged_in
- `import_received` - import_id, share_link_id, recipient_user_state
- `inbox_viewed` - inbox_count, user_state - See: CH18
- `import_opened` - import_id, contains_custom_moves, contains_custom_media_uploads, contains_custom_media_links
- `import_saved_to_library_attempt` - import_id, user_state, current_saved_flow_count, inbox_count
- `import_saved_to_library_success` - import_id, new_flow_id, conflict_resolution_used
- `import_saved_to_library_blocked` - gate_name (free_saved_flows_cap|account_required), current_saved_flow_count

**6.8 Practice (Drills) + Credits:**
- `practice_entry_attempt` - entry_point, user_state, target_type, target_id
- `practice_gate_result` - gate_name, result (allowed|blocked), user_state, credits_remaining
- `practice_session_started` - practice_session_id, target_type, target_id, selected_paths_count, timer_seconds, assumed_reps, mode (drill|demo)
- `practice_path_presented` - practice_session_id, path_id, step_index, total_steps
- `practice_session_interrupted` - practice_session_id, reason, elapsed_seconds, completed_steps
- `practice_session_completed` - practice_session_id, completion_type, actual_duration_seconds, completed_steps, selected_paths_count
- `practice_credit_spent` - credit_type (monthly_free), credits_remaining_after, source
- `practice_credit_refreshed` - credits_added, credits_total_after, user_state
- `practice_demo_started` - practice_session_id, demo_id, timer_seconds

**6.9 Mastery + Maintenance Planning:**
- `gameplan_created` - gameplan_id, source, items_count - See: CH23
- `mastery_plan_created` - mastery_plan_id, target_type, target_id, goal_type, schedule_days_per_week, sessions_per_day
- `mastery_plan_updated` - mastery_plan_id, fields_changed_count, maintenance_enabled
- `maintenance_plan_created` - maintenance_plan_id, items_count, daily_budget_minutes, notif_enabled
- `maintenance_overload_warning_shown` - maintenance_plan_id, items_count, daily_budget_minutes
- `maintenance_session_completed` - maintenance_plan_id, actual_duration_seconds, items_drilled_count

**6.10 Paywall + Subscription:**
- `paywall_viewed` - paywall_id (practice|saved_flows|sharing|other), entry_point, user_state
- `paywall_cta_tapped` - paywall_id, cta (start_trial|subscribe|restore|not_now)
- `trial_started` - plan (monthly_9_99), trial_days (7)
- `subscription_purchased` - plan, intro_offer, via (paywall|settings)
- `subscription_cancelled` - plan, days_since_purchase
- `restore_purchases_success` - plan
- `restore_purchases_failed` - error_code

**6.11 Errors + Performance:**
- `error_shown` - error_code, screen_name, severity (info|warning|blocking) - See: CH31
- `perf_flow_builder_loaded` - duration_ms, nodes_count, edges_count, fps_bucket (good|ok|poor)
- `perf_flow_render_time` - duration_ms, nodes_count, branches_count, layout_mode
- `crash_reported` - last_screen_name, had_unsaved_changes, flow_draft_id

**6.12 Abuse + Safety Signals:**
- `abuse_rate_limit_triggered` - limit_name, window, attempts_in_window, action_blocked - See: CH30
- `abuse_warning_ladder_step` - ladder_step, reason_code, cooldown_hours

---

#### 7. Dashboard Requirements (V1)

**7.1 Launch Health (Daily):**
- Crash-free sessions % (last 24h, 7d rolling)
- App_open count, unique users, sessions
- Error_shown by error_code (top 10)
- Perf_flow_builder_loaded: p50/p90 duration_ms, fps_bucket distribution
- Signup_success rate and signup_failed by method/error_code

**7.2 Activation + Onboarding:**
- F0 activation conversion with drop-off per step
- Median time-to-first-flow
- Education_flow_concept_opened rate; correlation with activation
- F1 (Guest -> signup) funnel

**7.3 Builder Usage + Complexity:**
- Flows created per day and cumulative
- Distribution: nodes_count, edges_count, branches_count, dangling_paths_count
- Builder_branch_limit_hit count
- Sequence_editor_saved rate

**7.4 Sharing Loop:**
- share_link_created per day by user_state
- share_link_opened -> import_received -> import_saved_to_library_success conversion
- Inbox cap hits (free inbox cap=10) and resulting signup/upgrade behavior

**7.5 Practice + Mastery:**
- practice_entry_attempt and practice_gate_result breakdown
- practice_session completion rate and duration distribution
- Credits: practice_credit_spent distribution; % users exhausting credits
- Mastery/maintenance adoption rates

**7.6 Monetization:**
- paywall_viewed by paywall_id and entry_point
- paywall_cta_tapped rates
- trial_started count and trial-to-paid conversion
- Churn (subscription_cancelled) over time

---

#### 8. Data Quality, QA, and Validation

**8.1 Validation Rules:**
- All events must include base properties (S3.2); reject events missing any required base property
- All event_name values must match known name from catalog; unknown events blocked in production
- Property types must match catalog; optional properties omitted rather than garbage values
- IDs must be UUID format strings where specified
- No PII scanning: build-time lint rejects suspicious keys (email, phone, name, notes, description)

**8.2 QA Checklist (Pre-Release):**
- Cold start: app_open emits exactly once; screen_viewed emits on first screen
- Guest flow: guest_mode_entered -> guest restriction -> signup funnel emits expected events
- Save flow blocked: flow_save_attempt + flow_saved_blocked + gate_attempt/gate_result emitted
- Share link: share_link_create_tapped -> share_link_created; open yields share_link_opened + import_received
- Inbox cap: at 10 items, new import emits expected gate events (per CH18)
- Practice: free user blocked emits practice_entry_attempt + practice_gate_result(blocked); Pro emits session events
- Credits: practice_credit_spent decrements correctly; refresh emits practice_credit_refreshed monthly
- Paywall: paywall_viewed -> paywall_cta_tapped -> trial_started/subscription_purchased; restore flows log events
- Performance: perf_flow_builder_loaded measures duration and buckets
- Analytics opt-out: toggling OFF stops all event sending immediately

---

#### 9. Offline Behavior for Analytics

**Offline Queue:**
- Events generated offline queued locally until network resumes
- Queue size cap (PLACEHOLDER): recommended 1,000 events or 5 MB, whichever first
- When cap reached: drop oldest events; emit local-only counter (do NOT upload special event)

**Timestamp Rules:**
- Each event carries device event_time_utc
- Backend may add received_time_utc for latency analysis

**User Logout/Account Delete:**
- If user logs out: stop sending user_id; continue install_id analytics unless toggle off
- If user deletes account: delete user_id mapping; future events anonymous - See: CH34

---

#### 10. Security and Abuse Considerations

- Analytics endpoints must be authenticated or use signed ingestion keys
- Do not accept arbitrary event names from client (use allowlist)
- Rate-limit analytics ingestion to prevent DoS
- Store analytics data with least privilege; restrict internal access
- Redact sensitive crash logs; disable logs in release unless opted-in

---

#### 11. Experimentation Readiness (Future-proof, Optional V1)

**Feature Flags:**
- Ability to enable/disable certain features remotely
- Log flag_state_snapshot (optional property) when relevant

**A/B Tests:**
- If running tests in V1, define experiment_id and variant in event properties (optional)
- Do not run more than 1-2 simultaneous tests at launch

---

#### 12. Troubleshooting and Debugging Analytics

**Developer Diagnostics Screen (Hidden):**
- Shows: analytics enabled state, queued events count, last 10 events, last upload result code, last error
- Accessible via hidden gesture in Settings (e.g., tap version number 7 times)

---

#### 13. Acceptance Tests (Chapter Complete Definition)

- Event allowlist implemented and matches catalog exactly
- All screens emit screen_viewed with correct screen_name and previous_screen_name
- All major funnels (F0-F4) can be computed from events without missing steps
- Analytics opt-out fully stops analytics network calls
- No PII leakage confirmed by automated lint + manual review
- Offline queue works and never crashes app; queue cap enforced
- Dashboards in S7 can be created with available events/properties

---

### CH33 Placeholders

| ID | Description | Owner | Options | Default | Decide-by |
|----|-------------|-------|---------|---------|-----------|
| CH33-P1 | Analytics vendor | Product/Engineering | Firebase Analytics, PostHog, Amplitude, Mixpanel, custom | Firebase Analytics + Crashlytics (suggested) | Before build |
| CH33-P2 | Default analytics opt-in policy | CH33 | A) On by default with disclosure; B) Off by default; C) Ask at first launch | A (recommended unless legal requires otherwise) | Before launch |
| CH33-P3 | Event retention period | - | 13 months / 24 months / 36 months | - | Before launch |
| CH33-P4 | Sampling | - | No sampling for core funnels; optional for high-frequency perf events | No sampling | - |
| CH33-P5 | Device identifier hash usage | - | Salted rotating hash if needed for abuse / Omit entirely | - | - |
| CH33-P6 | Experiment system | - | Remote config and A/B testing | Defer until after launch | - |
| CH33-P7 | Offline queue size cap | CH33 | 1,000 events or 5 MB | 1,000 events or 5 MB | - |
| CH33-P8 | Historical backfill on opt-in | CH33 | Backfill / Do not backfill | Do not backfill | - |

---

### CH33 Cross-References

- CH08 S1: Plan States (user_state)
- CH08 S3: Entitlement rules (gate_name)
- CH04 S2: Screen route identifiers
- CH10: Canonical move IDs
- CH12 S3.4: Branch limit (10)
- CH14: Sequence detail editor
- CH16: Flow Detail View
- CH17: Sharing/Unlisted Links
- CH17 S3: Link limits
- CH18: Inbox
- CH20-CH22: Practice Mode
- CH23: Mastery and Gameplans
- CH24: Maintenance System
- CH28/CH15: Draft support
- CH30: Warning Ladder, Abuse thresholds
- CH31 S1: Error codes
- CH34: Data deletion guarantees

---

## CH34: Data Export & Account Deletion

**Doc ID:** HZ-V1-CH34_Data_Export_Account_Deletion_R1  
**Revision:** R1 (2026-01-02)  
**Status:** Draft  
**Depends on:** CH07, CH08, CH28, CH29, CH31  
**Related:** CH15, CH16, CH17-CH19, CH20-CH24, CH25, CH30, CH33  
**Owned Decisions:** Export package contents and formats; export UX; deletion UX; deletion semantics and retention; re-auth requirements; rate limits for exports/deletions

### Overview

Defines user-facing controls and system behavior for data export and account deletion. Required for App Store compliance and user trust.

---

### Requirements

#### 1. Purpose and Scope

**Non-negotiable Outcomes:**
- User can initiate account deletion from within iOS app without contacting support
- User can initiate data export from within app and receive usable package
- No data export or deletion flow introduces security holes
- Exports and deletions produce deterministic, testable outputs

**Out of Scope:** Pricing, trial behavior, paywall placement, entitlement rules (owned by CH25/CH08)

---

#### 2. Data Inventory

**2.1 Required Export Categories:**
- **Account:** identifiers, sign-in methods, account creation date, last login date, plan state
- **Profile:** display name, username/handle, training styles/filters from onboarding
- **Settings:** app settings, notifications toggles, default filters, UI preferences, privacy toggles
- **Moves library:** default-move selections, custom moves, user customizations (notes, tags, videos/links, aliases)
- **Flows:** all saved flows, folders/organization, flow metadata, flow graphs (nodes/edges), sequences, per-edge detail
- **Sharing:** unlisted share links (active + revoked), link metadata (created_at, revoked_at)
- **Inbox:** received imports, statuses (viewed, saved to library, expired)
- **Practice:** practice sessions history, per-session configuration, completion state, summary metrics
- **Mastery/Gameplans:** gameplans, selected paths/flows, mastery status per path, user adjustments
- **Maintenance:** schedules/plans, load preferences, reminder settings, history
- **Notes:** freeform notes tied to moves, sequences, paths, flows, gameplans

**2.2 Media Data Types:**
- **Link-based media (shareable):** YouTube links, private/unlisted links, external URLs - Export includes URLs and attachment context
- **Uploaded media (Pro-only, private-only):** Videos under 2GB cap - Export includes either (1) media download links + manifest (default) OR (2) media files in zip if user explicitly requests and size allows

**2.3 Deletion Categories:**

**Must Delete:**
- Profile, settings, authored moves/customizations, flows, practice logs, mastery/gameplans, maintenance plans, inbox items, share links, uploaded media, push notification tokens

**May Retain (De-identified):**
- Aggregated analytics counters with no user_id linkage

**Must Revoke:**
- Share links created by user (so they no longer resolve)

**Must NOT Delete:**
- Content other users duplicated into their own libraries (belongs to recipient's account)

---

#### 3. Export Package, Formats, and Schema

**3.1 Export Package Structure:**
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

- If category has no items, include file with empty array (not omit)

**3.2 manifest.json (Required):**
- export_id, generated_at, app info (name, bundle_id, export_schema_version)
- user info (user_id, timezone, plan_state)
- counts (moves_total, flows_total, practice_sessions_total, gameplans_total, media_items_total)
- media (includes_media_files, media_delivery, expires_at)
- integrity (sha256 for each major file)

**3.3 JSON Files Rules:**
- UTF-8 encoded
- Arrays for collections
- Include stable identifiers (canonical IDs)
- Timestamps: ISO-8601 in UTC
- Never export secrets (auth tokens, payment receipts)
- Minimum profile fields only (display name, username, email if email signup; no password hashes)

**3.4 CSV Files Rules:**
- Delimiter: comma
- Header row required
- Escape quotes; wrap cells with commas/newlines in double-quotes
- One CSV per major time-series: practice sessions, practice sets, maintenance tasks
- Avoid nested JSON inside CSV cells

**3.5 README.txt (Required):**
- Explains what is in export, what files mean, how to interpret counts
- Include disclaimer that results vary for training heuristics (owned by CH26)

---

#### 4. Data Export UX

**4.1 Entry Points (Must Exist):**
- Settings Tab -> Data & Privacy section -> Export My Data
- Account screen (if separate) -> Data & Privacy -> Export My Data

**4.2 Screen List:**
- CH34-EX1: Data & Privacy (Settings subsection)
- CH34-EX2: Export Options (scope + include-media toggles)
- CH34-EX3: Confirm Export (summary)
- CH34-EX4: Export Progress (generating zip; cancel; background behavior)
- CH34-EX5: Export Ready (share sheet; save to Files; copy export_id)
- CH34-EXE: Export Error (actionable message + retry)

**4.3 Data & Privacy Screen (CH34-EX1):**
- Section header: "Data & Privacy"
- Row: "Export My Data" (chevron) -> CH34-EX2
- Row: "Delete Account" (destructive styling) -> deletion flow
- Optional row: Privacy Policy / Terms
- Inline helper: "Download a copy of your Handz data as a zip file (JSON + CSV)."

**4.4 Export Options Screen (CH34-EX2):**
- Export scope (radio): Everything (default); Practice only; Flows and Moves only (V1: Everything only, others hidden but reserved)
- Include media (toggle): "Include uploaded media" (default OFF) - Subtext: "Uploads are private. Exporting media may create a large file."
- Delivery method (radio): Generate on device (default); Email me a download link (optional, if server-side implemented; V1: on-device only)
- Button: Continue -> CH34-EX3

**Plan Gating:** Guest cannot export (cannot save data per CH08). If Guest taps Export: blocking prompt "Create an account to export data."

**4.5 Confirm Export Screen (CH34-EX3):**
- Bullet summary of what will be included
- Show counts if available: "2 flows, 18 moves, 9 practice sessions..."
- If include-media OFF: "Uploaded media will not be included. Links will be included."
- If include-media ON: warning callout "This file could be large. Keep the app open during export."
- Button (primary): Generate Export
- Button (secondary): Cancel
- Requires re-auth if last re-auth older than security window (S5)

**4.6 Export Progress Screen (CH34-EX4):**
- Progress indicator: determinate if possible (0-100%) else step-based
- Text: "Generating your export..."
- Subtext: "Do not close the app."
- Button: Cancel Export (secondary, confirm modal)
- If cancel: delete partial export, return to CH34-EX2 with toast "Export canceled."

**Background Behavior (V1):** If app backgrounds, export may pause; resume when returned. If OS kills app, export fails gracefully; user can retry.

**4.7 Export Ready Screen (CH34-EX5):**
- Success state: "Export ready"
- Show export_id: "Export ID: XXXXX" with Copy button
- Primary action: "Save to Files" (iOS share sheet / file exporter)
- Secondary action: "Share..." (share sheet; warn personal data)
- Tertiary action: "Done" -> Settings
- If media links with expiry: show "Media links expire on <date>."

---

#### 5. Export Security, Limits, and Abuse Controls

**5.1 Re-authentication Rules (Required):**
- Before generating export, require re-auth if user has not re-authenticated within last 10 minutes (default)
- Re-auth methods match account method: Apple Sign-In, Google reauth, or password entry
- If re-auth fails/canceled: return to CH34-EX3 with "Export canceled."

**5.2 Rate Limits (Required):**
- Soft limit: 3 exports per 24 hours per account - Above: warning + extra confirmation
- Hard limit: 10 exports per 24 hours per account - Above: block with "For safety, exports are limited. Try again tomorrow."
- Log abuse event (owned by CH30); display "Why?" help link

**5.3 Export Size Safeguards:**
- Show estimated size; warn if > 250MB
- If include-media ON and total media > 1GB: blocking warning "This may fail on your device. Consider exporting without media." Buttons: [Export Without Media (default)] [Continue Anyway]
- Never exceed user's 2GB stored media cap

**5.4 Privacy Warnings:**
- When user taps Share on CH34-EX5, show one-time interstitial: "This export may contain personal data. Only share it with people you trust." [Cancel] [Continue]
- User can disable warning in Settings (optional)

---

#### 6. Export Generation Behavior (Deterministic Rules)

**6.1 Snapshot Semantics:**
- Export is snapshot at point in time with single timestamp (generated_at)
- V1: Allow editing during export; export is start-of-export snapshot
- If snapshot fails due to sync conflict: show actionable error (S11)

**6.2 Ordering and Determinism:**
- Items sorted deterministically (created_at asc then id asc)
- Do not randomize file names or order inside zip

**6.3 Integrity Checks:**
- Compute SHA-256 for each major file; include in manifest.json
- If file fails to write: fail export; do not produce partial export

**6.4 Partial Failures:**
- V1: Fail export with clear message (recommended)
- Alternative: produce export with missing-file note (not recommended V1)

---

#### 7. Account Deletion UX

**7.1 Entry Points (Must Exist):**
- Settings -> Data & Privacy -> Delete Account (destructive row)
- Optional duplicate: Profile screen -> Delete Account

**7.2 Screen List:**
- CH34-DEL1: Delete Account (information + consequences)
- CH34-DEL2: Confirm Deletion (reauth + type-to-confirm)
- CH34-DEL3: Deletion In Progress (blocking)
- CH34-DEL4: Deleted (signed out; confirmation)
- CH34-DELE: Deletion Error (retry / contact support)

**7.3 Delete Account Info Screen (CH34-DEL1):**
- Title: "Delete account"
- Short line: "This permanently deletes your Handz data."
- Consequences shown:
  - Your saved flows, moves, notes, practice history, mastery/gameplans, and maintenance plans will be deleted.
  - Your share links will be revoked and will stop working.
  - Any flows you previously sent that other users duplicated will not be deleted from their accounts.
  - If you have an active subscription, you must cancel it in iOS Settings. Deleting your account does not automatically cancel your Apple subscription.
  - This action cannot be undone after completion.
- Button (destructive): Continue to delete
- Button (secondary): Cancel

**7.4 Confirm Deletion Screen (CH34-DEL2):**
- Re-auth prompt (same rules as exports; S5.1)
- Type-to-confirm: user must type DELETE (all caps) to enable final button (accessibility: allow case-insensitive but display as DELETE)
- Final button (destructive, disabled until typed): "Delete my account"
- Secondary button: "Cancel"
- Optional: 3-second hold-to-confirm on final button

**7.5 Deletion In Progress Screen (CH34-DEL3):**
- Blocking screen with spinner: "Deleting your account..."
- No navigation away allowed
- If > 10 seconds: subtext "This may take a moment. Keep the app open."
- If app backgrounded and interrupted: resume on next launch or show clear failure

**7.6 Deleted Screen (CH34-DEL4):**
- Confirmation: "Account deleted."
- Button: "Return to Welcome" -> Welcome Gate (CH05/CH07)
- Optional: Privacy Policy link

---

#### 8. Deletion Semantics, Retention, and Data-by-Data Rules

**8.1 Recommended Deletion Model (V1):**
- **Phase 1 (Immediate):** Mark account as "deleting" and sign user out. Revoke all share links immediately.
- **Phase 2 (Purge):** Within 24 hours (target), no later than 30 days (hard requirement), delete all user content and media from storage.
- **No undo in V1:** Once deletion initiated, no undo flow.

**8.2 Data-by-Data Deletion Rules (Normative Order):**
1. Revoke share links (public access cut off first)
2. Invalidate sessions / refresh tokens
3. Delete uploaded media (largest cost)
4. Delete flows and flow graphs
5. Delete moves and move notes
6. Delete practice logs (sessions, sets, summaries)
7. Delete mastery/gameplans and maintenance data
8. Delete inbox records and pending conflict resolution records
9. Delete settings and notification tokens
10. Delete user profile record
11. Delete auth account (Firebase Auth user / equivalent)

**8.3 What is Retained (Minimal):**
- Aggregated, de-identified analytics counters (no user_id, email, username, device identifiers)
- Security audit logs may retain hashed user id for abuse investigations only if required; must be documented

**8.4 Handling Subscriptions on Deletion:**
- Show clear note on CH34-DEL1: "Cancel in iOS Settings."
- Provide button "How to cancel subscription" -> iOS subscription management deep link or instructions screen
- After deletion, if user re-installs later, may still be subscribed at Apple level (CH25/CH08 handles entitlements)

---

#### 9. Interaction with Sharing, Inbox, Imports, and Conflicts

**9.1 Export Includes Sharing Objects:**
- Export must include all share links (active and revoked) with metadata
- Export must include inbox items (even if Free can only view them)

**9.2 Deletion and Shared Links:**
- All share links revoked immediately on deletion initiation
- Revoked link shows: "This link is no longer available." (CH17/CH31)

**9.3 Deletion and Imported Duplicates:**
- Deleting sender account must NOT delete duplicates saved by recipients
- If duplicates include sender's custom move descriptions, those become recipient's data
- Imported flows in user's library exported normally as their content

**9.4 Deletion and Conflict Resolution Artifacts:**
- Pending import conflicts (CH19) deleted as part of deletion
- Custom moves created from conflict resolution also deleted

---

#### 10. Offline Behavior and Reliability

**10.1 Export While Offline:**
- If export requires server fetch and offline: block with "Connect to the internet to export your data."
- V1 recommendation: require connectivity to avoid incomplete exports
- If connectivity drops mid-export: error with retry; retry re-generates fresh export

**10.2 Deletion While Offline:**
- Deletion initiation requires connectivity (to revoke access server-side)
- If offline: block with "Connect to the internet to delete your account."
- If connectivity drops after initiation: server-side purge completes when connectivity returns
- On next login attempt, if account flagged deleting: block with "Account deletion in progress."

**10.3 Long-Running Operations:**
- If > 60 seconds: persistent status message
- Never leave user stuck on spinner indefinitely; provide timeouts and clear next steps

---

#### 11. Error States (Export and Deletion)

**11.1 Export Errors (CH34-EXE):**
| Error | Copy | Actions |
|-------|------|---------|
| No internet | "You're offline. Connect to the internet and try again." | [OK] |
| Re-auth canceled | "Export canceled." | [OK] |
| Insufficient storage | "Not enough space to create the export. Free up storage and try again." | [Retry] [Cancel] |
| Zip generation failed | "Export failed while generating the file. Please try again." | [Retry] [Cancel] |
| Media export too large | "Media makes this export too large for your device. Export without media instead." | [Export Without Media] [Cancel] |
| Unexpected | "Something went wrong. Try again." | [Retry] [Cancel] |

**11.2 Deletion Errors (CH34-DELE):**
| Error | Copy | Actions |
|-------|------|---------|
| No internet | "You're offline. Connect to the internet to delete your account." | [OK] |
| Re-auth failed | "We couldn't confirm it's you. Try again." | [Retry] [Cancel] |
| Server error | "We couldn't delete your account right now. Try again later." | [Retry] [Cancel] |
| Account already deleting | "Account deletion is already in progress." | [OK] |
| Unexpected | "Something went wrong. Try again." | [Retry] [Cancel] |

**11.3 Public Errors for Revoked Links:**
- Owned by CH17/CH31
- Revoked links must not reveal whether user existed
- Copy: "This link is no longer available."

---

#### 13. Acceptance Tests (Given/When/Then)

**Export Flow Tests:**
- Given logged-in Free user with 2 saved flows and 0 media uploads, when they navigate Settings -> Data & Privacy -> Export My Data and generate export, then app produces zip with manifest.json, README.txt, all required data/*.json with correct counts and deterministic ordering
- Given logged-in Pro user with uploaded media, when they leave Include media OFF, then export contains media_manifest.json with links-only and no media files
- Given user not re-authenticated within 10 minutes, when they tap Generate Export, then re-auth prompt appears
- Given user cancels re-auth, when they return, then export canceled and no export file created
- Given app backgrounded during export, when user returns, then export continues or fails gracefully with retry path
- Given user taps Cancel Export during generation, when they confirm, then partial artifacts deleted and toast shown
- Given user hits hard export limit, when they attempt another export, then app blocks with limit message

**Deletion Flow Tests:**
- Given logged-in user, when they navigate to Delete Account, then they see consequences list
- Given user not re-authenticated within 10 minutes, when they proceed to confirm deletion, then re-auth required
- Given user has not typed DELETE, when viewing confirm screen, then final delete button disabled
- Given user types DELETE and confirms, when deletion begins, then user signed out, share links revoked immediately
- Given revoked share link, when someone opens it, then they see "This link is no longer available."
- Given deleted user re-installs app, when they attempt to log in, then login blocked/fails
- Given recipients duplicated user's flows previously, when deletion completes, then recipient duplicates remain accessible

---

### CH34 Placeholders

| ID | Description | Owner | Options | Default | Decide-by |
|----|-------------|-------|---------|---------|-----------|
| CH34-P1 | Export scope options | CH34 | Everything only (V1) / Add Practice-only / Add Flows-only | Everything only | Before build starts |
| CH34-P2 | Export delivery method | CH34 | On-device only / Email link / Both | On-device only | Before build starts |
| CH34-P3 | Export rate limits | CH34 | 3/day soft, 10/day hard / Adjust after telemetry | 3/10 | After CH33 metrics plan |
| CH34-P4 | Media export behavior | CH34 | Links-only (default) / Embed files / Both | Links-only | After storage testing |
| CH34-P5 | Deletion completion SLA | CH34 | 24h target, 30d hard / Faster | 24h/30d | Before launch |

---

### CH34 Cross-References

- CH07: Authentication & Account System
- CH08: Entitlements & Plan States
- CH05/CH07: Welcome Gate
- CH15: Library
- CH16: Flow Detail
- CH17-CH19: Sharing/Inbox/Import Conflicts
- CH17/CH31: Revoked link error handling
- CH19: Conflict resolution
- CH20-CH24: Practice/Mastery/Maintenance
- CH25: Monetization (subscriptions)
- CH26: Scientific Claims (training heuristics disclaimer)
- CH28: Offline Behavior & Sync
- CH29: Data Storage & Limits (2GB cap)
- CH30: Safety/Abuse Limits
- CH31: Error States
- CH33: Analytics & Metrics

---

## Batch 09 Summary

### Open Questions / Unresolved Items

**CH33:**
1. Analytics vendor selection (Firebase Analytics + Crashlytics suggested)
2. Default analytics opt-in state (On by default with disclosure recommended)
3. Event retention period (13/24/36 months)
4. Sampling strategy for high-frequency perf events
5. Device identifier hash usage for abuse prevention
6. Experiment/A/B testing system (defer post-launch)
7. Offline queue size cap (1,000 events or 5MB recommended)
8. Historical backfill on opt-in (do not backfill recommended)

**CH34:**
1. Export scope options beyond "Everything" (V1: Everything only)
2. Export delivery method beyond on-device (V1: on-device only)
3. Export rate limits adjustment post-telemetry
4. Media export behavior (links-only default)
5. Deletion completion SLA (24h target, 30d hard)

### Potential Contradictions / Edge Cases

1. **Analytics vs Deletion:** CH33 specifies user_id must be deleted on account deletion; analytics must continue as anonymous install_id-based unless toggle off. Must ensure deletion worker correctly removes user_id mapping while preserving aggregated metrics.

2. **Export + Offline:** CH34 requires connectivity for export to avoid incomplete data; CH28 (offline behavior) may allow local-only cache. V1 recommendation is require connectivity.

3. **Share Link Revocation Timing:** CH34 requires immediate revocation on deletion; CH17 must implement resolver that checks revoked flag synchronously.

4. **Free Inbox Cap:** CH33 dashboard references "free inbox cap=10"; must verify alignment with CH18 definition.

5. **Credits in Analytics:** practice_credit_spent event references monthly_free credits; must align with CH08 credit refresh mechanics.

### Assumptions Detected

**CH33:**
- A1: Platform is iOS only (V1)
- A2: Analytics is first-party only; no third-party trackers
- A3: IDFA not used in V1
- A4: Event names in snake_case format
- A5: All identifiers are random UUIDs

**CH34:**
- A1: Guest cannot export (cannot save data)
- A2: Subscriptions managed via StoreKit (Apple); deletion does not auto-cancel
- A3: Duplicated flows belong to recipient (not deleted when sender deletes)
- A4: No undo for deletion in V1
- A5: Export is point-in-time snapshot

---

**Batch 09 Complete. Awaiting approval before proceeding to Batch 10 (CH35-CH38: QA & Deployment).**

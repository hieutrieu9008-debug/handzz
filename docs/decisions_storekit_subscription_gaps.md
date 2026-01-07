# Decision: StoreKit / Subscription Gaps

**Date:** 2026-01-06  
**Status:** FINAL — ALL DECISIONS LOCKED  
**Mode:** No assumptions, no decisions, no invented values  
**Resolves:** ME-SK01, ME-SK02, ME-SK03, ME-SK04, ME-SK05, related subscription blockers

---

## LOCKED (Verbatim PRD Only)

### Subscription Platform — CH08 (L486)
| Requirement | Specification | Source |
|-------------|---------------|--------|
| Platform | Use StoreKit subscriptions (iOS-only V1) | CH08 L486 |

### Pricing — CH08 (L487), CH25 (L582)
| Aspect | Value | Source |
|--------|-------|--------|
| Monthly price | $9.99 | CH08 L487, CH25 L582 |
| Trial length | 7-day free trial | CH08 L487, CH25 L582 |
| Subscription type | Auto-renewing subscription | CH25 L582 |
| Trial eligibility | For new subscribers | CH25 L582 |

### Required StoreKit Flows — CH08 (L488)
| Flow | Required | Source |
|------|----------|--------|
| Purchase | Yes | CH08 L488 |
| Restore | Yes | CH08 L488 |
| Cancel flow | Yes (link out to Apple subscription management) | CH08 L488 |
| Plan status screen | Yes (clear) | CH08 L488 |

### Error Handling (Purchase Pending/Failed) — CH08 (L489)
| Scenario | Behavior | Source |
|----------|----------|--------|
| Purchase pending or fails | Keep user in current plan and show recoverable error state (see CH31) | CH08 L489 |

### UI Pricing Rules — CH25 (L584-586)
| Rule | Specification | Source |
|------|---------------|--------|
| Localized pricing | All pricing UI must use localized StoreKit product pricing (currency + region) | CH25 L585 |
| No hardcoding | Never hardcode '$9.99' into UI strings; show "{localizedPrice}/month" | CH25 L586 |

### Trial Behavior — CH25 (L588-593)
| Rule | Specification | Source |
|------|---------------|--------|
| Trial start | Begins immediately after successful Apple subscription purchase confirmation (StoreKit) | CH25 L589 |
| Cancel during trial | Pro entitlements remain active until trial end; then downgrade to Free automatically | CH25 L590 |
| Not eligible for trial | Show "Start Pro" (no "Free trial" language) | CH25 L591 |
| Disclosure required | Always show disclosure: "Cancel anytime in Settings." | CH25 L592 |
| Renewal disclosure | Under CTA: "Free for 7 days, then {localizedPrice}/month. Auto-renews until canceled." | CH25 L593 |

### Trial Definition — CH08 (L324, L463)
| Rule | Specification | Source |
|------|---------------|--------|
| Trial = Pro | Time-limited entitlement that behaves exactly like Pro | CH08 L324 |
| Trial gates | During trial, all Pro gates are removed (Practice, uploads, etc.) | CH08 L463 |
| Trial end | User becomes Pro (paid) if converted, otherwise downgrades to Free | CH08 L324 |

### Restore Purchases — CH25 (L595-599)
| Rule | Specification | Source |
|------|---------------|--------|
| Settings entry | Settings includes "Restore Purchases" action | CH25 L596 |
| Entitlement refresh | On app launch and returning to foreground: refresh entitlements from StoreKit (with caching) | CH25 L597 |
| Unknown status | If entitlement status unknown (network issue): default to Free entitlements + non-blocking banner "Checking subscription status..." + background refresh (CH31) | CH25 L598 |
| Refresh confirms Pro | Entitlements update immediately + subtle toast (no modal) | CH25 L599 |

### Eligibility-Aware CTA Rules — CH25 (L622-625)
| Eligibility | CTA Text | Source |
|-------------|----------|--------|
| Eligible for trial | "Start 7-Day Free Trial" | CH25 L623 |
| Not eligible | "Start Pro" | CH25 L624 |
| Clarity | Never show multiple confusing CTAs | CH25 L625 |

### Upgrade Flow (Free/Guest → Trial/Pro) — CH25 (L734-738)
| Step | Behavior | Source |
|------|----------|--------|
| Trigger | User triggers paywall, taps primary CTA | CH25 L735 |
| StoreKit succeeds | Immediately unlock Pro, dismiss paywall, show toast "Pro unlocked" | CH25 L736 |
| From blocked action | Automatically proceed after unlock | CH25 L737 |
| Fails/cancelled | Stay on paywall, show non-blocking error (CH31), allow retry | CH25 L738 |

### Purchase Pending State — CH08 (L343)
| Scenario | Behavior | Source |
|----------|----------|--------|
| Purchase pending | Keep current state but show purchase-in-progress UI; do not unlock until confirmation | CH08 L343 |

### Pro (grace) State — CH08 (L330)
| State | Definition | Source |
|-------|------------|--------|
| Pro (grace) | Subscription was active recently but cannot be verified due to network/StoreKit failure. Allow Pro features for short grace window (owned by CH28). Display non-blocking banner: "Can't verify subscription right now. Some Pro features may pause if this continues." | CH08 L330 |

### Plan State Enum — CH08 (L349)
```
planState: enum (guest | free | trial | pro | pro_grace)
```
Source: CH08 L349

### Entitlement Evaluation with Grace — CH08 (L342)
| Rule | Specification | Source |
|------|---------------|--------|
| Grace condition | If StoreKit cannot verify and a recent verified snapshot exists → state may be Pro (grace) | CH08 L342 |

### Offline Behavior for Pro — CH25 (L746)
| Rule | Specification | Source |
|------|---------------|--------|
| Pro offline | Allow Pro entitlements for grace window (e.g., 24 hours) using last-known timestamp; after grace, treat as Free until refresh | CH25 L746 |

### N-BILLING-TRIAL-END Notification — CH27 (L142-149)
| Aspect | Value | Source |
|--------|-------|--------|
| Trigger | Trial ends in 48 hours; schedule at moment trial starts | CH27 L143 |
| Delivery | Local (scheduled) and/or push | CH27 L144 |
| Eligibility | Trial users only | CH27 L145 |
| Default | ON | CH27 L146 |
| Frequency cap | 1 per trial | CH27 L147 |
| Deep-link | handz://paywall?context=trialEnding | CH27 L148 |
| Copy | "Your trial ends soon. Keep practicing without interruptions." | CH27 L149 |

### N-BILLING-PAYMENT-FAILED Notification — CH27 (L151-158)
| Aspect | Value | Source |
|--------|-------|--------|
| Trigger | StoreKit reports billing issue / payment failed | CH27 L152 |
| Delivery | Push if available; must also show in-app alert on next open | CH27 L153 |
| Eligibility | Pro users | CH27 L154 |
| Default | ON | CH27 L155 |
| Frequency cap | 1 per event | CH27 L156 |
| Deep-link | handz://paywall?context=paymentFailed | CH27 L157 |
| Copy | "Payment issue. Update your subscription to keep Pro features." | CH27 L158 |

### Billing Notification Category Behavior — CH27 (L203-204)
| Rule | Specification | Source |
|------|---------------|--------|
| User can disable | User can disable categories | CH27 L203 |
| Critical override | Transactional billing and security/account recovery must still appear in-app | CH27 L203 |
| In-app reminders | If user disables Billing notifications, app still shows in-app reminders during trial end / payment issue window | CH27 L204 |

### Badge Count for Billing Issue — CH27 (L229, L232)
| Rule | Specification | Source |
|------|---------------|--------|
| Badge component | Billing: 1 if unresolved billing issue | CH27 L232 |
| Formula | Badge = unreadInboxCount + (maintenanceDueToday ? 1 : 0) + (billingIssue ? 1 : 0) + (securityFlag ? 1 : 0) | CH27 L229 |

### Manage Subscription Screen — CH25 (L728-730)
| Element | Content | Source |
|---------|---------|--------|
| Plan status card | currentPlanLabel, renewal date if Trial/Pro, caps summary if Free | CH25 L729 |
| Actions | "Manage in Apple Subscriptions", "Restore Purchases", "How Pro works" link, Cancel instruction for Pro/Trial | CH25 L730 |

### Analytics Events — CH08 (L500-506), CH25 (L756-762)
| Event | Parameters | Source |
|-------|------------|--------|
| trial_started | — | CH08 L500 |
| purchase_started | — | CH08 L501 |
| purchase_success | productId, trialUsed | CH08 L502, CH25 L758 |
| purchase_failed | reason | CH08 L503, CH25 L759 |
| restore_clicked | — | CH08 L504 |
| restore_success | plan | CH08 L505, CH33 L300 |
| restore_failed | error_code | CH08 L506, CH33 L301 |
| paywall_viewed | context, sourceScreen, planState, trialEligible | CH25 L756 |
| paywall_cta_tapped | context, trialEligible | CH25 L757 |

### Acceptance Test: Trial Parity — CH08 (L519)
| Test | Given | When | Then | Source |
|------|-------|------|------|--------|
| Trial parity | I am in Trial | I open Practice or Upload | No paywall or credit gates appear | CH08 L519 |

### Account Deletion and Subscription — CH34 (L719, L768-771)
| Rule | Specification | Source |
|------|---------------|--------|
| Warning | "If you have an active subscription, you must cancel it in iOS Settings. Deleting your account does not automatically cancel your Apple subscription." | CH34 L719 |
| Button | Provide button "How to cancel subscription" -> iOS subscription management deep link or instructions screen | CH34 L770 |
| Re-install | After deletion, if user re-installs later, may still be subscribed at Apple level (CH25/CH08 handles entitlements) | CH34 L771 |

---

## Blocking Decisions — ALL LOCKED

| ID | Decision | Resolution | Status |
|----|----------|------------|--------|
| SK-D01 | Billing grace period | **3 days** | **LOCKED** |
| SK-D02 | Grace period UX | **Banner with explanation** | **LOCKED** |
| SK-D03 | Subscription validation frequency | **Launch + every 24h** | **LOCKED** |
| SK-D04 | Offline subscription handling | **Respect 24h Pro grace** | **LOCKED** |
| SK-D05 | Family Sharing support | **Not supported in V1** | **LOCKED** |
| SK-D06 | Promotional offers | **Not supported in V1** | **LOCKED** |
| SK-D07 | Upgrades/downgrades handling | **Immediate entitlement change** | **LOCKED** |
| SK-D08 | Refund handling | **Handled by Apple; reflect on next sync** | **LOCKED** |

---

## LOCKED DECISIONS

### SK-D01: Billing grace period — **LOCKED: 3 days**

**Decision:** When StoreKit reports a billing issue (payment failed), allow 3 days of Pro grace before downgrading to Free.

**Resolution Date:** 2026-01-06

**Implications:**
- User retains Pro features for 3 days during Apple's billing retry period
- After 3 days without resolution, downgrade to Free
- Banner shown throughout grace period

---

### SK-D02: Grace period UX — **LOCKED: Banner with explanation**

**Decision:** During billing grace period, show a non-blocking banner explaining the situation.

**Resolution Date:** 2026-01-06

**Copy:** "Payment issue. Update your payment method to keep Pro features."

**Implications:**
- Banner persists until resolved or grace expires
- Deep-links to Apple subscription management
- Does not block app usage

---

### SK-D03: Subscription validation frequency — **LOCKED: Launch + every 24h**

**Decision:** Subscription status is validated:
1. On every app launch
2. Every 24 hours while app is active
3. On return to foreground (cached, not blocking)

**Resolution Date:** 2026-01-06

**Implications:**
- Ensures timely detection of subscription changes
- Uses StoreKit caching to minimize latency
- Background refresh if initial check times out

---

### SK-D04: Offline subscription handling — **LOCKED: Respect 24h Pro grace**

**Decision:** When offline, respect the 24-hour Pro grace window (per OFF-D01). After 24 hours offline without validation, treat user as Free until connection restored.

**Resolution Date:** 2026-01-06

**Implications:**
- Consistent with offline sync decisions
- Pro features available for 24h offline
- Banner shown when approaching/exceeding grace

---

### SK-D05: Family Sharing support — **LOCKED: Not supported in V1**

**Decision:** Family Sharing is not supported in V1. Each user must have their own subscription.

**Resolution Date:** 2026-01-06

**Implications:**
- Simplifies entitlement logic for V1
- Can be added in future version
- Users with Family Sharing expectations should be informed

---

### SK-D06: Promotional offers — **LOCKED: Not supported in V1**

**Decision:** App Store promotional offers and offer codes are not supported in V1.

**Resolution Date:** 2026-01-06

**Implications:**
- No promo code redemption UI
- No introductory offers beyond standard trial
- Can be added post-MVP based on marketing needs

---

### SK-D07: Upgrades/downgrades handling — **LOCKED: Immediate entitlement change**

**Decision:** Subscription upgrades and downgrades result in immediate entitlement changes.

**Resolution Date:** 2026-01-06

**Implications:**
- Upgrade: Pro features unlock immediately
- Downgrade: Follows DG-D01 through DG-D10 decisions
- No delayed entitlement changes

---

### SK-D08: Refund handling — **LOCKED: Handled by Apple; reflect on next sync**

**Decision:** Refunds are processed entirely by Apple. App reflects refund status on next subscription sync.

**Resolution Date:** 2026-01-06

**Implications:**
- No in-app refund flow
- StoreKit notifies app of entitlement revocation
- User downgrades to Free when refund processed
- Data preserved per downgrade decisions

---

## PRD References

- CH08 L324: Trial definition
- CH08 L330: Pro (grace) state
- CH08 L342-343: Entitlement evaluation, purchase pending
- CH08 L349: planState enum
- CH08 L463: Trial = Pro
- CH08 L486-489: StoreKit expectations
- CH08 L500-506: Analytics events
- CH08 L519: Trial parity acceptance test
- CH25 L582: Locked pricing
- CH25 L584-593: UI rules, trial behavior
- CH25 L595-599: Restore purchases
- CH25 L622-625: Eligibility-aware CTAs
- CH25 L728-730: Manage subscription screen
- CH25 L734-738: Upgrade flow
- CH25 L746: Offline behavior
- CH25 L756-762: Analytics hooks
- CH25 L767-772: Placeholders
- CH27 L142-158: Billing notifications
- CH27 L203-204: Billing category behavior
- CH27 L229, L232: Badge count
- CH34 L719, L768-771: Deletion and subscription

---

**End of Document — All StoreKit/Subscription Decisions LOCKED**

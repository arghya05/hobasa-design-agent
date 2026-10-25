# feat.platform_update: FEAT-ZD-48600 (DES-01 check record)

Step DES-01 "Platform Architecture Update" ran in **check mode**. The spine reported that its condition is not met: "condition not met: TRD flags a platform-level change" (record:intake). This record checks that against the approved TRD and the platform architecture. No architecture document, code, configuration, test or environment was changed.

| Field | Value | Source |
|---|---|---|
| Trace ID | FEAT-ZD-48600 | record:brief; feat.trd |
| Workbook action | "If the feature needs an architectural change at the platform level, so firstly it gets updated in the platform architectural level document" | record:brief |
| Mode | Check: say whether the action is needed now. Change nothing | record:brief; record:intake |
| Inputs read | feat.trd (from hobasa-requirement-agent at commit 3637e51); org.architecture (feature override, at commit 70aad33) | record:brief |
| Other files read | `org/org.architecture.md` (org-level base document); `org/org.decision_log.md` | file:hobasa-agentic-sdlc/org/org.architecture.md; file:hobasa-agentic-sdlc/org/org.decision_log.md |

## 1. Conclusion

**No platform-level architecture update is needed now.** The TRD (the step brief lists it as the "Approved TRD") does not flag a platform-level change. None of its changes alters the platform architecture as org.architecture describes it:

- The repositories stay the same.
- The runtimes stay the same.
- The order of the four repositories in the flow stays the same. One new in-library serialisation step, CT-1, goes between ap_aging and ap_portal. Who owns its transport is still open (TQ-3; see O-1 and TR-1).
- The JSON rules stay the same.
- The signature rule stays the same.
- Authentication, tenancy, finance posting and payment execution are not added or changed (S-1).
- Two feature-level security items are still open, and neither changes the platform architecture:
  - output encoding in ap_portal (S-4);
  - the risk label added to the existing LLM prompt (S-5, TQ-8; see TR-4).

Every change is either a new function or an additive change inside an existing repository. That is what the platform rule "Keep every existing signature; add new functions" allows. The feature-level contract CT-1 and the new functions belong in the feature's own design and TRD, not in the platform architecture document.

## 2. What was checked, and the evidence

| # | Platform aspect (org.architecture) | What the TRD does | Platform change? | Source |
|---|---|---|---|---|
| K-1 | Four repositories in one flow: ap_pipeline → ap_forecast → ap_aging → ap_portal | Changes only these four repositories (C-1 to C-10). It adds no repository or service. The end-to-end wiring (IF-8) follows the same flow | No, pending TQ-3. The new CT-1 serialiser (C-7) is a function inside ap_aging; its transport is open (TR-1) | org.architecture; feat.trd (section 1, IF-6, IF-8, TQ-3) |
| K-2 | Runtimes: Python 3.9 for three repositories; Node.js 22 CommonJS for ap_portal | Keeps them. F-15 says new Python code must not use syntax newer than 3.9 | No | org.architecture; feat.trd (F-15) |
| K-3 | "Keep every existing signature; add new functions" | New functions: `delays_by_vendor`, `vendor_risk`, `overdue_with_risk`, `overdue_rows_to_json`, `renderOverdueRisk`. Two existing functions change additively: `to_model` behaviour (signature unchanged) and `build_explanation_request` (new optional `risk=None` as the last parameter). The TRD validation check records "Pass" for this rule | No. This is the change type the rule allows | org.architecture; feat.trd (section 1; IF-1; IF-4.1; section 9) |
| K-4 | "JSON between services carries amounts as strings with two decimals and dates as ISO strings" | CT-1 sends `amount` as a two-decimal string and has no date field. It says any date added later must be an ISO string | No. CT-1 follows the existing rule | org.architecture; feat.trd (IF-6) |
| K-5 | Data shape: Invoice records from ap_pipeline | Adds one key, `paid_date` (date or None), to the record dict. Existing keys and values do not change. No storage, schema migration or new persistent data | No platform change. This is a field-level change to the feature's data | feat.trd (IF-1.6; D-2; section 3 data rules) |
| K-6 | Base org document: ap_aging is a pure library with "No database, no network, no secrets", and existing signatures and return shapes must not change | `overdue_with_risk` and `overdue_rows_to_json` are pure and in-memory. `aging_buckets`, `days_past_due` and `bucket_for` do not change | No | file:hobasa-agentic-sdlc/org/org.architecture.md; feat.trd (IF-5.8; section 3 data rules) |
| K-7 | Tenancy and security | "No finance posting, authentication, tenancy or payment execution is added or changed; tier T2." The risk label is advisory only | No | feat.trd (S-1, S-2; section 1 "Not affected") |
| K-8 | LLM provider and model (ap_forecast `models.json`) | Provider and model stay the same (IF-4.4). `vendor_risk` uses no LLM (IF-3.2) | No | feat.trd (IF-3.2, IF-4.4; section 1 "Not affected") |
| K-9 | Platform decisions D-2026-031, D-2026-044, D-2026-050 | TRD section 9 records new code as consistent with all three | No decision change | file:hobasa-agentic-sdlc/org/org.decision_log.md; feat.trd (section 9); record:memory |

## 3. Observations for the Architect (not platform changes in this item)

| # | Observation | Why it is not a platform update now | Source |
|---|---|---|---|
| O-1 | The transport between ap_aging and ap_portal is open (TQ-3). The TRD found no service or transport layer in the code it read, and proposes a serialiser in ap_aging (C-7). The org-level base document says ap_aging is "used by the AP API", but no AP API code or contract is among this step's inputs. | C-7 is a pure function inside ap_aging and follows the existing JSON rule. It adds no service, endpoint or transport. If TQ-3 is answered with a new or changed service/API, see trigger TR-1. | feat.trd (IF-6, C-7, R-7, TQ-3); file:hobasa-agentic-sdlc/org/org.architecture.md |
| O-2 | The function lists in org.architecture will be out of date after this feature: the new exported functions and the optional `risk` parameter are not listed. | The platform rule allows new functions. A catalogue refresh after release is housekeeping, not an architectural change. It is offered as a non-blocking question. | org.architecture; feat.trd (section 1; IF-4) |
| O-3 | The two architecture documents do not have the same scope. The feature override describes all four repositories. The org-level base document describes only ap_aging. | This does not affect the conclusion, because both documents allow additive new functions. It is noted so that any later platform update edits the correct document. | org.architecture; file:hobasa-agentic-sdlc/org/org.architecture.md |
| O-4 | Existing code has two issues the TRD found. First, `formatAmount` formats through a float, against decision D-2026-044 (TQ-2). Second, `bucket_for(30)` returns `"31-60"`, which does not match its own "1-30" label or the `> 30` overdue rule (TQ-1). | These are defects in existing code and the TRD raises them as separate decisions. They are not architecture changes, and this feature does not touch either helper (C-9, IF-5.3). | feat.trd (F-10, F-12, TQ-1, TQ-2, C-9) |

## 4. What would trigger the action (re-run DES-01 as a full update)

| # | Trigger | Linked open item |
|---|---|---|
| TR-1 | The answer to TQ-3 adds or changes a service, API or transport between ap_aging and ap_portal (for example, CT-1 served through the AP API) | feat.trd (TQ-3, R-7) |
| TR-2 | CT-1, or any other inter-service JSON, needs a format other than two-decimal amount strings and ISO date strings | org.architecture; feat.trd (IF-6) |
| TR-3 | Any change breaks or replaces an existing signature or return shape. This includes rejecting an unpaid row with a `paid_date` (OQ-B6) if that changed `to_model`'s return shape (today the TRD says only IF-1 changes) | org.architecture; feat.trd (IF-1.7, R-11) |
| TR-4 | The answer to TQ-8 (sending the vendor's risk label to the external LLM provider) requires a platform-wide data-flow or provider policy, or a change of provider or model | feat.trd (S-5, TQ-8) |
| TR-5 | A new repository, runtime, storage or persistent data, or an authentication or tenancy change, is added to the scope | feat.trd (S-1; section 3 data rules) |
| TR-6 | The TRD is revised and flags a platform-level change | record:intake |

## 5. Assumptions and open questions (non-blocking)

- PU-Q1: Should org.architecture's function lists be updated after release with the new exported functions and the `risk` parameter (O-2)? This is catalogue housekeeping, not an architectural change.
- PU-Q2: Is the "AP API" in the org-level base document the transport for CT-1 (O-1, TQ-3)? If it is, TR-1 applies and DES-01 should run as a full update.
- PU-Q3: Which document is the platform architecture of record: the four-repository feature override or the ap_aging-only org-level base document (O-3)?

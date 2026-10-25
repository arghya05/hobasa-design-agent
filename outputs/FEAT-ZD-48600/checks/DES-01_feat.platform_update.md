# feat.platform_update: FEAT-ZD-48600 (DES-01 check record)

Step DES-01 Platform Architecture Update, attempt 1, run in **check mode**. The step's condition was not met in this run ("condition not met: TRD flags a platform-level change", record:intake). This record shows what was checked, the evidence, the conclusion, and what would make a platform architecture update necessary. No architecture document, code, configuration, test or environment was changed.

| Field | Value | Source |
|---|---|---|
| Trace ID | FEAT-ZD-48600 (ZD-48600, "Show which overdue invoices belong to vendors that usually pay late") | record:brief; feat.trd |
| Workbook action | If the feature needs a platform-level architectural change, update the platform architecture document first | record:brief |
| Owner / gate | Architect / automated checks, independent judge, sampled human evaluation | record:brief |
| Inputs | feat.trd (approved TRD, commit 3637e51); org.architecture (feature override, commit 3b03f15) | record:brief |
| Other files read | `org/org.architecture.md` (base platform document); `org/org.decision_log.md` | file:hobasa-agentic-sdlc/org/org.architecture.md; file:hobasa-agentic-sdlc/org/org.decision_log.md |

## 1. What was checked

A platform-level change is one that alters something the platform architecture document states as a rule or structure. For each statement in org.architecture (and the base `org/org.architecture.md`), the check asked whether feat.trd changes it.

| # | Platform statement | Source of the statement | What feat.trd does | Platform change? | Evidence |
|---|---|---|---|---|---|
| P-1 | Four repositories in one flow: ap_pipeline → ap_forecast → ap_aging → ap_portal | org.architecture | Changes code in the same four repositories only; adds no new repository, service or component outside them (C-1 to C-10) | No | feat.trd (header "Repositories"; section 1) |
| P-2 | Runtimes: three repositories on Python 3.9; ap_portal on Node.js 22 CommonJS | org.architecture | Keeps the runtimes; requires new Python code to avoid syntax newer than 3.9 (F-15) | No | feat.trd (F-15) |
| P-3 | "Keep every existing signature; add new functions." | org.architecture | Only two existing functions change, both additively: `to_model` behaviour (signature unchanged) and `build_explanation_request` gains `risk=None` as the last parameter. Everything else is new functions. The TRD's own validation row records this as Pass | No (rule respected) | feat.trd (IF-1, IF-4.1, section 9) |
| P-4 | Existing function signatures and return shapes must not change | file:hobasa-agentic-sdlc/org/org.architecture.md | `to_model` still returns `(records, rejected)`; records gain one key, `paid_date`, and existing keys and values are unchanged. `aging_buckets`, `days_past_due` and `bucket_for` are unchanged (IF-5.8, T-11) | No | feat.trd (IF-1.6, IF-5.8, T-11) |
| P-5 | JSON between services carries amounts as two-decimal strings and dates as ISO strings | org.architecture | New contract CT-1 (ap_aging → ap_portal) sends `amount` as a two-decimal string and has no date field; any later date must be an ISO string | No (CT-1 applies the existing rule) | feat.trd (IF-6) |
| P-6 | ap_aging: pure library; no database, no network, no secrets | file:hobasa-agentic-sdlc/org/org.architecture.md | `overdue_with_risk` is a pure function; the serialiser `overdue_rows_to_json` returns a string. The TRD adds no storage, schema migration or new persistent data | No | feat.trd (IF-5.8, C-7, section 3 "Data rules") |
| P-7 | Tenancy, authentication, finance posting, payment execution | record:brief (guardrail: stop when tenancy or security impact is unclear) | Explicitly not affected; tier T2 | No | feat.trd (section 1 "Not affected"; S-1) |
| P-8 | LLM provider and model (`acme-llm`, `acme-text-2` from `models.json`) | feat.trd (F-7) | Provider, model and `max_tokens` unchanged; `vendor_risk` uses no LLM | No | feat.trd (IF-3.2, IF-4.4, S-3) |
| P-9 | Decisions D-2026-031, D-2026-044, D-2026-050 | file:hobasa-agentic-sdlc/org/org.decision_log.md; record:memory | New code follows all three (calendar days, Decimal/string money, paid excluded) | No | feat.trd (section 9) |

## 2. Findings that are near the platform level, and why they do not trigger the action now

| # | Finding | Why it is not a platform update in this step | Source |
|---|---|---|---|
| N-1 | No transport or service layer between ap_aging and ap_portal exists in the code read; who owns serialisation and transport is open (TQ-3, R-7). | The TRD's proposal (C-7) keeps serialisation inside ap_aging as a pure function and introduces no new transport. The platform statement "ap_portal renders HTML from JSON" already covers this. If the Architect's answer to TQ-3 introduces a new service, API or transport, that **would** be a platform change (trigger X-1). | feat.trd (IF-6, C-7, R-7, TQ-3); org.architecture |
| N-2 | `overdue_with_risk` needs a `vendor` key on each invoice, while the base document describes ap_aging invoices as dicts with `id`, `due_date`, `amount`, `paid`. | The requirement belongs to a new function only; the existing invoice shape used by `aging_buckets` is unchanged. ap_pipeline records already carry `vendor`. The record shape is the data model's concern (DES-02 reads org.data_model), not a platform rule. | feat.trd (F-11, IF-5.7); file:hobasa-agentic-sdlc/org/org.architecture.md |
| N-3 | The platform document names only existing public functions; new public functions (`delays_by_vendor`, `vendor_risk`, `overdue_with_risk`, `overdue_rows_to_json`, `renderOverdueRisk`) will exist. | The platform rule is "add new functions"; adding them is the expected pattern, not a change to the architecture. Listing them is the design document's job (DES-02). Non-blocking question OQ-1 asks whether the Architect wants the function lists in org.architecture refreshed after release. | org.architecture; feat.trd (section 1) |
| N-4 | Existing code conflicts: `bucket_for(30)` returns `"31-60"` (TQ-1) and `formatAmount` formats through a float (TQ-2). | Both are left unchanged by this feature and raised as separate decisions. Neither changes a platform rule; fixing either is a separate item. | feat.trd (F-10, F-12, TQ-1, TQ-2) |
| N-5 | The risk label is added to the prompt sent to the external LLM provider (S-5, TQ-8). | Same provider, model and data path as today; only the prompt content grows. It is a data-flow approval question for the Architect, not a structural change. If the answer is "not acceptable" and the provider path must change, trigger X-4 applies. | feat.trd (S-5, TQ-8, IF-4.4) |

## 3. Conclusion

**No platform architecture update is needed now.** feat.trd keeps the four-repository flow, the runtimes, every existing signature and return shape, the JSON money/date rule, ap_aging's no-database/no-network property, the LLM provider, and tenancy and security unchanged (P-1 to P-9). All changes are additive functions and one new feature-level contract (CT-1) that applies the existing JSON rule. This agrees with the spine's condition result (record:intake). The org.architecture document stays as it is; the feature design continues in DES-02.

## 4. What would trigger the action

DES-01 should be re-run in full (the platform architecture document updated before design continues) if any of these happens:

| # | Trigger | Linked item |
|---|---|---|
| X-1 | The answer to TQ-3 introduces a new service, API endpoint, message transport or other layer between ap_aging and ap_portal | feat.trd (TQ-3, R-7) |
| X-2 | Any existing public signature or return shape has to change (for example a fix to `bucket_for` that changes aging totals, or a change to `formatAmount`) | feat.trd (TQ-1, TQ-2); org.architecture |
| X-3 | Persistent storage, a database, network access or secrets are added to any of the four repositories | file:hobasa-agentic-sdlc/org/org.architecture.md; feat.trd (section 3) |
| X-4 | The LLM provider, model or the external data path changes (for example as an outcome of TQ-8 or PROV-ACME-2026-11, PQ-5) | feat.trd (S-5, TQ-8, R-3) |
| X-5 | Tenancy, authentication, finance posting or payment execution becomes part of the scope | feat.trd (S-1) |
| X-6 | A new repository or runtime is added, or the JSON-between-services rule (two-decimal string amounts, ISO dates) has to change | org.architecture |

## 5. Open questions (non-blocking)

| ID | Question |
|---|---|
| OQ-1 | Should org.architecture's function lists be refreshed to name the new public functions once FEAT-ZD-48600 is released (N-3)? This is documentation upkeep, not an architectural change. |
| OQ-2 | Two platform documents exist with different scope: the feature override (four repositories) and the base `org/org.architecture.md` (ap_aging only). Should the base document be widened to the four-repository flow so later items do not depend on a feature override? |

TRD questions TQ-1 to TQ-11 stay open with the Architect as raised in feat.trd; this check does not answer them.

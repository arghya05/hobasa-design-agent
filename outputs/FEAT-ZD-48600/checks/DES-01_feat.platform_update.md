# feat.platform_update: FEAT-ZD-48600 (DES-01 check record)

| Field | Value | Source |
|---|---|---|
| Trace ID | FEAT-ZD-48600 | record:brief |
| Step | DES-01 Platform Architecture Update, run in **check mode** | record:brief; record:intake |
| Check-mode reason | "condition not met: TRD flags a platform-level change" | record:intake |
| Owner / gate | Architect / automated checks, independent judge, sampled human evaluation | record:brief |
| Inputs read | feat.trd (approved TRD, REQ-07 output at commit 3637e51); org.architecture (feature override at commit 76d03ce) | record:brief |
| Other files read | `org/org.architecture.md` (platform baseline); `features/FEAT-ZD-48600/org-overrides/org.data_model.md`; `org/org.decision_log.md` | file:hobasa-agentic-sdlc/… (cited per row) |
| Changes made | None. No platform architecture document, code, configuration, test or environment was changed. | record:brief (CHECK MODE) |

## 1. Conclusion

**No platform-level architecture update is needed now.** FEAT-ZD-48600 stays inside the existing four-repository flow, runtimes, contract rules and the "keep every existing signature; add new functions" rule. Every change the TRD lists is additive at component level: new functions, one new optional column, one new optional parameter. None of them changes a platform element (a repository, runtime, inter-service contract rule, tenancy, authentication, storage or external provider). The platform architecture document stays as it is. The new components and the CT-1 contract are designed in DES-02 (feat.design).

## 2. What was checked, and the evidence

Each row tests one kind of platform-level change against the approved TRD and the registered architecture.

| # | Platform dimension checked | Evidence | Result | Source |
|---|---|---|---|---|
| C-1 | Repositories and flow | The TRD touches only ap_pipeline, ap_forecast, ap_aging and ap_portal, the "four repositories in one flow" that org.architecture already registers. No repository is added, split or retired. | No change | feat.trd (section 1, C-1 to C-10; header "Repositories"); org.architecture |
| C-2 | Runtimes and technology | New code keeps to Python 3.9 (no newer syntax) and Node.js 22 CommonJS, the runtimes org.architecture names. The TRD chooses no new language, framework or library. | No change | feat.trd (F-15); org.architecture |
| C-3 | Signature rule | Only two existing functions change, both additively: `transform.to_model` gains `paid_date` handling with the same signature and return shape, and `build_explanation_request` gains `risk=None` as its last parameter. Everything else is a new function. This complies with "Keep every existing signature; add new functions." | Complies; no rule change | feat.trd (IF-1, IF-4.1, section 9 "Keep every existing signature"); org.architecture; file:hobasa-agentic-sdlc/org/org.architecture.md ("existing function signatures and return shapes must not change") |
| C-4 | Inter-service contract rules | CT-1 (ap_aging → ap_portal) sends `amount` as a two-decimal string and has no date field. This matches the registered rule "JSON between services carries amounts as strings with two decimals and dates as ISO strings". The rule is applied, not changed. | No change | feat.trd (IF-6); org.architecture |
| C-5 | Data model | The Invoice record gains `paid_date` (date or None). The feature's registered data model already contains `paid_date (date or None) [paid_date added in feed v3]`, so the data model needs no further update here. | Already registered | feat.trd (D-1, D-2, IF-1.6); file:hobasa-agentic-sdlc/features/FEAT-ZD-48600/org-overrides/org.data_model.md |
| C-6 | Storage and persistence | The TRD adds no storage, no schema migration and no new persistent data; every new function is in memory and pure. | No change | feat.trd (section 3, data rules) |
| C-7 | Tenancy, authentication, finance posting, payment execution | The TRD lists these as not affected (S-1), and no code path acts on a risk value (S-2). | No change | feat.trd (section 1 "Not affected"; S-1, S-2) |
| C-8 | External providers and models | Provider `acme-llm` and model `acme-text-2` are unchanged. `vendor_risk` uses no LLM. The only change at this boundary is that the explanation prompt may also carry the risk label (S-5, TQ-8). That is a data-content question for the Architect; the provider and the integration stay the same. | No platform change; question carried (TQ-8) | feat.trd (F-7, IF-3.2, IF-4.4, S-3, S-5, TQ-8) |
| C-9 | Transport between services | The TRD found no existing service or transport layer between ap_aging and ap_portal. It proposes only a serialiser function, `overdue_rows_to_json`, inside ap_aging (C-7), not a new service, endpoint or transport. Who owns transport is still open (TQ-3, R-7). | No platform change under the TRD design; see trigger T-1 | feat.trd (IF-6, C-7, TQ-3, R-7) |
| C-10 | Decision log | The design stays consistent with D-2026-031 (calendar days), D-2026-044 (Decimal, never float) and D-2026-050 (paid excluded). The TRD found two conflicts in existing code, `bucket_for(30)` and `formatAmount`, and raised them as separate questions (TQ-1, TQ-2). Neither changes the platform architecture. | No change | feat.trd (section 9; F-10, F-12; TQ-1, TQ-2); file:hobasa-agentic-sdlc/org/org.decision_log.md; record:memory |

### 2.1 Observation (no action in DES-01)

The platform baseline `org/org.architecture.md` describes ap_aging invoices as dicts with `id, due_date, amount, paid`. The feature override of org.architecture describes the four-repository flow. Under the TRD, `overdue_with_risk` also reads a `vendor` key, which ap_pipeline records already carry (F-2, F-11), and the existing `aging_buckets` input shape does not change. This is a new function's input, not a change to a platform rule. Whether the baseline's descriptive invoice-field list and function lists should be refreshed after release is a documentation question for the Architect (OQ-1). It does not block, and it does not trigger DES-01.

Sources: file:hobasa-agentic-sdlc/org/org.architecture.md; org.architecture; feat.trd (F-2, F-11, IF-5.7, IF-5.8).

## 3. What would trigger the DES-01 action

Any one of these would make the step's action needed. The platform architecture document would then be updated first, before DES-02 is finalised.

| ID | Trigger | Linked open item | Source |
|---|---|---|---|
| T-1 | The answer to TQ-3 introduces a new service, endpoint, message channel or transport layer between ap_aging and ap_portal, rather than the in-library serialiser C-7. | TQ-3, R-7, PQ-3 | feat.trd (IF-6, TQ-3, R-7) |
| T-2 | CT-1 or a later change departs from the registered JSON rule (amounts as two-decimal strings, dates as ISO strings), for example by sending `amount` as a JSON number. | T-A1 | org.architecture; feat.trd (IF-6, T-A1) |
| T-3 | Any existing signature or return shape has to change, for example if TQ-10 or OQ-B6 is answered in a way that alters `to_model`'s return shape, or if fixing `bucket_for` (TQ-1) or `formatAmount` (TQ-2) is taken into this item and changes their behaviour. | TQ-1, TQ-2, TQ-10, OQ-B6 | org.architecture; feat.trd (TQ-1, TQ-2, TQ-10, R-11) |
| T-4 | A new runtime, language version, framework or third-party package is chosen. | None | feat.trd (F-15); org.architecture |
| T-5 | The TQ-8 answer changes the LLM provider, the model, or the data-flow policy for the external provider. | TQ-8 | feat.trd (S-5, TQ-8) |
| T-6 | Persistent storage, a database or a schema migration is introduced, for example to hold delay history or risk levels between runs. | None | feat.trd (section 3 data rules) |
| T-7 | Tenancy, authentication, finance posting or payment execution comes into scope, for example a row action that acts on the risk label. | OQ-B9 | feat.trd (S-1, S-2, IF-7.8) |

## 4. Open questions (non-blocking)

| ID | Question | Why it matters |
|---|---|---|
| OQ-1 | After release, should the descriptive function and invoice-field lists in `org/org.architecture.md` and the org.architecture override be refreshed with the new functions (`delays_by_vendor`, `vendor_risk`, `overdue_with_risk`, `overdue_rows_to_json`, `renderOverdueRisk`) and the `vendor` / `paid_date` keys? This is documentation upkeep, not a platform change. | Keeps the registered architecture current for later agents |
| OQ-2 | Carried: answer TQ-3 (transport ownership) before DES-02 is finalised. If it introduces a transport layer, trigger T-1 applies and DES-01 must run. | Decides whether this check still holds |

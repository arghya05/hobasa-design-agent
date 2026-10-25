# feat.platform_update: FEAT-ZD-48600 (DES-01 check record)

| Field | Value | Source |
|---|---|---|
| Trace ID | FEAT-ZD-48600 | record:brief |
| Step | DES-01 Platform Architecture Update, attempt 1, run in **check mode** | record:brief |
| Check-mode reason | "condition not met: TRD flags a platform-level change" | record:intake |
| Owner / gate | Architect / automated checks, the independent judge and sampled human evaluation | record:brief |
| Inputs read | feat.trd (approved TRD, REQ-07 output at commit 3637e51); org.architecture (feature override at commit e8afce8) | record:brief |
| Background read | feat.design (approved DES-02 output, revision 722a98f) | journey:feat.design |
| Other files read | `org/org.architecture.md` (platform baseline); `features/FEAT-ZD-48600/org-overrides/org.data_model.md`; `org/org.decision_log.md` | file:hobasa-agentic-sdlc/… (cited per row) |
| Changes made | None. No platform architecture document, code, configuration, test or environment was changed. | record:brief (CHECK MODE) |

## 1. Conclusion

**No platform-level architecture update is needed now.** The approved TRD keeps FEAT-ZD-48600 inside the registered four-repository flow. It keeps the existing runtimes and the inter-service JSON rule, and it follows the rule "Keep every existing signature; add new functions." Every change the TRD lists is additive at component level: new functions, one new optional feed column and one new optional last parameter. None of them changes a platform element: the topology, a runtime, a repository's role, the JSON contract rule, the compatibility rule, or the security, tenancy and storage boundary. The platform architecture document stays as it is.

The approved design (DES-02) already builds on this position. It adds no repository, service, runtime, library or storage (DD-1). It adopts CT-1 with no transport layer (DD-3). Its section A8 lists conditions that would reopen this check, and section 3 below carries each of them (T-1 to T-5). This check confirms that nothing in the current inputs has changed that position.

Sources: feat.trd (section 1, section 3 data rules, section 9); org.architecture; journey:feat.design (A1, A7 DD-1 and DD-3, A8).

## 2. What was checked, and the evidence

Rows K-1 to K-6 are the six platform elements that journey:feat.design A1 names: topology, runtimes, repository roles, JSON contract rule, compatibility rule and security boundary. Rows K-7 to K-10 are supporting checks.

| # | Platform element checked | Evidence | Result | Source |
|---|---|---|---|---|
| K-1 | Topology (repositories and flow) | The TRD touches only ap_pipeline, ap_forecast, ap_aging and ap_portal. These are the "four repositories in one flow" that org.architecture already registers. No repository or service is added, split or retired. The end-to-end harness (C-10) is a new test, not a deployed component. | No change | feat.trd (header "Repositories"; section 1, C-1 to C-10); org.architecture |
| K-2 | Runtimes and technology | New code keeps to Python 3.9, with no newer syntax, and to Node.js 22 CommonJS. These are the runtimes org.architecture names. The TRD chooses no new language, framework or third-party library. | No change | feat.trd (F-15); org.architecture; journey:feat.design (DD-1) |
| K-3 | Repository roles | ap_pipeline still produces Invoice records, ap_forecast still predicts payment delay and ap_aging still computes aging over invoices. ap_portal still renders HTML from JSON. The TRD gives each new function to the repository that already holds that role: delay history in ap_pipeline, risk rule in ap_forecast, overdue list and its serialiser in ap_aging, and the table renderer in ap_portal. | No change | org.architecture; feat.trd (section 1, C-2, C-4, C-6, C-7, C-8) |
| K-4 | Inter-service JSON contract rule | CT-1 (ap_aging → ap_portal) carries `amount` as a two-decimal string and has no date field. This matches the registered rule "JSON between services carries amounts as strings with two decimals and dates as ISO strings". The rule is applied, not changed. | No change | feat.trd (IF-6, D-7, T-A1); org.architecture |
| K-5 | Compatibility rule | Only two existing functions change, and both changes are additive. `transform.to_model` keeps its signature and return shape and adds `paid_date` handling. `build_explanation_request` gains `risk=None` as its last parameter. Everything else is a new function. A feed without `paid_date` (v2) still loads. This complies with "Keep every existing signature; add new functions" and with the baseline rule "existing function signatures and return shapes must not change". | Complies; no rule change | feat.trd (IF-1, IF-1.1, IF-4.1, section 9); org.architecture; file:hobasa-agentic-sdlc/org/org.architecture.md |
| K-6 | Security, tenancy and storage boundary | The TRD lists finance posting, authentication, tenancy and payment execution as not affected (S-1), and no code path acts on a risk value (S-2). It adds no storage, no schema migration and no new persistent data, and every new function is pure and in memory. The ap_aging baseline says "No database, no network, no secrets", and the new ap_aging functions add none of these. | No change | feat.trd (section 1 "Not affected"; S-1, S-2, S-6; section 3 data rules; IF-5.8); file:hobasa-agentic-sdlc/org/org.architecture.md |
| K-7 | Data model | The Invoice record gains `paid_date` (date or None). The feature's data model already registers `paid_date (date or None) [paid_date added in feed v3]`, so no further data-model update is needed here. | Already registered | feat.trd (D-1, D-2, IF-1.6); file:hobasa-agentic-sdlc/features/FEAT-ZD-48600/org-overrides/org.data_model.md |
| K-8 | External provider and model | Provider `acme-llm` and model `acme-text-2` stay unchanged, and `vendor_risk` uses no LLM. The only change at this boundary is in content: with `risk`, the explanation prompt also carries the vendor's risk label (S-5). Whether that is acceptable is open for the Architect (TQ-8). The provider integration stays the same. | No platform change; TQ-8 carried | feat.trd (F-7, IF-3.2, IF-4.4, S-3, S-5, TQ-8) |
| K-9 | Transport between ap_aging and ap_portal | The TRD found no service or transport layer between the two in the code. It proposes only a serialiser function in ap_aging, `overdue_rows_to_json` (C-7). That is not a new service, endpoint or channel. The approved design adopts this choice and adds no transport (DD-3), but marks TQ-3 as still to be confirmed. | No platform change under the approved TRD and design; see trigger T-1 | feat.trd (IF-6, C-7, R-7, TQ-3); journey:feat.design (A3 "Transport for CT-1", DD-3) |
| K-10 | Decision log | The TRD is consistent with D-2026-031 (calendar days), D-2026-044 (Decimal, never float) and D-2026-050 (paid excluded). It found two conflicts inside existing code, `bucket_for(30)` and `formatAmount`, and raised them as separate questions (TQ-1, TQ-2). The TRD leaves both helpers unchanged, so neither changes the platform architecture in this item. | No change | feat.trd (section 9; F-10, F-12; TQ-1, TQ-2); file:hobasa-agentic-sdlc/org/org.decision_log.md; record:memory |

### 2.1 Observations (no action in DES-01)

- **The baseline lists fewer fields and functions than the feature uses.** The platform baseline `org/org.architecture.md` describes ap_aging only: invoices are dicts with `id, due_date, amount, paid`. Under the TRD, `overdue_with_risk` also reads `vendor`, which ap_pipeline records already carry, and the input shape of `aging_buckets` does not change. This is a new function's input, not a change to a platform rule. Whether to refresh the descriptive function and field lists after release is a documentation question (PUQ-1). Sources: file:hobasa-agentic-sdlc/org/org.architecture.md; org.architecture; feat.trd (F-2, F-11, IF-5.7, IF-5.8).
- **The baseline names an "AP API" that no input describes.** It says ap_aging "is a pure Python 3.9 library (src/ap_aging) used by the AP API". The feature override and the TRD name no such component, and the approved design leaves the production caller of the stages open (DQ-1). This is where a CT-1 transport could later appear, so it is linked to trigger T-1 (PUQ-2). Sources: file:hobasa-agentic-sdlc/org/org.architecture.md; org.architecture; feat.trd (IF-6, R-7); journey:feat.design (A4, DQ-1).
- **The TRD header still calls itself a draft.** It reads "This is a draft for the REQ-07 gate (Architect)." The step brief and the context receipt both supply it as the "Approved TRD" (producer REQ-07, commit 3637e51), so this check treats it as approved. Sources: feat.trd (line 4); record:brief; tool view context_list.

## 3. What would trigger the DES-01 action

Any one of these makes the step's action necessary. The platform architecture document would then be updated first, before the affected design is finalised. T-1 to T-5 follow the conditions listed in journey:feat.design A8. T-6 and T-7 complete the set from the TRD.

| ID | Trigger | Linked open item | Source |
|---|---|---|---|
| T-1 | The answer to TQ-3 or DQ-1 adds a new service, endpoint, message channel or transport layer between ap_aging and ap_portal. This includes making the "AP API" carry CT-1. The in-library serialiser C-7 does not count. | TQ-3, PQ-3, R-7, DQ-1, PUQ-2 | feat.trd (IF-6, C-7, R-7, TQ-3); journey:feat.design (DD-3, DQ-1); file:hobasa-agentic-sdlc/org/org.architecture.md |
| T-2 | TQ-1 (`bucket_for`) or TQ-2 (`formatAmount`) is answered with a platform-wide rule, or the fix is taken into this item and changes existing behaviour or a return shape. | TQ-1, TQ-2 | feat.trd (F-10, F-12, TQ-1, TQ-2, R-1, R-2); journey:feat.design (A8) |
| T-3 | Any library gains storage, a database, a schema migration or a network call, for example to keep delay history or risk levels between runs. | None | feat.trd (section 3 data rules, IF-2.6, IF-5.8); file:hobasa-agentic-sdlc/org/org.architecture.md ("No database, no network, no secrets") |
| T-4 | The LLM provider or model changes, or the data-flow policy for the external provider changes. Examples: the TQ-8 answer. Another: PROV-ACME-2026-11, which the TRD says also changes `build_explanation_request`, if it brings a provider or model change. The TRD does not say that it does. | TQ-8, PQ-5, R-3 | feat.trd (S-5, TQ-8, R-3); journey:feat.design (A8) |
| T-5 | The end-to-end harness (C-10) becomes a deployed component instead of a test asset. | None | feat.trd (C-10, IF-8); journey:feat.design (HC-10, A8) |
| T-6 | A change departs from the registered rules: CT-1 sends `amount` as a JSON number or a date that is not an ISO string, or an existing signature or return shape has to change. Examples: the answer to TQ-10 or OQ-B6 alters what `to_model` returns. | T-A1, TQ-10, OQ-B6 | org.architecture; feat.trd (IF-6, T-A1, TQ-10, R-11) |
| T-7 | A new runtime, language version, framework or third-party package is chosen, or tenancy, authentication, finance posting or payment execution comes into scope. One example is a row action that acts on the risk label. | OQ-B9 | feat.trd (F-15, S-1, S-2, IF-7.8); org.architecture |

## 4. Open questions (non-blocking, for the Architect)

| ID | Question | Why it matters |
|---|---|---|
| PUQ-1 | After release, should the function and invoice-field lists be refreshed with the new functions and the `vendor` / `paid_date` keys? The lists are in `org/org.architecture.md` and in the org.architecture override. The new functions are `delays_by_vendor`, `vendor_risk`, `overdue_with_risk`, `overdue_rows_to_json` and `renderOverdueRisk`. This is documentation upkeep, not a platform change. | Keeps the registered architecture current for later agents |
| PUQ-2 | Confirm TQ-3 (who owns CT-1 serialisation and transport) and say what the "AP API" in the platform baseline is. If it, or any new component, carries CT-1 between ap_aging and ap_portal, trigger T-1 applies and DES-01 must run in full. | Decides whether this check still holds; links to DQ-1 in the approved design |

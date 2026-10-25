<!-- input feat.clarifications for DES-04: from hobasa-design-agent/outputs/FEAT-ZD-48600/feat.clarifications.md at commit 36d4dac, produced by DES-03 -->
# feat.clarifications: FEAT-ZD-48600

Questions, assumptions and answers for the coding lanes of FEAT-ZD-48600 (ZD-48600, "Show which overdue invoices belong to vendors that usually pay late"). This is DES-03, loop 1. The workbook outcome: "Assumptions, Questions or Clarifications would be looped till the developer has achieved complete clarity" (record:brief).

This file reads the approved design (feat.design), stories (feat.stories) and TRD (feat.trd) the way a developer about to start would. It lists every doubt found, answers each one from an approved source where possible, states the assumption used where no source answers it, and lists what is still open with an owner. It adds no scope and changes no approved decision. Answers marked **agent judgement** are this agent's reading; no input states them as fact.

| Field | Value | Source |
|---|---|---|
| Trace ID | FEAT-ZD-48600 | record:brief |
| Step | DES-03 Understand HLD or LLD + doubts clarifications (attempt 1) | record:brief |
| Trigger | Task creation | record:brief |
| Owner / gate | Architect / automated checks, independent judge, sampled human evaluation | record:brief |
| Inputs | feat.design (DES-02, revision 722a98f), feat.stories (REQ-08, revision 0590ea4), feat.trd (REQ-07, revision 3637e51) | record:brief; mcp:hobasa-context (context_list) |
| Background read | feat.screens (DES-02, approved) | journey:feat.screens |
| Other files read | Each repository's `ci.py` / `ci.mjs` and `CLAUDE.md`; `ap_portal/package.json`; DES-02 gate decisions | file:ap-aging-sample/ci.py; file:ap-pipeline-sample/ci.py; file:ap-portal-sample/ci.mjs; file:ap-portal-sample/package.json; file:ap-aging-sample/CLAUDE.md; file:ap-pipeline-sample/CLAUDE.md; file:ap-forecast-sample/CLAUDE.md; file:ap-portal-sample/CLAUDE.md; file:hobasa-design-agent/steps/FEAT-ZD-48600/DES-02/gate_decisions_1.json |
| Code read | The four repositories' source and tests from the step's tool view | tool:repo |
| Next step | DES-04 Task Creation reads this file | record:brief (design-steps) |

## 0. Result of this loop

- **No blocking question.** Every story can start on the approved design. Each item still open either has an interim answer the lanes code against, or gates release rather than coding (sections 2 to 7 and 10). Agent judgement: most interim answers are confined to one function, as feat.trd IF-1.7 states for OQ-B6. The exceptions are TQ-3 / DQ-1: a transport between ap_aging and ap_portal would reopen DES-01 (feat.design A8).
- **Precedence rule used for every answer:** the story acceptance criteria and the TRD say *what* must hold. feat.design (approved at DES-02 by the Architect and the UX lead) says *how*. Where the two differ only in form (a type hint, a helper name, `.get` instead of `[]`), follow feat.design. No behavioural conflict was found between the three inputs (section 1). Agent judgement, from journey:feat.design D2 ("Conflict with an approved output: None").
- **What the DES-02 approval does and does not settle.** Both DES-02 approvals carry only the comment "automated checks and the independent judge passed". They record no answer to TQ-1 to TQ-11. The lanes therefore build on feat.design's interim answers (feat.design D3: TQ-3 → DD-3, TQ-6 → allow-list, TQ-7 → plain header row, TQ-9 → `KeyError`, TQ-10 → DD-6, TQ-11 → DD-5). Every acceptance criterion marked **[Pending &lt;ID&gt;]** in feat.stories keeps that marker (file:hobasa-design-agent/steps/FEAT-ZD-48600/DES-02/gate_decisions_1.json; feat.stories SQ-3).

## 1. Differences between the inputs, and which one to follow

| ID | Topic | feat.trd / feat.stories says | feat.design says | Follow | Why | Source |
|---|---|---|---|---|---|---|
| X-1 | `delays_by_vendor` input type | `records: List[Dict]` (IF-2; TK-02.1) | `records: Iterable[Dict]` | feat.design | A list argument behaves identically; the design notes this as the only difference | feat.trd (IF-2); feat.stories (TK-02.1); feat.design (B1.2) |
| X-2 | Paid test in `delays_by_vendor` | `record["paid"]` is true (IF-2.2) | `record.get("paid")` is truthy | feat.design | Same result for every `to_model` record, which always has `paid`; matches the `aging_buckets` idiom. A hand-built record without `paid` is skipped, not an error | feat.trd (IF-2.2, F-2); feat.design (B1.2, NF-3); tool:repo (`transform.py`, `aging.py`) |
| X-3 | Non-array `rows` in `renderOverdueRisk` | "missing or empty" → `''` (IF-7.1; AC-07.4) | missing, `null` or `[]` → `''`; any other non-array → `TypeError` | feat.design | Extends IF-7.1 without contradicting it; DQ-3 still open | feat.trd (IF-7.1); feat.stories (AC-07.4); feat.design (B4.2 step 1, DD-8) |
| X-4 | Serialising the amount | `str(amount.quantize(Decimal("0.01")))` (TK-06.1; IF-6) | `str(Decimal(r["amount"]).quantize(TWO_PLACES))` | feat.design | Same result for a `Decimal` input; the design form also accepts a string amount | feat.stories (TK-06.1); feat.trd (IF-6); feat.design (B3.2) |
| X-5 | Where the ap_portal version lives | `2.3.0` "in `index.js` and `package.json`" (TK-07.5) | `VERSION` step in `index.js` and `package.json` (B4.1) | Both files | The repository note names only `src/ap_portal/index.js`, but `package.json` also carries `"version": "2.3.0"`. Change both, or they drift apart | feat.stories (TK-07.5); feat.design (B4.1); file:ap-portal-sample/CLAUDE.md; file:ap-portal-sample/package.json |
| X-6 | Tests for the ap_portal `TypeError` branches | Not in the TRD tests | B4.5 lists "rejects non-string amounts" only | Add tests for every branch (CL-FE6) | Coverage of at least 90% is a CI gate | feat.design (B4.4, B4.5); file:ap-portal-sample/ci.mjs |

## 2. Cross-lane clarifications

| ID | Doubt | Answer | Status | Source |
|---|---|---|---|---|
| CL-X1 | May a lane start while TQ / OQ / PQ items are open (feat.stories SQ-4)? | Yes. feat.design puts all four lanes in parallel in one stage. Each open item has an interim design whose change is local (for example, IF-1.7: "If OQ-B6 is answered 'reject', only IF-1 changes"). Where a protected QA oracle disagrees with an agent-judgement rule, "the oracle wins and the rule changes" | Answered (design); PM to confirm SQ-4 | feat.design (Part C "Lane dependencies"); feat.trd (IF-1.7, section 5); feat.stories (SQ-4) |
| CL-X2 | Does any lane import another lane's package? | No. ap_forecast consumes DC-2 (`Dict[str, List[int]]`), ap_aging consumes DC-1 and DC-3 (plain dicts), ap_portal consumes CT-1 (JSON). Build against the contract, not against another lane's code | Answered | feat.design (A3, Part C) |
| CL-X3 | Which Python version does CI enforce? | The repositories declare Python 3.9, and CI runs `/usr/bin/python3 ci.py`. `ci.py` compiles with whatever interpreter runs it and has no version or syntax gate. A newer interpreter would therefore accept syntax that Python 3.9 rejects. Developers must keep to 3.9 syntax themselves: no `match`, no `X \| Y` unions, `typing.Dict` / `List` / `Optional` / `Iterable`. The interpreter version on the CI host was not checked in this step (CQ-3) | Answered; CQ-3 open | file:ap-pipeline-sample/CLAUDE.md; file:ap-aging-sample/ci.py; feat.design (Part B conventions, NF-9); feat.trd (F-15) |
| CL-X4 | Where do new tests go, and will CI find them? | Python CI discovers tests under `tests/` with `unittest.defaultTestLoader.discover`. Agent judgement on the unittest default: a new file must be named `test*.py`, for example `tests/test_delays.py`. ap_portal CI runs only `tests/*.test.js`. Adding to the existing test files (feat.stories SA-6) also works | Answered | file:ap-pipeline-sample/ci.py; file:ap-aging-sample/ci.py; file:ap-portal-sample/ci.mjs; feat.stories (SA-6) |
| CL-X5 | What do the static checks flag? | Python: calls to `print`, `eval` or `exec`, bare `except`, lines over 140 characters, in `src/<package>/*.py`. ap_portal: any line matching `console.log` or `eval(` and lines over 140 characters, in `src/ap_portal/*.js`. The ap_portal check is a plain text match, so a **comment** that mentions `console.log` also fails CI (agent judgement from the regular expression) | Answered | file:ap-pipeline-sample/ci.py; file:ap-portal-sample/ci.mjs |
| CL-X6 | How is coverage measured? | Python: `coverage` over `src/<package>` only, threshold 90.0. ap_portal: the line figure of the `all files` row from `node --test --experimental-test-coverage --test-coverage-include=src/**`, threshold 90. A new module with no tests lowers the whole package's figure. Every error branch needs its own test | Answered | file:ap-aging-sample/ci.py; file:ap-portal-sample/ci.mjs; feat.trd (F-14) |
| CL-X7 | Branch and commit naming | Branches: `main` (released), `dev` (integration), `feature/<story>`. Commit messages start with the story ID. Until DES-04 commits tasks, the story ID is the feat.stories label (for example STORY-01) (CQ-5) | Answered; CQ-5 open | file:ap-aging-sample/CLAUDE.md; record:memory (conventions); feat.stories (section 1) |
| CL-X8 | Version step | No input requires one. Each changed package takes a minor step, for the lane to confirm. Versions live in `src/ap_pipeline/__init__.py` (`0.4.0`), `src/ap_forecast/__init__.py` (`0.9.0`), `src/ap_aging/__init__.py` (`1.4.0`) and, for ap_portal, `src/ap_portal/index.js` and `package.json` (`2.3.0`) | Answered (DD-11, lane confirms) | feat.design (DD-11); feat.trd (T-A6); tool:repo; file:ap-portal-sample/package.json |
| CL-X9 | Test data | Synthetic only: ids `SYN-INV-*`, vendors "Synthetic Vendor &lt;letter&gt;"; no secrets or personal data | Answered | feat.trd (S-6); feat.design (SE-4) |
| CL-X10 | Money | `Decimal` end to end in Python, a string in JSON and in ap_portal; never `float`, `Number()`, `parseFloat` or `formatAmount` | Answered | record:memory (D-2026-044, L-011); feat.design (NF-1) |

## 3. Data Engineering lane: ap_pipeline (STORY-01, STORY-02; DT-1, DT-2)

| ID | Doubt | Answer | Status | Source |
|---|---|---|---|---|
| CL-DE1 | Does the `paid_date` parse go inside the existing `try` block? | No. Step 1 builds the record exactly as today inside the existing `try`. Step 2 runs only for a row that passed step 1: `_parse_paid_date(r.get("paid_date"))`. On `ValueError` it appends `{"row": r, "reason": "paid_date"}` (the literal, held in `PAID_DATE_REASON`) and adds no record. If the parse sat inside the existing `try`, the reason would become `"ValueError"` and AC-01.4 would fail | Answered | feat.design (B1.2 steps 1 to 3, DD-6); feat.trd (IF-1.4, IF-1.5, F-3); feat.stories (AC-01.4) |
| CL-DE2 | Missing column, `None`, blank or whitespace-only `paid_date`? | All give `paid_date = None`, and the row is accepted: `raw = (r.get("paid_date") or "").strip()`. A value with surrounding spaces is parsed after `strip()` | Answered | feat.trd (IF-1.1, IF-1.2, F-1); feat.design (B1.2, B1.4) |
| CL-DE3 | Should `due_date` be stripped too, for consistency? | No. Today `due_date` is parsed without `strip()`. Changing it would change existing reject behaviour, against "every existing key and value is unchanged" | Answered | tool:repo (`transform.py`); feat.stories (AC-01.8); feat.trd (IF-1.6) |
| CL-DE4 | Which invalid values should the tests use? | `"2026-13-01"` and `"not-a-date"` (TRD T-2). Agent judgement: avoid compact forms such as an 8-digit date without dashes. Newer Python versions of `date.fromisoformat` accept more formats than 3.9, so such a test could pass or fail depending on the CI interpreter (CL-X3) | Answered | feat.trd (T-2, IF-1.3); feat.stories (AC-01.4) |
| CL-DE5 | Unpaid row with a valid `paid_date` (OQ-B6) | Accepted; the date is kept on the record; `delays_by_vendor` ignores it because the row is unpaid | Interim (DD-7); OQ-B6 open | feat.design (DD-7); feat.trd (IF-1.7); feat.stories (AC-01.7, AC-02.6) |
| CL-DE6 | A row whose `paid` value is not `"true"` (for example `"yes"`) but has a `paid_date` | `paid` is `True` only for `"true"`, case-insensitive. Such a row is an unpaid row with a `paid_date`, so CL-DE5 applies | Answered (existing behaviour) | tool:repo (`transform.py`); feat.trd (F-2) |
| CL-DE7 | `paid_date` before `due_date`, or after the run date | Accepted. No input makes either a reject reason. The negative `delay_days` is kept with no floor (AC-02.4). A future `paid_date` is not checked (assumption CA-3) | Answered / assumption | feat.prd via feat.trd (IF-2.3); feat.stories (AC-02.4) |
| CL-DE8 | Row invalid in an existing field **and** in `paid_date` | Keeps today's reason (an exception type name such as `ValueError`) | Interim (DD-6); TQ-10 open | feat.design (DD-6); feat.trd (IF-1.5, T-A3); feat.stories (AC-01.6) |
| CL-DE9 | Is `delays_by_vendor` exported from `ap_pipeline/__init__.py`? | Not in this feature: the package has no `__all__`. Import it as `from ap_pipeline.delays import delays_by_vendor`. Agent judgement: the end-to-end harness would use the same path, though neither IF-8 nor B5 states it | Interim; DQ-4 open | feat.design (DQ-4); tool:repo (`ap_pipeline/__init__.py`) |
| CL-DE10 | Can the existing tests stay exactly as they are? | Yes. `test_run_maps_and_rejects` expects `reject_reasons` `["ValueError"]` for the bad `due_date` row, and that still holds. Its feed has no `paid_date` column, so it doubles as a feed v2 check | Answered | tool:repo (`tests/test_pipeline.py`); feat.design (B1.5) |
| CL-DE11 | Feed v3 is not yet in production (PQ-6). Does that block DT-1? | No. Build and test on synthetic v3 rows; feed v2 keeps loading (IF-1.1). It gates release, not coding | Answered | feat.design (Part C "External dependencies"); feat.trd (R-4) |

## 4. Data Science lane: ap_forecast (STORY-03, STORY-04; DT-3, DT-4)

| ID | Doubt | Answer | Status | Source |
|---|---|---|---|---|
| CL-DS1 | How do I build boundary histories that predict exactly 30 or 10? | Compare on the rounded prediction, not the raw mean. Python's `round` sends a half-way mean to the even integer. The existing test shows this: `predict_delay_days([-5, 5])` is `2` (values `[0, 5]`). Agent judgement: use single-value histories (`[30]`, `[31]`, `[10]`, `[11]`), whose prediction is the value itself. Use half-way means only in a test written for R-6 on purpose | Answered | feat.trd (IF-3.4, R-6, T-6); tool:repo (`predict.py`, `tests/test_forecast.py`); feat.design (B2.5); record:memory (L-007) |
| CL-DS2 | `history` is an `Iterable`. Can it be a generator? | Yes, if `vendor_risk` calls `predict_delay_days(history)` exactly once and compares the stored result. A second pass over a generator would see it empty (agent judgement on Python iterators) | Answered | feat.trd (IF-3.1); feat.design (B2.2) |
| CL-DS3 | Validate history values in `vendor_risk`? | No. `predict_delay_days` applies `int(d)`, and anything it cannot convert raises from there, as today | Answered | feat.design (B2.4); tool:repo (`predict.py`) |
| CL-DS4 | Does `risk.py` touch the LLM path? | No: no LLM, no network, no read of `models.json`. Design choice: `risk.py` imports only `predict_delay_days`, not `explain`. For AC-03.7, patch `ap_forecast.explain.load_config` to fail and assert that `vendor_risk` still returns | Answered | feat.trd (IF-3.2); feat.design (B2.2, B2.5); feat.stories (AC-03.7) |
| CL-DS5 | `build_explanation_request(..., risk='')` or a value outside high / medium / low | Interim rule: "given" means `risk is not None`, so any non-`None` value is appended as `f" Risk level: {risk}."`, with no validation. An empty string therefore yields `" Risk level: ."`. This falls under TQ-4 | Interim (IF-4.5); TQ-4 open | feat.trd (IF-4.2, IF-4.5, T-A2); feat.stories (AC-04.5) |
| CL-DS6 | Must anything new call `build_explanation_request` with `risk`? | No. IF-8 does not call it, so STORY-04 is not part of the end-to-end story. Only the parameter and its tests are in scope | Answered | feat.stories (SA-5); feat.trd (IF-8) |
| CL-DS7 | PROV-ACME-2026-11 also changes `build_explanation_request` (PQ-5) | Build and test now. T-7 guards the `risk` parameter. Whoever merges second rebases and re-runs T-7. The order of the two changes is still open | Answered for coding; PQ-5 open | feat.design (Part C "External dependencies"); feat.trd (R-3) |
| CL-DS8 | Sending the risk label to `acme-llm` (TQ-8) | Code it as specified; provider and model are unchanged. Confirmation of the data flow is the Architect's (TQ-8), and it gates release, not coding | Interim; TQ-8 open | feat.design (SE-3); feat.trd (S-5) |
| CL-DS9 | Early payments only | `'low'`, because negative delays count as 0 in the prediction | Interim; TQ-5 open | feat.trd (F-6, R-5); feat.stories (AC-03.6) |

## 5. Backend lane: ap_aging (STORY-05, STORY-06; DT-5, DT-6)

| ID | Doubt | Answer | Status | Source |
|---|---|---|---|---|
| CL-BE1 | Reuse `bucket_for` for "more than 30 days"? | No. `bucket_for(30)` returns `"31-60"`, so exactly-30 would be listed, against AC-05.2 (PRD AC-14). Use `days > OVERDUE_AFTER_DAYS` (30) directly. Leave `bucket_for` unchanged; the regression test pins `bucket_for(30) == "31-60"` as current behaviour (TQ-1 decides any fix as a separate item) | Answered | feat.design (DD-4, B3.5); feat.trd (F-10, IF-5.3, R-1); tool:repo (`aging.py`) |
| CL-BE2 | Where does `as_of` come from? Default it to today? | No default. `as_of` is a required argument from the caller, which keeps the function pure. Tests use `AS_OF = date(2026, 10, 25)`, as the existing tests do. Agent judgement: build due dates as `AS_OF - timedelta(days=N)` so that `days` is exactly 30, 31, 42 or 75 | Answered | feat.design (NF-10, B3.2, B3.5); tool:repo (`tests/test_aging.py`) |
| CL-BE3 | Amount input types | `Decimal` from ap_pipeline; `Decimal(inv["amount"])` also accepts a string, as `aging_buckets` does. Then `quantize(Decimal("0.01"))` with the default context (round-half-even). No `float` should ever arrive; no float guard is added (assumption CA-4; CQ-6) | Answered / interim (DD-10, DQ-2) | feat.design (B3.2, DD-10); feat.trd (IF-5.5); record:memory (D-2026-044) |
| CL-BE4 | Missing `vendor`, `id` or `due_date` on an invoice | Missing `vendor`: `KeyError`, with a test (AC-05.10). Agent judgement from the B3.2 algorithm: `due_date` is read for every unpaid invoice, so a missing one raises `KeyError`. `id` and `vendor` are read only when the row is built, so a paid or not-overdue invoice that lacks them does not raise; write the AC-05.10 test with an overdue, unpaid invoice | Interim; TQ-9 open | feat.design (DD-8, B3.4); feat.trd (IF-5.7) |
| CL-BE5 | Sort | `rows.sort(key=lambda row: (-row["days"], row["id"]))`: days descending, then `id` ascending as text. Pipeline ids are strings, so the comparison is well defined | Answered | feat.trd (IF-5.6, F-2); feat.design (B3.2) |
| CL-BE6 | Duplicate invoice ids, negative amounts (credit notes) | No input addresses either. Both are passed through: duplicates are both listed, and a negative amount serialises as `"-15.00"`-style text, which the ap_portal pattern accepts (assumption CA-5) | Assumption | feat.design (B3.2, B4.2) |
| CL-BE7 | Non-finite amounts such as `NaN` or `Infinity` | Agent judgement on Python `Decimal`: `to_model` builds `Decimal(r["amount"])` with no finiteness check, so such a feed value could reach ap_aging. `quantize` would then return `NaN` or raise `InvalidOperation`, and ap_portal would throw `TypeError` for `"NaN"`. Agent judgement: this is existing ap_pipeline behaviour that no input names, so no lane adds handling in this feature (CA-6, CQ-1) | Assumption; CQ-1 open | tool:repo (`transform.py`); feat.design (B4.4); feat.trd (F-2) |
| CL-BE8 | `overdue_rows_to_json` output format | `json.dumps` of a list of dicts with keys in CT-1 order: `id`, `vendor`, `days` (`int`), `amount` (two-decimal string), `risk`; `'[]'` for no rows. Agent judgement: tests should `json.loads` the result and compare values, not compare whole raw strings, because separators and ASCII escaping are `json.dumps` defaults | Answered | feat.design (A3 CT-1, B3.2); feat.trd (IF-6) |
| CL-BE9 | Risk values from `risk_by_vendor` | Passed through as given; missing vendor gives `'unknown'`. ap_aging does not validate them, and ap_portal allow-lists the class | Answered | feat.trd (IF-5.4, IF-7.4); feat.design (DC-3) |
| CL-BE10 | Transport from ap_aging to ap_portal | None is added. ap_aging returns the CT-1 string, and the consumer (in this feature, only the end-to-end harness) parses it. A transport would re-trigger DES-01 | Interim (DD-3); TQ-3, DQ-1 open | feat.design (DD-3, A3, A8); feat.trd (R-7) |

## 6. Frontend lane: ap_portal (STORY-07; DT-7)

| ID | Doubt | Answer | Status | Source |
|---|---|---|---|---|
| CL-FE1 | What is the exact expected output? | feat.screens FS-1: one line, `<table class="overdue-risk">`, a header row with no class (`Invoice`, `Vendor`, `Days overdue`, `Amount`), one `<tr class="risk-…">` per row with four cells, the amount cell `class="amount"`, then `</table>`. No `<thead>` / `<tbody>`, no inline style, no tokens. FS-2: `''` | Answered (TQ-7 open on header form) | journey:feat.screens (sections 2 to 4); feat.trd (IF-7.2, IF-7.3) |
| CL-FE2 | Re-sort rows in the portal? | No. Keep the given order; ap_aging sorts | Answered | journey:feat.screens (FS-1 annotations); feat.stories (AC-07.1) |
| CL-FE3 | Amount handling | String only, pattern `^-?\d+(\.\d{1,2})?$`; pad to two decimals as a string (`"980.5"` → `980.50`, `"980"` → `980.00`). A non-string, an exponent, a thousands separator or three decimals throws `TypeError`. Never `Number()`, `parseFloat` or `formatAmount` | Interim (DD-5, DD-8); TQ-11, DQ-3 open | feat.design (B4.2, B4.4); feat.trd (IF-7.5) |
| CL-FE4 | `days` | Must satisfy `Number.isInteger(row.days)`, else `TypeError`; rendered as integer text | Interim (DD-8); DQ-3 open | feat.design (B4.2 step 3, B4.4) |
| CL-FE5 | Escaping | `escapeHtml(String(value))` on `id` and `vendor` for `&`, `<`, `>`, `"`, `'`. The class comes from the allow-list `RISK_CLASSES`, so an unexpected value renders `risk-unknown` | Interim; OQ-B8, TQ-6 open | feat.design (B4.2); feat.trd (IF-7.4, IF-7.7) |
| CL-FE6 | Which tests beyond feat.design B4.5? | Agent judgement, to keep line coverage of at least 90%: also test a non-array `rows` (for example the unparsed JSON string) → `TypeError`; a non-integer `days` → `TypeError`; `null` → `''`; an amount with an exponent, a thousands separator or three decimals → `TypeError` | Answered | feat.design (B4.4, B4.5); file:ap-portal-sample/ci.mjs |
| CL-FE7 | Touch `formatAmount`, `LABELS` or `renderAgingSummary`? | No. They stay unchanged and their tests pass unchanged; fixing `formatAmount` is TQ-2, a separate item | Answered | feat.design (B4.1, DD-5); feat.trd (C-9, F-12) |
| CL-FE8 | Show the risk as text or a column? Add row actions? | No to both. The row class is the only risk signal, and there are no row actions | Interim; OQ-B9, PQ-P7 open | feat.trd (IF-7.8); journey:feat.screens (section 7) |
| CL-FE9 | `id` or `vendor` that is not a string (`null`, a number) | CT-1 guarantees strings. The design converts with `String(value)` and adds no check, so `null` would render as the text `null` (CQ-2) | Assumption; CQ-2 open | feat.design (A3 CT-1, B4.2) |
| CL-FE10 | How do tests load the package? | `require('ap_portal')` with `NODE_PATH=src`, through `node:test` | Answered | tool:repo (`tests/portal.test.js`); file:ap-portal-sample/ci.mjs |

## 7. Testing Agent: end-to-end (STORY-08)

| ID | Doubt | Answer | Status | Source |
|---|---|---|---|---|
| CL-T1 | Wiring | `records, report = pipeline.run(csv_text)`; `delays = delays_by_vendor(records)`; `risk_by_vendor = {v: vendor_risk(h) for v, h in delays.items()}`; `rows = overdue_with_risk(records, as_of, risk_by_vendor)`; `payload = overdue_rows_to_json(rows)`; Node.js 22 `renderOverdueRisk(JSON.parse(payload))` | Answered | feat.trd (IF-8); feat.design (B5) |
| CL-T2 | `as_of` | Agent judgement: a fixed date, not today, so the exactly-30-days invoice stays at 30 on every run | Assumption | feat.stories (AC-08.1); feat.design (NF-10) |
| CL-T3 | Feed content | It must include an exactly-30-days invoice, a paid invoice, a row with an invalid `paid_date` and a vendor with no paid history | Answered | feat.trd (T-17); feat.stories (AC-08.1, AC-08.4) |
| CL-T4 | Who wires the stages in production? | No input names a component; in this feature only the harness wires them | Open (DQ-1) | feat.design (DQ-1, A4) |
| CL-T5 | When does it run? | After the four lanes are merged. It is not in the coding plan | Answered | feat.design (B5) |

## 8. Assumptions used by the lanes (new in this step)

| ID | Assumption | Used in | Basis |
|---|---|---|---|
| CA-1 | Where the TRD or stories and feat.design differ only in form, feat.design governs (section 1) | All lanes | feat.design approved at DES-02; no behavioural conflict found (feat.design D2) |
| CA-2 | Interim answers in feat.design D3 hold until an owner answer is recorded; [Pending] criteria stay pending | All lanes | file:hobasa-design-agent/steps/FEAT-ZD-48600/DES-02/gate_decisions_1.json; feat.stories (SA-2, SQ-3) |
| CA-3 | A `paid_date` after the run date is accepted (no input makes it invalid) | DE | feat.trd (IF-1.3, IF-1.4) |
| CA-4 | No float reaches `overdue_with_risk`; no float guard is added | BE | record:memory (D-2026-044); feat.trd (F-2) |
| CA-5 | Duplicate ids and negative amounts pass through unchanged | BE, FE | feat.design (B3.2, B4.2) |
| CA-6 | Non-finite feed amounts are existing ap_pipeline behaviour and out of scope (agent judgement: no input names them, and the stories add no scope beyond S-1 to S-7) | BE, FE | feat.stories (header, section 8 "No new scope"); tool:repo (`transform.py`) |
| CA-7 | Story labels from feat.stories are the IDs for branches and commits until DES-04 sets task IDs | All lanes | record:memory (conventions); file:ap-aging-sample/CLAUDE.md |

## 9. Open questions

### 9.1 New in this step (all non-blocking)

| ID | Question | Interim answer | Owner (proposed) | What changes if answered differently |
|---|---|---|---|---|
| CQ-1 | Should `to_model` reject non-finite amounts (`NaN`, `Infinity`)? Today `Decimal(...)` may accept them (agent judgement on Python) | Out of scope; no handling added (CA-6) | Architect / PM (new item) | A new reject reason in ap_pipeline only |
| CQ-2 | Should `renderOverdueRisk` throw `TypeError` for a non-string `id` or `vendor`, like the amount and `days` checks (DD-8)? | `String(value)`, no check | Architect (with DQ-3) | One check and one test in ap_portal |
| CQ-3 | Which Python version is `/usr/bin/python3` on the CI host? Should CI add a Python 3.9 syntax gate? | Developers keep to 3.9 syntax themselves (CL-X3) | Architect / DevOps | CI script only |
| CQ-4 | Should `build_explanation_request` restrict `risk` to `high` / `medium` / `low` (an empty string gives `" Risk level: ."`)? Part of TQ-4 | Append any non-`None` value | Architect | `explain.py` and T-7 only |
| CQ-5 | Branch and commit IDs: the feat.stories labels, or the task IDs DES-04 will create? | feat.stories labels (CA-7) | PM (DES-04) | Branch names only |
| CQ-6 | Should `overdue_with_risk` reject a `float` amount with `TypeError` instead of converting it? | No guard (CA-4) | Architect | One check and one test in ap_aging |

### 9.2 Carried (unchanged; none blocks coding)

| Group | IDs | Interim answer used | Source |
|---|---|---|---|
| TRD | TQ-1 to TQ-11 | feat.design D3 (TQ-3 → DD-3, TQ-6 → allow-list, TQ-7 → plain header, TQ-9 → `KeyError`, TQ-10 → DD-6, TQ-11 → DD-5); TQ-1, TQ-2 are separate items; TQ-4, TQ-5, TQ-8 as in section 4 | feat.trd (7.2); feat.design (D3) |
| Design | DQ-1 to DQ-5 | As in feat.design D3 | feat.design (D3) |
| Stories | SQ-1 to SQ-4 | SQ-4 answered for coding in CL-X1, pending PM confirmation | feat.stories (6.1) |
| PRD / BRD / prototype | OQ-B1 to OQ-B10, PQ-1 to PQ-6, PQ-P1 to PQ-P7 | As in feat.trd and feat.design | feat.stories (6.2); feat.trd (7.3) |

## 10. Readiness per story

| Story | Lane / task | Ready to build | Pending markers kept | Gates release, not coding |
|---|---|---|---|---|
| STORY-01 | DE / DT-1 | Yes | AC-01.6 (TQ-10), AC-01.7 (OQ-B6) | Feed v3 in production (PQ-6) |
| STORY-02 | DE / DT-2 (after DT-1) | Yes | AC-02.6 (OQ-B6) | — |
| STORY-03 | DS / DT-3 | Yes | AC-03.5 (PQ-1), AC-03.6 (TQ-5) | — |
| STORY-04 | DS / DT-4 | Yes | AC-04.5 (TQ-4) | PROV-ACME-2026-11 order (PQ-5); TQ-8 |
| STORY-05 | BE / DT-5 | Yes | AC-05.6 (PQ-2), AC-05.10 (TQ-9) | — |
| STORY-06 | BE / DT-6 (after DT-5) | Yes | AC-06.1 to AC-06.4 (TQ-3) | — |
| STORY-07 | FE / DT-7 | Yes | AC-07.2 (TQ-7), AC-07.6 (TQ-11, OQ-B10), AC-07.7 (OQ-B8), AC-07.8 (TQ-6), AC-07.9 (OQ-B9, PQ-P7) | — |
| STORY-08 | Testing Agent, after merge | Yes, after the four lanes | AC-08.1 (PQ-2) | — (DQ-1 stays open, non-blocking) |

Sources: feat.stories (sections 2 to 4); feat.design (Part C, D3).

## 11. How the loop continues

The feature issue takes answers in the form `/answer <STEP>: <text>` (file:hobasa-agentic-sdlc/features/FEAT-ZD-48600/COMMUNICATION.md). Proposed loop (agent judgement): a developer raises a new doubt there for DES-03. The next loop of this step adds it here with an ID (CL-… or CQ-…), an answer from an approved source or an interim assumption, and an owner. When an owner answers a TQ, DQ, CQ, OQ or PQ item, the matching [Pending] criteria in feat.stories (SA-2) can be updated. DES-04 reads this file as an input (design-steps).

## 12. Validation check

| Check | Result | Source |
|---|---|---|
| Every input read: feat.design, feat.stories, feat.trd | Pass | record:brief |
| Every story and lane has its doubts listed and answered or assigned | Pass (sections 3 to 7, 10) | feat.stories (section 1) |
| No approved decision contradicted; differences resolved by a stated rule | Pass (section 1, CA-1) | feat.design (D2) |
| No new scope, technology or contract | Pass | feat.design (DD-1) |
| Decisions D-2026-031, D-2026-044, D-2026-050; lessons L-007, L-011 | Consistent (CL-X10, CL-BE1, CL-BE3, CL-DS1) | record:memory |
| Blocking questions | None | — |

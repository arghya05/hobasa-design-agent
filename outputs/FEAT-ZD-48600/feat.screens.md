# feat.screens: FEAT-ZD-48600

Final screen specification for FEAT-ZD-48600 (ZD-48600, "Show which overdue invoices belong to vendors that usually pay late"). This is a draft for the DES-02 gate (Architect, UX lead).

**Delivery form.** "Figma is not connected in this test run" (org.design_library), and no Figma connector is approved for this step (record:brief). A Figma MCP connector was requested in this step (capability request; see feat.design DQ-5). Until it is approved, the final screens are delivered here in text form: the exact HTML each state renders, annotated frames, and the screen rules. When Figma is connected, each frame below maps one to one to a Figma frame with the same name.

This file promotes the approved low-fidelity prototype (feat.prototype WF-1, WF-2) to a build-ready screen specification. It adds no function, data or behaviour beyond feat.prd and feat.trd. Statements marked "design choice (agent judgement)" are for the UX lead to accept or change.

| Field | Value | Source |
|---|---|---|
| Trace ID | FEAT-ZD-48600 | record:brief |
| Gate | Architect, UX lead | record:brief |
| Inputs | feat.prototype (approved), org.design_library, feat.prd, feat.trd | record:brief |
| UI repository | ap_portal (Node.js 22 CommonJS) | org.architecture; feat.trd (C-8) |
| Component | `renderOverdueRisk(rows)` in `src/ap_portal/index.js` | feat.trd (IF-7); feat.design (B4) |
| Screens | One: SC-1 Overdue risk table | feat.prototype (section 2) |

## 1. Screen inventory

| ID | Screen / component | Frames | States | User story | Source |
|---|---|---|---|---|---|
| SC-1 | Overdue risk table in the AP portal | FS-1 Populated, FS-2 Empty | ST-1 to ST-5 from the prototype; ST-6 to ST-8 defensive, new here (section 5) | US-1 | feat.prototype (sections 2, 3, 5); feat.prd (US-1, AC-19 to AC-21) |

The host page (navigation, title, filters, placement) is not specified in any input and is not designed (feat.prototype PQ-P2). Loading, error, pagination and long-list states are not defined in the PRD and are not designed (feat.prototype PQ-P3; feat.prd PQ-4).

## 2. Design library applied

The DL-1 to DL-4 numbering is from feat.prototype (section 1); the quoted rules are from org.design_library.

| # | Design-library rule (quoted) | Final screen | Source |
|---|---|---|---|
| DL-1 | "overdue risk table uses class 'overdue-risk'" | Root element `<table class="overdue-risk">` | org.design_library |
| DL-2 | "each row has class 'risk-high', 'risk-medium', 'risk-low' or 'risk-unknown'" | Every body row `<tr>` carries exactly one of the four classes; any other `risk` value renders `risk-unknown` (feat.trd IF-7.4) | org.design_library; feat.trd (IF-7.4) |
| DL-3 | "columns Invoice, Vendor, Days overdue, Amount (two decimals)" | Header cells in this order and wording; amount always two decimals, from the CT-1 string | org.design_library; feat.prd (AC-19) |
| DL-4 | "render nothing when there are no rows" | Empty string; no table, header or message | org.design_library; feat.prd (AC-21) |

**Variables (tokens).** The design library defines no colour, spacing or typography variables and no visual style for the `risk-*` classes (org.design_library; feat.prototype PQ-P1). The final screen therefore applies **no tokens and no inline styles**: it emits only the class hooks above, so the portal stylesheet can style them once tokens exist. No colour is invented here.

## 3. Structure (exact markup)

Design choice carried from feat.trd IF-7.2: no `<thead>`/`<tbody>`, header row without a class, matching the plain style of the existing `renderAgingSummary` (tool:repo, `index.js`). Pending TQ-7.

```
table.overdue-risk
├── tr                      (header; no class)
│   ├── th  "Invoice"
│   ├── th  "Vendor"
│   ├── th  "Days overdue"
│   └── th  "Amount"
└── tr.risk-{high|medium|low|unknown}   × one per row, in the given order
    ├── td  {id, HTML-escaped}
    ├── td  {vendor, HTML-escaped}
    ├── td  {days, integer text}
    └── td.amount  {amount, two-decimal string}
```

| Cell | Content rule | Source |
|---|---|---|
| Invoice | `row.id`, HTML-escaped (`&`, `<`, `>`, `"`, `'`) | feat.trd (IF-7.3, IF-7.7) |
| Vendor | `row.vendor`, HTML-escaped | feat.trd (IF-7.3, IF-7.7) |
| Days overdue | `row.days` as integer text; calendar days after the due date, always more than 30 | feat.trd (IF-7.6, IF-6); record:memory (D-2026-031) |
| Amount | `row.amount` as a string, padded to two decimals as a string if shorter; never `Number()`, `parseFloat` or `formatAmount`; cell class `amount`, as in `renderAgingSummary` | feat.trd (IF-7.3, IF-7.5); record:memory (D-2026-044); tool:repo |

## 4. Frames

### FS-1: SC-1 Populated (states ST-1, ST-3, ST-4, ST-5)

Synthetic data from the approved prototype dataset (feat.prototype section 6); the values are illustrative, not facts.

Input (CT-1 rows):

```json
[
  {"id": "SYN-INV-004", "vendor": "Synthetic Vendor A", "days": 75, "amount": "1250.00", "risk": "high"},
  {"id": "SYN-INV-002", "vendor": "Synthetic Vendor B", "days": 42, "amount": "980.50", "risk": "medium"},
  {"id": "SYN-INV-003", "vendor": "Synthetic Vendor D", "days": 42, "amount": "15.00", "risk": "unknown"},
  {"id": "SYN-INV-001", "vendor": "Synthetic Vendor C", "days": 31, "amount": "300.00", "risk": "low"}
]
```

Exact output of `renderOverdueRisk(rows)` (one line in the real output; wrapped here for reading):

```html
<table class="overdue-risk">
<tr><th>Invoice</th><th>Vendor</th><th>Days overdue</th><th>Amount</th></tr>
<tr class="risk-high"><td>SYN-INV-004</td><td>Synthetic Vendor A</td><td>75</td><td class="amount">1250.00</td></tr>
<tr class="risk-medium"><td>SYN-INV-002</td><td>Synthetic Vendor B</td><td>42</td><td class="amount">980.50</td></tr>
<tr class="risk-unknown"><td>SYN-INV-003</td><td>Synthetic Vendor D</td><td>42</td><td class="amount">15.00</td></tr>
<tr class="risk-low"><td>SYN-INV-001</td><td>Synthetic Vendor C</td><td>31</td><td class="amount">300.00</td></tr>
</table>
```

Annotated frame:

```
 AP portal  (host page not specified: PQ-P2)
                 table.overdue-risk
                ┌─────────────┬──────────────────────┬──────────────┬─────────────┐
                │ Invoice     │ Vendor               │ Days overdue │      Amount │
                ├─────────────┼──────────────────────┼──────────────┼─────────────┤
 [risk-high]    │ SYN-INV-004 │ Synthetic Vendor A   │           75 │     1250.00 │
 [risk-medium]  │ SYN-INV-002 │ Synthetic Vendor B   │           42 │      980.50 │
 [risk-unknown] │ SYN-INV-003 │ Synthetic Vendor D   │           42 │       15.00 │
 [risk-low]     │ SYN-INV-001 │ Synthetic Vendor C   │           31 │      300.00 │
                └─────────────┴──────────────────────┴──────────────┴─────────────┘
 Annotations (designer notes, not rendered):
  - [risk-…] tags are the row class only; there is no visible Risk column or text (AC-19, OQ-B9).
  - Order is set by ap_aging: days descending, then id ascending (SYN-INV-002 before SYN-INV-003).
    The portal keeps the given order and does not re-sort.
  - SYN-INV-003: vendor has no risk entry, so risk-unknown (AC-17).
  - Not listed (filtered in ap_aging): SYN-INV-005, exactly 30 days (AC-14); SYN-INV-006, paid (AC-15).
  - No row actions: no pay, post, approve or chase (NFR-6; PQ-P7).
```

Sources: feat.prototype (WF-1, section 6); feat.prd (AC-14 to AC-17, AC-19, AC-20, NFR-6); feat.trd (IF-5.6, IF-7.2 to IF-7.8).

### FS-2: SC-1 Empty (state ST-2)

Input: `[]` (or no rows). Output: `''` (empty string).

```
 AP portal  (host page not specified: PQ-P2)
 ┌────────────────────────────────────────────────────────────────────┐
 │   (nothing rendered: renderOverdueRisk returns "")                 │
 └────────────────────────────────────────────────────────────────────┘
 Annotation: no table, no header, no "no results" message. The bracketed text is a designer note.
```

Sources: feat.prototype (WF-2, ST-2); org.design_library (DL-4); feat.prd (AC-21); feat.trd (IF-7.1).

## 5. States

| ID | State | Trigger | Rendered | Source |
|---|---|---|---|---|
| ST-1 | Populated | `rows` non-empty | FS-1 structure | feat.prototype (ST-1); feat.prd (AC-19) |
| ST-2 | Empty | `rows` empty or missing | `''` (FS-2) | feat.prototype (ST-2); feat.prd (AC-21) |
| ST-3 | Row risk variants | `risk` is `high`, `medium`, `low`, `unknown` | Same row; only the class changes | feat.prototype (ST-3); feat.prd (AC-20) |
| ST-4 | Unknown vendor risk | Vendor not in `risk_by_vendor` | Row class `risk-unknown` | feat.prototype (ST-4); feat.prd (AC-17) |
| ST-5 | Days tie | Equal `days` | Order as received (id ascending from ap_aging) | feat.prototype (ST-5); feat.prd (AC-16, P-A2) |
| ST-6 | Defensive: unexpected `risk` | `risk` outside the four values | Row class `risk-unknown` | feat.trd (IF-7.4, TQ-6) |
| ST-7 | Defensive: markup in text | `id` or `vendor` contains `<`, `&`, `"` and similar | Escaped text, for example `Synthetic Vendor &lt;E&gt;` | feat.trd (IF-7.7, OQ-B8) |
| ST-8 | Defensive: short amount | `amount` `"980.5"` | `980.50` | feat.trd (IF-7.5, TQ-11) |

ST-6 to ST-8 are not visible design states; they are rendering safeguards the TRD specifies, listed so the UX lead sees every output the component can produce.

## 6. Copy

| Element | Text | Source |
|---|---|---|
| Header 1 | Invoice | org.design_library; feat.prd (AC-19) |
| Header 2 | Vendor | org.design_library; feat.prd (AC-19) |
| Header 3 | Days overdue | org.design_library; feat.prd (AC-19) |
| Header 4 | Amount | org.design_library; feat.prd (AC-19) |
| Empty state | None | org.design_library (DL-4) |

Use "overdue"; never the regulatory label "Late" (feat.prd NFR-10; OQ-B2 open).

## 7. Accessibility and interaction

| Item | Final screen | Source |
|---|---|---|
| Risk signal | Row class only while OQ-B9 is open. If the portal later styles `risk-*` by colour alone, risk would be conveyed by colour only; the UX lead should decide visible text or a non-colour cue with OQ-B9 / PQ-P4 | feat.prd (AC-19, OQ-B9); feat.prototype (PQ-P4) |
| Header cells | Real `<th>` cells (feat.trd IF-7.2). Agent judgement: this lets assistive technology announce the column headings | feat.trd (IF-7.2) |
| Interaction | None: no row actions, links or buttons; the risk label is advisory only | feat.prd (NFR-6); feat.prototype (PQ-P7) |
| Amount alignment | Right-aligned in the prototype (design choice). The markup carries `class="amount"` and no inline style. Design intent (agent judgement): the portal stylesheet, which no input shows, aligns it through that class | feat.prototype (section 7); tool:repo (`renderAgingSummary` uses `class="amount"`) |

## 8. Check against the approved prototype and PRD

| Item | Prototype / PRD | Final screen | Result |
|---|---|---|---|
| Table class, row classes, headings, empty | DL-1 to DL-4; AC-19 to AC-21 | Sections 2 to 5 | Pass |
| Order and tie | AC-16; ST-5 | FS-1 order as given | Pass |
| Amount without float | NFR-3; prototype "Display the amount string exactly as given" | String display; padding of a one-decimal string is the TRD safeguard (IF-7.5) | Pass, with TQ-11 open on padding |
| Visible risk | AC-19; OQ-B9 | Class only | Follows AC-19; OQ-B9 open |
| No actions | NFR-6; PQ-P7 | None | Pass |
| Escaping | NFR-7; OQ-B8 | Escaped (TRD default) | Follows TRD; OQ-B8 open |
| Fidelity change | WF-1, WF-2 were low fidelity | Same layout, now with exact markup; no tokens added | No conflict |

## 9. Open questions (non-blocking)

| ID | Question | Source |
|---|---|---|
| SQ-S1 | Approve the Figma connector so FS-1 and FS-2 can be published as Figma frames (requested in this step) | org.design_library; feat.prototype (PQ-P6) |
| — | Carried: PQ-P1 (tokens for `risk-*`), PQ-P2 (host page), PQ-P3 (other states), PQ-P4 / OQ-B9 (visible risk), PQ-P7 (row actions), OQ-B8 (escaping), OQ-B10 / TQ-11 (amount padding), TQ-6 (class allow-list), TQ-7 (header form), OQ-B2 ("overdue" vs "Late") | feat.prototype (9.1); feat.prd (6.1); feat.trd (7.2) |

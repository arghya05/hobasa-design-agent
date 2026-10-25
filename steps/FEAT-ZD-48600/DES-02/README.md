# DES-02 Design Document (Design agent)

**Item:** FEAT-ZD-48600 · **status:** done
**Trigger:** TRD Completion  
**Action:** Creating the architecture design document with HLD & LLD  
**Outcome expected:** Architecture Design document with HLD and LLD

## Inputs (what the agent was given)

| Item | Prepared input (this repository) | Read from (producer in GitHub) | Commit | Produced by |
|---|---|---|---|---|
| feat.prd | hobasa-design-agent/inputs/FEAT-ZD-48600/DES-02/feat.prd.md | hobasa-requirement-agent/outputs/FEAT-ZD-48600/feat.prd.md | 908b58d | REQ-05 |
| feat.prototype | hobasa-design-agent/inputs/FEAT-ZD-48600/DES-02/feat.prototype.md | hobasa-requirement-agent/outputs/FEAT-ZD-48600/feat.prototype.md | 8fce3b3 | REQ-06 |
| feat.trd | hobasa-design-agent/inputs/FEAT-ZD-48600/DES-02/feat.trd.md | hobasa-requirement-agent/outputs/FEAT-ZD-48600/feat.trd.md | 3637e51 | REQ-07 |
| org.architecture | hobasa-design-agent/inputs/FEAT-ZD-48600/DES-02/org.architecture.md | hobasa-agentic-sdlc/features/FEAT-ZD-48600/org-overrides/org.architecture.md | a6b3e2a | source |
| org.data_model | hobasa-design-agent/inputs/FEAT-ZD-48600/DES-02/org.data_model.md | hobasa-agentic-sdlc/features/FEAT-ZD-48600/org-overrides/org.data_model.md | a6b3e2a | source |
| org.design_library | hobasa-design-agent/inputs/FEAT-ZD-48600/DES-02/org.design_library.md | hobasa-agentic-sdlc/features/FEAT-ZD-48600/org-overrides/org.design_library.md | a6b3e2a | source |

Full brief per attempt: `brief_attempt<N>.md` (trigger event, owner answers, tool view, refusal reasons).

## Outputs (what the agent produced)

| Item | GitHub file |
|---|---|
| feat.design | hobasa-design-agent/outputs/FEAT-ZD-48600/feat.design.md |
| feat.screens | hobasa-design-agent/outputs/FEAT-ZD-48600/feat.screens.md |

## How the agent worked

- Approach: I followed the design-steps skill for DES-02. First I read all six contracted inputs: the PRD, prototype, TRD, org.architecture, org.data_model and org.design_library. I also read the approved stories (via context_get), the DES-01 check record, the requirement file and the code in the step's tool view. The approved TRD already fixes the interfaces (IF-1 to IF-8, CT-1), so the design builds on it rather than re-deriving it.

The HLD covers context, components, the inter-repository data contracts (DC-1 to DC-4 and CT-1), data flow, non-functional needs, security and tenancy, and design decisions DD-1 to DD-11, each citing its source.

The LLD covers each repository: modules, function signatures, algorithms, data structures, error handling, and tests mapped to TRD tests T-1 to T-17 and story criteria. Build tasks name their components, tests and size.

I promoted the approved low-fidelity wireframes to feat.screens. It holds the exact HTML for each state, annotated frames, copy, accessibility notes and a check against the prototype and PRD. org.design_library says Figma is not connected and no Figma connector is approved, so I filed a capability request for one and delivered the screens in text form.

Coding plan: no lane imports another lane's code, and each lane builds against a documented data contract, so all four lanes run in one parallel stage.

A verifier subagent checked both outputs against the inputs and code. I applied its fixes: a dangling reference, a contradiction in empty-rows handling, unlabelled design choices, story ID citations, a sequence-diagram arrow, the source of the DL numbering, and the external dependencies.
- Tools used: Skill (design-steps), Read, Grep, Glob, mcp__hobasa-context__context_list, mcp__hobasa-context__context_get (feat.stories), mcp__hobasa-context__memory_search, mcp__hobasa-context__capabilities_list, mcp__hobasa-context__capability_request (figma MCP), Agent (verifier), Write, Edit
- Helper subagents: Agent
- Connector calls: none

## Attempts (checks before any person saw the output)

| Attempt | Session | Checks failed | Judge unsupported | Judge scores |
|---|---|---|---|---|
| 1 | new | 0 | 0 | {'correct': 5, 'complete': 4, 'grounded': 5, 'useful': 5} |

## Improvements the independent judge suggests (non-blocking, for the people at the gate)

- feat.design cites `journey:feat.stories` (header table, B5, Part C, D3). That is not one of the citation forms the brief allows. Cite it as file:hobasa-requirement-agent/outputs/FEAT-ZD-48600/feat.stories.md. The word "approved" in the header row also needs its own source: the stories file still calls itself a draft for the REQ-08 gate, and only the REQ-08 approval commit shows it was approved.
- B3.5 `test_overdue_missing_vendor_raises`: in the B3.2 algorithm, `vendor` is only read after the paid filter and the `days > 30` filter. A paid or not-yet-overdue invoice without `vendor` therefore does not raise. The test should say it uses an unpaid invoice more than 30 days past due. Otherwise a test author could write a fixture that never reaches the `KeyError`.
- A8 lists the DES-01 triggers T-1 to T-6 in prose but leaves out T-5 (new runtime, language version or repository, or a changed signature). Add it for completeness.
- DD-8 (ap_portal throws `TypeError` on any bad row) means one malformed row stops the whole table from rendering. DQ-3 states this, but the gate would benefit from a short note on the user-facing effect, or a pointer to which component catches the error, given that DQ-1 (who wires the stages in production) is open.
- Part C gives no owner or stage for the end-to-end harness (HC-10 / B5) in the coding plan. It says only that the harness runs after the lanes. Name the Testing Agent step that consumes it, so DES-04 or the scheduler can pick it up.
- feat.screens is delivered as a text and HTML spec, not Figma frames. The capability request is filed and flagged (SQ-S1 / DQ-5), but the UX lead should know that the workbook outcome "Approved final Figma screens" is only met in substitute form until the connector is approved.

Details: `result_attempt<N>.json` (claims with sources), `checks_attempt<N>.json`, `judge_attempt<N>.json`.

## Human evaluation

| Role | By | Decision | Scores | Comment or reason |
|---|---|---|---|---|
| Architect | sim.architect | approve | — | automated checks and the independent judge passed |
| UX lead | sim.ux_lead | approve | — | automated checks and the independent judge passed |

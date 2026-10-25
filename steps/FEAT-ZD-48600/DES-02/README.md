# DES-02 · Design Document

**Agent:** Design · **Item:** FEAT-ZD-48600 · **Status:** ✅ done · approved in run-20261025-201308-feat-zd-48600, reused
**Approved by:** Architect: approve by sim.architect; UX lead: approve by sim.ux_lead

**What this step does:** Creating the architecture design document with HLD & LLD

**Expected outcome:** Architecture Design document with HLD and LLD

**Started by:** TRD Completion

## Input: what the agent received

| # | Input | What it is | Produced by | Open |
|---|---|---|---|---|
| 1 | `feat.prd` | Approved PRD | — | not applicable for this item, or passed by reference |
| 2 | `feat.prototype` | FigJam flow and Figma Make prototype | — | not applicable for this item, or passed by reference |
| 3 | `feat.trd` | Approved TRD | — | not applicable for this item, or passed by reference |
| 4 | `org.architecture` | Platform architecture, contracts and tenancy rules | — | not applicable for this item, or passed by reference |
| 5 | `org.data_model` | Data Model and Knowledge Graph | — | not applicable for this item, or passed by reference |
| 6 | `org.design_library` | Hobasa design library and variables (tokens) | — | not applicable for this item, or passed by reference |

The full brief the agent was given, attempt by attempt: `brief_attempt<N>.md` in this folder.

## Output: what the agent produced

| # | Output | What it is | Open |
|---|---|---|---|
| 1 | `feat.design` | HLD, LLD and contracts | [Markdown](https://github.com/arghya05/hobasa-design-agent/blob/main/outputs/FEAT-ZD-48600/feat.design.md) · [HTML](https://htmlpreview.github.io/?https://raw.githubusercontent.com/arghya05/hobasa-design-agent/main/docs/FEAT-ZD-48600/DES-02_feat-design.html) · [PDF](https://github.com/arghya05/hobasa-design-agent/blob/main/docs/FEAT-ZD-48600/DES-02_feat-design.pdf) |
| 2 | `feat.screens` | Approved final Figma screens | [Markdown](https://github.com/arghya05/hobasa-design-agent/blob/main/outputs/FEAT-ZD-48600/feat.screens.md) · [HTML](https://htmlpreview.github.io/?https://raw.githubusercontent.com/arghya05/hobasa-design-agent/main/docs/FEAT-ZD-48600/DES-02_feat-screens.html) · [PDF](https://github.com/arghya05/hobasa-design-agent/blob/main/docs/FEAT-ZD-48600/DES-02_feat-screens.pdf) |

## Human evaluation

| Role | By | Decision | Scores (correct, complete, grounded, useful) | Comment or reason |
|---|---|---|---|---|
| Architect | sim.architect | approve | — | automated checks and the independent judge passed |
| UX lead | sim.ux_lead | approve | — | automated checks and the independent judge passed |

## Files in this folder (audit trail)

- `brief_attempt<N>.md`: the exact brief the agent received
- `result_attempt<N>.json`: the agent's structured answer (approach, tools, claims with their sources, open questions)
- `checks_attempt<N>.json`: what the automated guardrail checks found
- `judge_attempt<N>.json`, `judge_brief_attempt<N>.md`: the independent judge
- `outputs/`: the outputs as approved

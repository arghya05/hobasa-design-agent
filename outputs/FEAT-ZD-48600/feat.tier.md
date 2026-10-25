# feat.tier: FEAT-ZD-48600

Risk tier for FEAT-ZD-48600 (ZD-48600, "Show which overdue invoices belong to vendors that usually pay late"). This is DES-04 (attempt 1), a draft for the Developer gate.

## 1. Tier

| Field | Value | Source |
|---|---|---|
| Tier | **T2** | org.risk_map |
| Basis | "No finance posting, authentication, tenancy or payment execution in this change; tier T2." | org.risk_map |
| Stated by | org.risk_map (feature override `features/FEAT-ZD-48600/org-overrides/org.risk_map.md`, commit a6b3e2a) | record:brief; org.risk_map |
| Carried by earlier steps | feat.design (header, SE-1: "Not touched; tier T2"); feat.stories (header: "T2"; advisory) | feat.design; feat.stories |
| Design agent check | Concurs: no task touches a high-risk path (section 2) | this step; feat.tasks |
| Confirmed by | **Pending: Developer at the DES-04 gate.** The Design agent does not self-approve | record:brief |

## 2. Check of each task against the high-risk paths

The risk map states that this change has no finance posting, authentication, tenancy or payment execution (org.risk_map). Agent judgement: this step treats those four areas as the high-risk paths behind the tier and checks each task's files in budget (feat.tasks section 3) against them.

| Task | Repository / files in budget | Finance posting | Authentication | Tenancy | Payment execution | Source |
|---|---|---|---|---|---|---|
| TSK-01 | ap_pipeline `transform.py` | No | No | No | No | feat.design (HC-1); tool:repo (`transform.py`) |
| TSK-02 | ap_pipeline new `delays.py` | No | No | No | No | feat.design (HC-2) |
| TSK-03 | ap_forecast new `risk.py` | No | No | No | No | feat.design (HC-4) |
| TSK-04 | ap_forecast `explain.py` | No | No | No | No | feat.design (HC-5, SE-3); tool:repo (`explain.py`) |
| TSK-05 | ap_aging `aging.py` | No | No | No | No | feat.design (HC-6); tool:repo (`aging.py`) |
| TSK-06 | ap_aging `aging.py` | No | No | No | No | feat.design (HC-7) |
| TSK-07 | ap_portal `index.js` | No | No | No | No | feat.design (HC-8, SE-2); tool:repo (`index.js`) |
| TSK-08 | Test assets only | No | No | No | No | feat.design (HC-10) |

All new functions are pure and in-memory, with no I/O, network or input mutation (feat.design NF-10). No code path acts on a risk value, and the portal table has no row actions (feat.design NF-6). Tenancy and security impact is clear: there is no tenancy surface (feat.design A6).

## 3. Items noted, which do not change the tier

| Item | Why it stays T2 | Source |
|---|---|---|
| The vendor risk label is advisory only | No automatic action is taken on it | org.risk_map; feat.design (NF-6) |
| TSK-04 sends the risk label to the configured LLM provider (`acme-llm`) when `risk` is given | Provider and model are unchanged; this is an external data flow, not one of the risk-map areas. Architect confirmation (TQ-8) gates release | feat.design (SE-3); feat.clarifications (CL-DS8) |
| TSK-07 output encoding | Handled by HTML escaping and the risk-class allow-list | feat.design (SE-2, NF-7) |

## 4. What would raise the tier

Agent judgement, from the four areas in org.risk_map and the DES-01 reopen triggers in feat.design A8 (which concern the platform architecture, not the tier directly): any change that adds finance posting, authentication, tenancy or payment execution; a code path that acts on a risk value automatically; or a transport, service or deployed harness between the repositories (which also re-triggers DES-01). None is in feat.tasks.

## 5. Controls that follow from T2

Machine checks per lane: compile, unit tests, coverage of at least 90% and static checks (feat.design NF-9). Independent verification also uses protected oracles owned by QA and SMEs; the critic re-check applies to T3 only, so it does not apply here (record:brief, G7).

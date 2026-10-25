# DES-04 · Task Creation

**Agent:** Design · **Item:** FEAT-ZD-48600 · **Status:** ✅ done · approved in an earlier run, reused
**Approved by:** Developer: approve by sim.developer

**What this step does:** Not defined in workbook; complete the readiness action

**Expected outcome:** Committed user story
Task creation for actual engineering effort for each committed user story

**Started by:** design document with HLD & LLD

## Input: what the agent received

| # | Input | What it is | Produced by | Open |
|---|---|---|---|---|
| 1 | `feat.stories` | Epics, stories and tasks with numbered AC, frame links, assumptions and open items (latest revision) | Requirement agent · REQ-08 | [input copy](https://github.com/arghya05/hobasa-design-agent/blob/main/inputs/FEAT-ZD-48600/DES-04/feat.stories.md) · [original](https://github.com/arghya05/hobasa-requirement-agent/blob/main/outputs/FEAT-ZD-48600/feat.stories.md) · [HTML](https://htmlpreview.github.io/?https://raw.githubusercontent.com/arghya05/hobasa-requirement-agent/main/docs/FEAT-ZD-48600/REQ-08_feat-stories.html) · [PDF](https://github.com/arghya05/hobasa-requirement-agent/blob/main/docs/FEAT-ZD-48600/REQ-08_feat-stories.pdf) |
| 2 | `feat.design` | HLD, LLD and contracts | Design agent · DES-02 | [input copy](https://github.com/arghya05/hobasa-design-agent/blob/main/inputs/FEAT-ZD-48600/DES-04/feat.design.md) · [original](https://github.com/arghya05/hobasa-design-agent/blob/main/outputs/FEAT-ZD-48600/feat.design.md) · [HTML](https://htmlpreview.github.io/?https://raw.githubusercontent.com/arghya05/hobasa-design-agent/main/docs/FEAT-ZD-48600/DES-02_feat-design.html) · [PDF](https://github.com/arghya05/hobasa-design-agent/blob/main/docs/FEAT-ZD-48600/DES-02_feat-design.pdf) |
| 3 | `feat.clarifications` | Questions, assumptions and answers | Design agent · DES-03 | [input copy](https://github.com/arghya05/hobasa-design-agent/blob/main/inputs/FEAT-ZD-48600/DES-04/feat.clarifications.md) · [original](https://github.com/arghya05/hobasa-design-agent/blob/main/outputs/FEAT-ZD-48600/feat.clarifications.md) · [HTML](https://htmlpreview.github.io/?https://raw.githubusercontent.com/arghya05/hobasa-design-agent/main/docs/FEAT-ZD-48600/DES-03_feat-clarifications.html) · [PDF](https://github.com/arghya05/hobasa-design-agent/blob/main/docs/FEAT-ZD-48600/DES-03_feat-clarifications.pdf) |
| 4 | `org.risk_map` | High-risk paths per repository for tiering | organisation source | [input copy](https://github.com/arghya05/hobasa-design-agent/blob/main/inputs/FEAT-ZD-48600/DES-04/org.risk_map.md) · [original](https://github.com/arghya05/hobasa-agentic-sdlc/blob/main/features/FEAT-ZD-48600/org-overrides/org.risk_map.md) |

The full brief the agent was given, attempt by attempt: `brief_attempt<N>.md` in this folder.

## Output: what the agent produced

| # | Output | What it is | Open |
|---|---|---|---|
| 1 | `feat.tasks` | Engineering tasks with goal, AC, dependencies and size budget | [Markdown](https://github.com/arghya05/hobasa-design-agent/blob/main/outputs/FEAT-ZD-48600/feat.tasks.md) · [HTML](https://htmlpreview.github.io/?https://raw.githubusercontent.com/arghya05/hobasa-design-agent/main/docs/FEAT-ZD-48600/DES-04_feat-tasks.html) · [PDF](https://github.com/arghya05/hobasa-design-agent/blob/main/docs/FEAT-ZD-48600/DES-04_feat-tasks.pdf) |
| 2 | `feat.tier` | Risk tier T1–T3 and who confirmed it | [Markdown](https://github.com/arghya05/hobasa-design-agent/blob/main/outputs/FEAT-ZD-48600/feat.tier.md) · [HTML](https://htmlpreview.github.io/?https://raw.githubusercontent.com/arghya05/hobasa-design-agent/main/docs/FEAT-ZD-48600/DES-04_feat-tier.html) · [PDF](https://github.com/arghya05/hobasa-design-agent/blob/main/docs/FEAT-ZD-48600/DES-04_feat-tier.pdf) |

## Checks and approval

| Attempt | Session | Problems found by the checks | Unsupported statements (judge) | Judge scores |
|---|---|---|---|---|
| 1 | new | 0 | 0 | {'correct': 5, 'complete': 4, 'grounded': 5, 'useful': 5} |

Improvements the independent judge suggests (non-blocking):

- feat.tier section 5 cites "record:brief, G7" for "protected oracles owned by QA and SMEs; the critic re-check applies to T3 only". The DES-04 step brief has no G7 and no wording about the critic re-check. The content comes from the design agent's own definition (hobasa-design-agent/.claude/agents/design.md:36, "G7 Independent verification ... critic re-check for T3"), and feat.trd section 5 also mentions protected oracles. Cite that file instead of record:brief. The paraphrase also leaves out the SonarQube and schema machine checks that G7 lists.
- feat.tasks puts the new ap_forecast and ap_aging tests in new files (tests/test_risk.py, tests/test_overdue.py). The approved stories assigned them to the existing files (feat.stories TK-03.2 → test_forecast.py, TK-05.2 → test_aging.py, SA-6). feat.design B2.5/B3.5 and CL-X4 allow either choice, but the tasks should say outright that they replace the story-level task wording, so the stories and tasks don't disagree.
- The workbook outcome is a 'Committed user story', but the output leaves commitment, priority and iteration to the PM (SQ-1, SQ-4) and gives no numeric estimate (TKQ-1). That is reasonable and openly stated. A short checklist of what the Developer gate must confirm before the stories count as committed (TA-1..TA-5, TKQ-1..3) would make the gate decision easier.
- TSK-08's size M is agent judgement, and it is labelled as such. Give the Testing Agent a scope budget like the other tasks have, for example how many synthetic feed rows to use and which assertions are required (AC-08.1..4).

## How the agent worked

- **Approach:** I read the four contracted inputs: feat.stories, feat.design, feat.clarifications and org.risk_map. I also used the code in the step's tool view. I gave each committed story one engineering task. STORY-01 to STORY-07 map to the approved design tasks DT-1 to DT-7, and the end-to-end story became TSK-08 for the Testing Agent. Each task has a goal, the design components it touches, its story acceptance criteria and pending markers, the tests to add (from feat.design B1.5 to B4.5 and feat.clarifications CL-FE6), its dependencies and a size budget. For the size budget I kept the design's S/M class and added a scope budget: the files the task may change and the files it must not change. I set no numeric estimate, because no input gives one. To avoid merge conflicts between parallel tasks, I gave each package's version step to one task and split the test files by task. Both are marked as agent judgement. I took the tier T2 from org.risk_map and checked every task's files against the four areas it names: finance posting, authentication, tenancy and payment execution. The tier is marked pending confirmation by the Developer at the gate. A verifier subagent checked both drafts against the inputs. I then fixed the issues it raised: the version numbers are now labelled as the current versions, the S budget no longer contradicts the version-step assignment, I removed the unsourced 'branch from dev' claim, the 'high-risk areas' framing is now marked as agent judgement, and the QA-oracle claim now cites record:brief.
- **Tools used:** Skill (design-steps), Read, mcp__hobasa-context__memory_search, Write, Edit, Agent (verifier)
- **Helper subagents:** Agent
- **Connector calls:** none

## Human evaluation

| Role | By | Decision | Scores (correct, complete, grounded, useful) | Comment or reason |
|---|---|---|---|---|
| Developer | sim.developer | approve | — | automated checks and the independent judge passed |

## Files in this folder (audit trail)

- `brief_attempt<N>.md`: the exact brief the agent received
- `result_attempt<N>.json`: the agent's structured answer (approach, tools, claims with their sources, open questions)
- `checks_attempt<N>.json`: what the automated guardrail checks found
- `judge_attempt<N>.json`, `judge_brief_attempt<N>.md`: the independent judge
- `outputs/`: the outputs as approved

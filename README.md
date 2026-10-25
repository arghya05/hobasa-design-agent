# Hobasa Design agent

Design Agent (incl. Review Agent) in the Hobasa agentic SDLC. Owner: Architect or development manager; UX lead for screens.. It runs on Claude Code: every step is one Claude Code session started by the Hobasa spine, with this agent's prompt and step skill (`.claude/`), helper subagents, guardrail hooks and the shared context server.

**Reads from GitHub:** each step's inputs are prepared in `inputs/<trace>/<STEP>/` with the repository, path and commit they came from (other agents' `outputs/`, the requirements and organisation sources in `hobasa-agentic-sdlc`, code in the `*-sample` repositories).

**Writes back to GitHub:** approved outputs in `outputs/<trace>/`; every step run in `steps/<trace>/<STEP>/` (brief, attempts, claims, checks, independent judge, human decision). Code changes go to the `*-sample` repositories. Journey, evaluations and shared memory: `hobasa-agentic-sdlc`.

## Steps

| Step | Name | Reads | Writes | Gate |
|---|---|---|---|---|
| DES-01 | Platform Architecture Update ¹ | feat.trd, org.architecture | feat.platform_update | checks + sampled evaluation |
| DES-02 | Design Document | feat.prd, feat.prototype, feat.trd, org.architecture, org.data_model, org.design_library | feat.design, feat.screens | Architect, UX lead |
| DES-03 | Understand HLD or LLD+ Doubts Clarifications | feat.design, feat.stories, feat.trd | feat.clarifications | checks + sampled evaluation |
| DES-04 | Task Creation | feat.stories, feat.design, feat.clarifications, org.risk_map | feat.tasks, feat.tier | Developer |

<!-- work:FEAT-ZD-48600:start -->
## Work done for FEAT-ZD-48600

| Step | Name | Status | Input | Output | Details |
|---|---|---|---|---|---|
| DES-01 | Platform Architecture Update ¹ | checked | `feat.trd`, `org.architecture` | `feat.platform_update`: [Markdown](https://github.com/arghya05/hobasa-design-agent/blob/main/outputs/FEAT-ZD-48600/checks/DES-01_feat.platform_update.md) · [HTML](https://htmlpreview.github.io/?https://raw.githubusercontent.com/arghya05/hobasa-design-agent/main/docs/FEAT-ZD-48600/DES-01_feat-platform_update.html) · [PDF](https://github.com/arghya05/hobasa-design-agent/blob/main/docs/FEAT-ZD-48600/DES-01_feat-platform_update.pdf) | [step record](https://github.com/arghya05/hobasa-design-agent/blob/main/steps/FEAT-ZD-48600/DES-01/README.md) |
| DES-02 | Design Document | done | `feat.prd`, `feat.prototype`, `feat.trd`, `org.architecture`, `org.data_model`, `org.design_library` | `feat.design`: [Markdown](https://github.com/arghya05/hobasa-design-agent/blob/main/outputs/FEAT-ZD-48600/feat.design.md) · [HTML](https://htmlpreview.github.io/?https://raw.githubusercontent.com/arghya05/hobasa-design-agent/main/docs/FEAT-ZD-48600/DES-02_feat-design.html) · [PDF](https://github.com/arghya05/hobasa-design-agent/blob/main/docs/FEAT-ZD-48600/DES-02_feat-design.pdf)<br>`feat.screens`: [Markdown](https://github.com/arghya05/hobasa-design-agent/blob/main/outputs/FEAT-ZD-48600/feat.screens.md) · [HTML](https://htmlpreview.github.io/?https://raw.githubusercontent.com/arghya05/hobasa-design-agent/main/docs/FEAT-ZD-48600/DES-02_feat-screens.html) · [PDF](https://github.com/arghya05/hobasa-design-agent/blob/main/docs/FEAT-ZD-48600/DES-02_feat-screens.pdf) | [step record](https://github.com/arghya05/hobasa-design-agent/blob/main/steps/FEAT-ZD-48600/DES-02/README.md) |
| DES-03 | Understand HLD or LLD+ Doubts Clarifications | done | `feat.design`, `feat.stories`, `feat.trd` | `feat.clarifications`: [Markdown](https://github.com/arghya05/hobasa-design-agent/blob/main/outputs/FEAT-ZD-48600/feat.clarifications.md) · [HTML](https://htmlpreview.github.io/?https://raw.githubusercontent.com/arghya05/hobasa-design-agent/main/docs/FEAT-ZD-48600/DES-03_feat-clarifications.html) · [PDF](https://github.com/arghya05/hobasa-design-agent/blob/main/docs/FEAT-ZD-48600/DES-03_feat-clarifications.pdf) | [step record](https://github.com/arghya05/hobasa-design-agent/blob/main/steps/FEAT-ZD-48600/DES-03/README.md) |
| DES-04 | Task Creation | done | `feat.stories`, `feat.design`, `feat.clarifications`, `org.risk_map` | `feat.tasks`: [Markdown](https://github.com/arghya05/hobasa-design-agent/blob/main/outputs/FEAT-ZD-48600/feat.tasks.md) · [HTML](https://htmlpreview.github.io/?https://raw.githubusercontent.com/arghya05/hobasa-design-agent/main/docs/FEAT-ZD-48600/DES-04_feat-tasks.html) · [PDF](https://github.com/arghya05/hobasa-design-agent/blob/main/docs/FEAT-ZD-48600/DES-04_feat-tasks.pdf)<br>`feat.tier`: [Markdown](https://github.com/arghya05/hobasa-design-agent/blob/main/outputs/FEAT-ZD-48600/feat.tier.md) · [HTML](https://htmlpreview.github.io/?https://raw.githubusercontent.com/arghya05/hobasa-design-agent/main/docs/FEAT-ZD-48600/DES-04_feat-tier.html) · [PDF](https://github.com/arghya05/hobasa-design-agent/blob/main/docs/FEAT-ZD-48600/DES-04_feat-tier.pdf) | [step record](https://github.com/arghya05/hobasa-design-agent/blob/main/steps/FEAT-ZD-48600/DES-04/README.md) |
<!-- work:FEAT-ZD-48600:end -->

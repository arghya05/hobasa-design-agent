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

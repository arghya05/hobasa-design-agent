# Evaluate step DES-01: Platform Architecture Update ¹ for FEAT-ZD-48600

- Workbook action: If the feature needs an architectural change at the platform level,so firstly it gets updated in the platform architectural level document
- Workbook outcome: Update in the platform architecture

## Outputs to evaluate
- `hobasa-design-agent/outputs/FEAT-ZD-48600/checks/DES-01_feat.platform_update.md`

## Inputs the agent was given
- feat.trd: `hobasa-design-agent/inputs/FEAT-ZD-48600/DES-01/feat.trd.md`
- org.architecture: `hobasa-design-agent/inputs/FEAT-ZD-48600/DES-01/org.architecture.md`

## Trigger event
{"check_mode": "condition not met: TRD flags a platform-level change"}

## Other GitHub files the agent read (valid sources when cited as file:<repository>/<path>)
- `hobasa-agentic-sdlc/org/org.architecture.md`
- `hobasa-agentic-sdlc/org/org.decision_log.md`

Shared memory and conventions: `memory/MEMORY.md` and `CLAUDE.md` in the SDLC repository.

## The step brief the agent was given (a valid source, cited as record:brief)

# Step DES-01: Platform Architecture Update ¹ (attempt 1) for FEAT-ZD-48600

- Workbook trigger: TRD Completion
- Workbook action: If the feature needs an architectural change at the platform level,so firstly it gets updated in the platform architectural level document
- Workbook outcome: Update in the platform architecture
- Owner of this step: Architect
- Gate (who approves this output): no gate: automated checks, the independent judge and sampled human evaluation

## Inputs (GitHub checkout; read them with Read, or context_get for a receipt)
- feat.trd (Approved TRD): `hobasa-design-agent/inputs/FEAT-ZD-48600/DES-01/feat.trd.md` (from hobasa-requirement-agent/outputs/FEAT-ZD-48600/feat.trd.md at commit 3637e51)
- org.architecture (Platform architecture, contracts and tenancy rules): `hobasa-design-agent/inputs/FEAT-ZD-48600/DES-01/org.architecture.md` (from hobasa-agentic-sdlc/features/FEAT-ZD-48600/org-overrides/org.architecture.md at commit 3b03f15)

## Outputs (write each file in full; the spine commits and pushes after evaluation and approval)
- feat.platform_update (Platform architecture change (when flagged)): write `hobasa-design-agent/outputs/FEAT-ZD-48600/checks/DES-01_feat.platform_update.md`

## CHECK MODE
The condition for this step is not met in this run (condition not met: TRD flags a platform-level change). Execute the step as a check: using your inputs, the tool view and (coding agents) the repository and its CI, determine with evidence whether the step's action is needed now. Write each declared output file as a check record: what you checked, the evidence, the conclusion, and what would trigger the action. Do not change code, configuration, tests or environments.

## How to work
Decide the best way to do this step from the inputs and the workbook action, plan it (TodoWrite for multi-part work), then do it. Helper subagents you can delegate to: explorer, verifier (use the verifier before you finish when the output makes many factual statements). Use the shared memory (memory_search) for decisions and lessons. Connectors approved for you: api:pypi (Python package metadata: latest version, licence, release dates (GET /pypi/<package>/json)), api:npm (npm package metadata: versions, licence (GET /<package>)). Use one only when your inputs do not hold what you need; request a missing one with capability_request. You may also read any other file in the GitHub checkouts your step needs (for example other requirements when grouping, or code); cite it as file:<repository>/<path>.

## Trigger event (cite as record:intake)
{
  "check_mode": "condition not met: TRD flags a platform-level change"
}

Finish with the structured result: approach, tools_used, claims (each citing an input item name, a tool view, api:<name>, mcp:<server>, file:<repository>/<path> for any other GitHub file you read, record:intake, record:owner_answer, record:memory or record:brief for this brief; a claim drawing on several sources lists them separated by ';'; every number must appear in what you cite), open questions (BLOCKING: only when you cannot finish), summary.
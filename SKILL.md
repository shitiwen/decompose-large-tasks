---
name: decompose-large-tasks
description: Turn a large, ambiguous, multi-step goal into a dependency-aware plan of small, independently verifiable tasks without implementing it. Use when a user asks to split, break down, plan, scope, sequence, parallelize, or estimate a substantial coding or product implementation task; when work spans several files, components, systems, sessions, or agents; or when the task is too large to start safely. Do not use for obvious one-step changes, ordinary explanations, or implementation unless the user separately authorizes execution.
---

# Decompose Large Tasks

Create a plan that another capable agent can execute without rediscovering scope, architecture, dependencies, or the meaning of done. Plan only: do not implement, commit, create tracker items, dispatch agents, or mutate external systems unless the user explicitly asks for that additional action.

Preserve repository instructions and the user's stated architecture, tools, output location, and execution method. A plan grants no new authority.

## Choose the output mode

Use the mode requested by the user:

- `response-only`: return the complete plan in the conversation and write nothing.
- `file`: save the plan at the user- or repository-specified path.
- `tracker`: create external tasks only when the user explicitly requests it and the relevant integration is available.

Default to `response-only`. If the user asks for a durable plan but provides no path, use the repository's existing convention. If none exists, propose `docs/plans/YYYY-MM-DD-<goal>.md`; do not silently overwrite an active plan.

For the exact output structure, read [references/plan-format.md](references/plan-format.md). For sizing decisions, difficult boundaries, migration work, or final review, read [references/decomposition-rules.md](references/decomposition-rules.md).

## Workflow

### 1. Establish the planning contract

Extract and state:

- Goal: the user-visible outcome, not an implementation activity.
- Scope: included behavior, users, systems, and environments.
- Exclusions: what this plan deliberately will not change.
- Constraints: compatibility, security, performance, dependency, time, platform, and repository rules.
- Evidence of success: what must be observable when the goal is achieved.
- Inputs: specifications, issue text, designs, code, logs, prior decisions, and existing plans.
- Unknowns: missing facts that could change task boundaries or architecture.

Ask a question only when the answer would materially change the plan and cannot be learned safely from available sources. Otherwise record a bounded assumption and place its validation before dependent implementation.

Never convert a user preference into a hard requirement or invent product behavior to make the plan look complete.

### 2. Inspect the real system before splitting work

For repository work, read the applicable instructions and trace the current path end to end. Inspect relevant entry points, callers, data flow, interfaces, tests, configuration, and established patterns. Search for an existing helper or implementation before planning a new one.

Identify:

- Current owner of each relevant responsibility.
- Formal entry point and runtime path.
- Data or state transitions.
- Integration boundaries and external systems.
- Existing test and validation commands.
- Legacy, fallback, mock, feature-flagged, or alternate paths that could be mistaken for the target.

Do not prescribe exact files, symbols, commands, or line numbers unless verified. Label an unverified path as `candidate`, never as fact.

For non-repository work, inspect the equivalent real workflow, stakeholders, inputs, decision points, and deliverables.

### 3. Run the readiness gate

Classify the planning state before producing tasks:

- `READY`: outcome, target, scope, constraints, and evidence are sufficiently defined.
- `READY_WITH_ASSUMPTIONS`: work can be planned if named assumptions are validated by early evidence tasks.
- `READINESS_BLOCKED`: a missing decision or fact makes a responsible plan impossible.
- `READINESS_REVIEW_REQUIRED`: the requested direction conflicts with observed system facts or repository constraints.

When blocked, return the smallest set of missing decisions or evidence needed to continue. Do not pad the response with speculative implementation tasks.

For rewrites, migrations, new runtimes, cross-boundary state, permissions, billing, destructive data changes, or agent control flow, require additional readiness evidence:

- Why a local fix or compatible extension is insufficient.
- Current responsibility -> current owner -> proposed owner.
- Migration and coexistence behavior.
- Legacy retirement conditions.
- Rollback or containment path.
- The first real end-to-end thin slice.

### 4. Build requirement traceability

Assign stable IDs to requirements and constraints: `R1`, `R2`, and so on. Keep each statement atomic and testable.

Create a traceability table mapping every requirement to:

- Owning task or evidence task.
- Acceptance evidence.
- Final user journey or integration check, when applicable.

Every requirement must have exactly one primary owning task. Other tasks may support it. If no task owns a requirement, the plan is incomplete. If a task owns no requirement, challenge whether it is needed.

### 5. Separate evidence from implementation

Turn high-risk unknowns into early evidence tasks when they could invalidate the approach. Examples include confirming an API capability, measuring a limit, reproducing a bug on the formal path, validating a migration, or proving an authentication boundary.

Each evidence task must state:

- Question being answered.
- Cheapest valid method.
- Evidence to capture.
- Pass and fail criteria.
- Decision unlocked by each result.
- Stop action and resume point when it fails.

Do not hide research inside a later implementation task.

### 6. Design vertical slices

Prefer tasks that deliver one observable behavior through every necessary layer. Avoid horizontal batches such as “build all models,” “build all APIs,” then “connect the UI.”

Put the first thin, real user path before broad infrastructure expansion. Use the formal entry point, real integration boundary, and representative data whenever safe and available. Mocks may support tests but cannot replace final evidence for a requirement whose value depends on the real target.

Reuse existing code, platform features, and dependencies before introducing new abstractions or packages. Do not add scaffolding for hypothetical future work.

### 7. Right-size every task

A task is valid only if it:

- Produces one reviewable outcome.
- Has one primary reason to change.
- Can be completed in one focused execution context.
- Leaves the project in a coherent, verifiable state.
- Has concrete acceptance criteria and a verification method.
- Can be approved or rejected independently of neighboring tasks.
- Names its dependencies and the interfaces it consumes or produces.

Split a task when it combines independently reviewable outcomes, crosses unrelated subsystems, has two unrelated failure modes, cannot be rolled back coherently, or uses “and” to join separable deliverables.

Merge setup, configuration, generated files, documentation, and cleanup into the task whose outcome requires them. Do not create ceremonial tasks for these items unless they are independently risky or deliverable.

Use `XS`, `S`, `M`, or `L` as relative size, not time promises. `L` means “split again” unless the coupling is intrinsic and explicitly explained.

### 8. Write executable task contracts

Each task must contain:

- ID and outcome-oriented title.
- Requirement IDs covered.
- Outcome and reason.
- Preconditions and dependencies.
- Verified files or components likely affected.
- Interfaces consumed and produced.
- Ordered implementation steps at the detail level justified by risk.
- Acceptance criteria phrased as observable facts.
- Exact automated checks when verified; otherwise the method for discovering the command.
- Manual or end-to-end check when automated checks are insufficient.
- Evidence to retain.
- Rollback or containment boundary when failure could affect data, users, compatibility, cost, or availability.
- Explicit exclusions that prevent scope creep.

Do not use placeholders such as “handle edge cases,” “add tests,” “implement validation,” “as appropriate,” or “similar to Task 2.” Name the relevant cases, expected behavior, and verification.

Include code snippets only when an exact contract, signature, migration shape, or fragile sequence must be frozen. Do not invent large implementations inside the plan.

### 9. Build the dependency graph and execution waves

Represent dependencies explicitly. Distinguish:

- Hard dependency: the downstream task cannot start without the upstream output.
- Validation dependency: implementation can start, but completion waits for evidence.
- Integration dependency: independently built work must meet at a checkpoint.

Group ready tasks into waves:

- Tasks in the same wave may run in parallel only if they do not edit the same ownership surface, mutate the same state, or depend on unstabilized interfaces.
- Freeze shared interfaces before parallel consumers rely on them.
- Put high-risk evidence and critical-path work early.
- Add an integration checkpoint after parallel branches rejoin.

Do not dispatch agents merely because parallel waves exist. The plan describes safe parallelism; execution requires separate user authorization.

### 10. Define checkpoints and real acceptance

Add checkpoints where multiple tasks or layers combine. A checkpoint must specify what is integrated, the command or procedure, expected evidence, failure owner, and rollback or stop action.

For each core user journey, define a final acceptance case with:

- Target product, build, entry point, route, environment, provider, identity, and data.
- Preconditions and permissions.
- Exact user actions or external trigger.
- User-visible outcome.
- System evidence such as persisted state, API result, event, artifact, log, or screenshot.
- Negative or boundary case.
- Forbidden substitutes such as a legacy route, mock provider, fallback, or adjacent feature.

Keep completion states distinct:

- `IMPLEMENTED`: required code or artifact exists.
- `ENABLED`: target configuration activates it.
- `EXECUTED`: the formal path ran.
- `VERIFIED`: required evidence matches the acceptance contract.
- `COMPLETE`: every required task and final acceptance case is verified, with no required work remaining.

A planning session may claim only `PLAN_READY`, never implementation or completion.

### 11. Self-review before delivery

Check all of the following and repair the plan before presenting it:

- Every requirement maps to an owning task and evidence.
- Every task maps to at least one requirement or justified enabling need.
- Dependencies form an executable graph with no unexplained cycle.
- Parallel tasks do not collide on the same interface, file ownership, migration, or mutable state.
- Interface names, data shapes, paths, and commands are internally consistent.
- Risky assumptions appear as evidence tasks before dependent work.
- The first real user thin slice appears early.
- Each task has observable acceptance and verification.
- Failure, rollback, legacy, and data handling are covered where relevant.
- Final acceptance exercises the formal target, not a substitute.
- The plan contains no placeholders or invented repository facts.
- No task is large merely because the plan stopped decomposing.

Assign the final status:

- `PLAN_READY` when all mandatory checks pass.
- `PLAN_READY_WITH_ASSUMPTIONS` when bounded, early validation tasks cover every remaining assumption.
- `PLAN_NOT_VERIFIABLE` when tasks exist but the real outcome cannot be verified.
- `READINESS_BLOCKED` or `READINESS_REVIEW_REQUIRED` when the readiness gate requires it.

### 12. Hand off without starting implementation

Present or save the plan, point to unresolved decisions, and recommend the next ready wave. Ask for review when assumptions or architecture decisions remain. Do not begin implementation in the same step unless the user already authorized execution and the applicable workflow permits it.


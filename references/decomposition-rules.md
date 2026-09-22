# Decomposition rules and quality gates

Read this reference when task boundaries are unclear, the plan involves migration or destructive state, parallel execution is proposed, or the final self-review finds gaps.

## 1. Boundary tests

Use these tests in order. A boundary is good when most answers are yes.

### Outcome test

- Does the task make one externally observable or independently reviewable thing true?
- Can a reviewer reject this outcome without rejecting neighboring outcomes?
- Is the title a result rather than an activity?

Prefer “Users can reset a password through the formal login flow” over “Implement password reset backend and UI.”

### Coherence test

- Does the task leave the system buildable or otherwise coherent?
- Are setup, configuration, documentation, and generated outputs folded into the outcome they enable?
- Is cleanup coupled to the change that makes it safe?

### Verification test

- Can completion be decided from stated evidence rather than author confidence?
- Do acceptance criteria cover success and the most important boundary or failure behavior?
- Does the verification exercise the changed behavior, not merely a neighboring unit?

### Context test

- Can one implementer understand and finish the task without loading unrelated subsystems?
- Are consumed and produced interfaces explicit?
- Are repository facts verified instead of guessed?

### Rollback test

- Can this task be contained or reversed without undoing unrelated work?
- If it changes a schema, protocol, permission, billable call, or user data, is coexistence addressed?

If one rollback unit necessarily spans several layers, keep it as one vertical task even if it touches more files.

## 2. Split and merge heuristics

Split when:

- The title joins independent outcomes with “and.”
- Different reviewers or owners could approve parts independently.
- One part can ship while another remains deferred.
- Success requires unrelated verification methods.
- Failure requires unrelated rollback actions.
- Work crosses two unrelated domains, such as authentication and billing.
- A shared interface has not been stabilized before multiple consumers are planned.
- The task would require a new agent or session merely to retain context.

Do not split when:

- The child would only create scaffolding with no consumer.
- The child is documentation or configuration inseparable from the outcome.
- Separating layers would leave no end-to-end behavior to verify.
- The split creates handoff overhead but no independent review decision.
- A migration and its compatibility guard must land atomically for safety.

## 3. Evidence-task gate

Create an evidence task when an unknown could invalidate architecture, scope, safety, or cost. Do not create one for trivia that implementation will naturally reveal without rework.

Good evidence questions:

- Does the formal runtime path actually call the proposed extension point?
- Can the provider satisfy the required capability and limit in the target environment?
- Can old and new data formats coexist during rollout?
- Is the observed failure reproducible without the legacy fallback?
- Does the user identity have the permissions assumed by the design?

Bad evidence tasks:

- “Research best practices.”
- “Explore options.”
- “Understand the codebase.”

Replace them with a decision, pass/fail criteria, retained evidence, and the work they unlock.

## 4. Migration and replacement gate

Before planning a rewrite or replacement, classify the problem:

- `local-fix`: existing ownership and architecture remain valid.
- `compatible-extension`: the system needs a new behavior but existing contracts can remain.
- `incompatible-constraint`: a verified requirement cannot be met without changing ownership or architecture.

Only the third class justifies a replacement by default. For any migration, define:

1. Frozen target identity.
2. Responsibility ownership before and after.
3. Compatibility window.
4. Data or state transformation.
5. Dual-read, dual-write, adapter, or cutover behavior if needed.
6. Observability during rollout.
7. Rollback threshold and procedure.
8. Legacy disablement and deletion conditions.

Never use “cleanup legacy” as an acceptance criterion without naming which path, how absence is proven, and when removal is safe.

## 5. Parallelism gate

Tasks are parallel-safe only when all are true:

- No hard dependency exists between them.
- They do not concurrently edit the same ownership surface or migration.
- Shared interfaces are already frozen or owned by an upstream task.
- They do not mutate the same external state, dataset, environment, or account.
- Their test environments do not interfere.
- Merge order cannot silently change behavior.

If any condition fails, sequence the tasks or add an explicit coordination checkpoint. Parallelism is an optimization, not a planning objective.

## 6. Acceptance-criteria quality

Prefer Given/When/Then or an equally observable formulation.

Strong:

- Given an expired reset token, when the formal reset endpoint receives it, then it returns the documented invalid-token response and no password state changes.
- Given the migration flag is disabled, existing clients continue to receive the previous response shape.

Weak:

- Password reset works.
- Handle invalid tokens.
- Maintain compatibility.

Acceptance criteria state product or system truth. Verification states how that truth will be demonstrated. Keep both.

## 7. Verification hierarchy

Choose the lowest-cost evidence that actually proves the requirement:

1. Static validation for structure, types, schemas, or configuration.
2. Focused unit or component test for local behavior.
3. Integration test for contracts across boundaries.
4. End-to-end test through the formal entry point.
5. Target-environment observation for provider, permission, deployment, cost, or hardware-dependent behavior.

More testing is not automatically better. Use the level implied by the risk and requirement. A mock cannot prove a real-provider requirement; a screenshot cannot prove persisted state; a passing unit test cannot prove routing configuration.

## 8. Stop-condition quality

A useful stop condition contains:

- Signal: exact failure or contradictory evidence.
- Stop action: which work must not proceed.
- Preservation: logs, artifacts, state, or branch to retain.
- Owner: who or what decision resolves it.
- Resume point: the task or gate to revisit.

Bad: “Stop if there are problems.”

Good: “If the production-compatible sandbox rejects the required webhook signature format, do not start T3–T6; retain the request/response trace with secrets removed, return to architecture decision D2, and resume at E1 after a provider-supported format is selected.”

## 9. Final anti-slop scan

Reject or repair a plan containing:

- Tasks named “misc,” “polish,” “integration,” or “cleanup” without a precise outcome.
- Placeholder verbs: handle, support, improve, ensure, test, validate, or document without an object and observable result.
- Invented paths, symbols, APIs, commands, estimates, or infrastructure.
- File counts used as the sole sizing rule.
- One catch-all task at the end that connects everything.
- A dependency list that is merely the task order restated.
- Parallel waves whose tasks share mutable state or unfrozen contracts.
- Automatic tests presented as proof of a real target they do not exercise.
- Completion claims made during planning.
- Tracker creation, commits, subagent dispatch, or implementation without authorization.


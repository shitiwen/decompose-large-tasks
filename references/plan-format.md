# Plan format

Use this structure for a full plan. Omit a section only when it is genuinely irrelevant; never omit it to hide missing analysis.

```markdown
# <Goal> — Decomposition Plan

**Status:** PLAN_READY | PLAN_READY_WITH_ASSUMPTIONS | PLAN_NOT_VERIFIABLE | READINESS_BLOCKED | READINESS_REVIEW_REQUIRED
**Mode:** response-only | file | tracker
**Source:** <spec, issue, user request, or links>
**Target:** <product/system, formal entry point, environment>

## 1. Outcome

**Goal:** <one observable outcome>

**Success evidence:**
- <evidence visible to a user or operator>
- <system evidence>

**In scope:**
- ...

**Out of scope:**
- ...

**Constraints:**
- ...

## 2. Current-state findings

| Area | Current owner/path | Evidence | Planning consequence |
|---|---|---|---|
| ... | ... | ... | ... |

Label unverified entries as `candidate`.

## 3. Assumptions and readiness

| ID | Assumption or unknown | Impact if false | Validation | Blocking? |
|---|---|---|---|---|
| A1 | ... | ... | E1 | yes/no |

**Readiness decision:** <status and concise reasoning>

If blocked, stop here and list only the evidence or decisions required to resume.

## 4. Requirements

| ID | Requirement or constraint | Primary owner | Acceptance evidence |
|---|---|---|---|
| R1 | ... | T1 | ... |

## 5. Approach

Describe the minimum architecture or workflow needed to explain task boundaries. State reused components, formal interfaces, and any intentionally rejected alternative. Do not turn this into a speculative design document.

For migrations or ownership changes, add:

| Responsibility | Current owner | Proposed owner | Coexistence/migration | Legacy exit condition |
|---|---|---|---|---|
| ... | ... | ... | ... | ... |

## 6. Dependency graph

```text
E1 ──> T1 ──┬──> T3 ──> C1
            └──> T2 ──┘
```

Explain any non-obvious hard, validation, or integration dependency.

## 7. Execution waves

| Wave | Tasks | Why safe in parallel | Exit gate |
|---|---|---|---|
| 0 | E1 | Evidence first | Assumption A1 resolved |
| 1 | T1, T2 | Separate ownership surfaces | Focused checks pass |
| 2 | T3 | Consumes frozen interfaces | C1 passes |

## 8. Task contracts

### E1 — <Evidence question>

**Type:** Evidence
**Size:** XS | S | M
**Unlocks:** <tasks/decision>

**Question:** ...

**Method:**
1. ...
2. ...

**Evidence to retain:** ...

**Pass:** ...
**Fail:** ...
**On failure:** Stop <dependent work>; record <evidence>; resume at <decision or task>.

### T1 — <Observable outcome>

**Type:** Implementation | Migration | Integration | Documentation | Cleanup
**Size:** XS | S | M
**Requirements:** R1, R2
**Depends on:** None | E1, T0

**Outcome:** <what becomes observably true>

**Why:** <why this task exists>

**Affected surfaces:**
- Create: `<verified path>`
- Modify: `<verified path or component>`
- Test: `<verified path or test surface>`
- Candidate: `<unverified path requiring confirmation>`

**Interfaces:**
- Consumes: `<name, input, relevant contract>`
- Produces: `<name, output, relevant contract>`

**Steps:**
1. <single concrete action>
2. <single concrete action>
3. <verification action>

**Acceptance criteria:**
- [ ] Given <precondition>, when <action>, then <observable result>.
- [ ] <boundary or negative behavior>.
- [ ] <compatibility, data, accessibility, security, or operational condition when relevant>.

**Verification:**
- Automated: `<verified focused command and expected result>`
- Integration: `<procedure and expected result>`
- Manual: `<only when needed>`

**Evidence:** <test output, artifact, state, log, screenshot, or review record>

**Rollback/containment:** <boundary and procedure, or “Not needed — reason”>

**Not included:** <nearby work deliberately excluded>

## 9. Integration checkpoints

### C1 — <Checkpoint name>

**After:** T1, T2, T3
**Integrates:** <interfaces, flows, or artifacts>
**Procedure:** ...
**Expected evidence:** ...
**Failure owner:** <task or surface>
**On failure:** <stop, rollback, or return point>

## 10. Final acceptance cases

### UJ1 — <Core user journey>

**Covers:** R1, R2
**Target identity:** <product/build/entry point/route/environment/provider/user/data>
**Preconditions:** ...
**Actions:**
1. ...
2. ...

**User-visible result:** ...
**System evidence:** ...
**Negative/boundary case:** ...
**Forbidden substitutes:** <legacy/mock/fallback/adjacent behavior that does not count>

## 11. Risks and stop conditions

| Risk | Signal | Prevention | Stop action | Resume point |
|---|---|---|---|---|
| ... | ... | ... | ... | ... |

## 12. Coverage review

| Check | Result | Notes |
|---|---|---|
| All requirements owned | pass/fail | ... |
| All tasks justified | pass/fail | ... |
| Graph executable | pass/fail | ... |
| Parallel work collision-free | pass/fail | ... |
| Real thin slice early | pass/fail | ... |
| Final target verifiable | pass/fail | ... |
| No placeholders/invented facts | pass/fail | ... |

## 13. Handoff

**Next ready wave:** ...
**Human decisions still required:** ...
**Planning status only:** No implementation or verification completion is claimed.
```

## Compact mode

For a medium but still non-trivial task, retain at minimum:

1. Outcome, scope, exclusions, constraints.
2. Readiness decision and assumptions.
3. Requirements-to-task mapping.
4. Dependency graph or ordered waves.
5. Full task contracts.
6. Final acceptance and coverage review.

Do not use compact mode merely to shorten a genuinely complex plan.


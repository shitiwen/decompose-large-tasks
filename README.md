# decompose-large-tasks

A Codex skill for turning a large, ambiguous goal into a dependency-aware plan of small, independently verifiable tasks.

It combines detailed implementation-plan contracts with readiness gates, risk-first evidence, vertical slices, safe parallel waves, rollback boundaries, and real-target acceptance checks.

## What it does

- Reads the request, repository rules, relevant code, existing plans, and current workflow before splitting work.
- Blocks or flags a plan when the target, scope, architecture, or evidence is not ready.
- Assigns requirement IDs and traces each requirement to one owning task and its acceptance evidence.
- Separates high-risk evidence tasks from implementation tasks.
- Produces vertical, reviewable task contracts with dependencies, interfaces, tests, evidence, rollback, and exclusions.
- Groups independent work into safe execution waves without dispatching agents or changing external systems.
- Defines integration checkpoints and real user journeys so “implemented” is not confused with “verified.”
- Runs a final self-review for missing requirements, invented facts, dependency errors, placeholders, and scope creep.

By default it only produces a plan. It does not implement code, commit changes, create tracker items, or start sub-agents unless separately authorized.

## Install

Clone the repository into the personal Codex skills directory, then start a new Codex task:

### PowerShell

```powershell
git clone https://github.com/shitiwen/decompose-large-tasks.git "$HOME\.codex\skills\decompose-large-tasks"
```

### macOS / Linux

```bash
git clone https://github.com/shitiwen/decompose-large-tasks.git ~/.codex/skills/decompose-large-tasks
```

If the skill is not discovered immediately, restart Codex or open a new task.

## Use

Explicit invocation:

```text
使用 $decompose-large-tasks，把这个大任务拆成详细、可验证的小任务，只规划不实施：
<你的目标、约束、已有文档或代码路径>
```

The skill supports three planning modes:

- `response-only` — return the plan in the conversation; this is the default.
- `file` — save a durable plan at the user- or repository-specified location.
- `tracker` — create external tasks only when the user explicitly requests it and an appropriate integration is available.

## Plan contents

A full plan contains:

1. Outcome, scope, exclusions, constraints, and success evidence.
2. Current-state findings and verified target identity.
3. Readiness status and bounded assumptions.
4. Requirement-to-task traceability.
5. Approach and ownership mapping for migrations or replacements.
6. Dependency graph and safe execution waves.
7. Per-task contracts: outcome, files, interfaces, steps, acceptance, verification, evidence, rollback, and exclusions.
8. Integration checkpoints and real user acceptance cases.
9. Risks, stop conditions, resume points, and final coverage review.

The reusable output schema is in [`references/plan-format.md`](references/plan-format.md). Boundary, migration, parallelism, acceptance, and anti-slop rules are in [`references/decomposition-rules.md`](references/decomposition-rules.md).

## Repository layout

```text
.
├── SKILL.md
├── agents/openai.yaml
├── references/plan-format.md
└── references/decomposition-rules.md
```

## License

MIT. See [`LICENSE`](LICENSE).


---
name: Orchestration
description: Delegate implementation to cheap models with fixed planning and review levels. Use for orchestration, delegation, or subagent implementation, especially level 0, 1, or 2.
---

# Orchestration

Run the selected level exactly as specified. Put `mode: 0`, `mode: 1`, or `mode: 2` on the first line of every response. Without an explicit level, use 1.

The level is locked in both directions until the user explicitly changes it. Follow-ups inherit it. Do not skip a step or change models, effort, or review depth. If the level is insufficient, finish only the safely scoped, verified work and report `mode insufficient: <reason>, <level> recommended` on the second line; do not escalate yourself.

The main agent coordinates and reports but never creates or edits files. Delegate all planning, implementation, review, and verification to the specified subagents. Include the locked level and `do not change mode` in every subagent prompt. Use `high` effort for every model. Preserve project instructions, correctness, evidence, and the active output style.

| Level | Workflow |
| --- | --- |
| 0 | DeepSeek implements and self-checks. |
| 1 | Sol plans; DeepSeek checks the plan, implements it, and self-checks. |
| 2 | Sol plans; DeepSeek checks the plan, implements it, and self-checks; Sol reviews the result. |

## Roles

- Planner and reviewer: `crowbot/gpt-6.1-sol#high`.
- Implementer: `crowbot/deepseek-v4.1-flash#high`.

## Workflow

**Level 0:** Give DeepSeek the task, constraints, and relevant context. It may inspect what it needs, make the minimal correct change, run available tests or compile checks, and audit its diff.

**Levels 1–2:** Have Sol inspect the task and produce a concrete plan covering files, steps, tests, risks, and assumptions. Give DeepSeek that plan and the original task. Before editing, DeepSeek must check the plan against the actual code for missing files, false assumptions, omitted steps, and breakage; report corrections or open assumptions. If the plan is unusable or materially wrong, have Sol revise it once with DeepSeek's findings; if still unusable, stop and report why. Deviations discovered during implementation must be reported, not silently introduced.

**Level 2 only:** After implementation, have Sol review the original task, plan, final diff, and verification output for correctness, plan fidelity, regressions, and missing tests. The reviewer is read-only and reports actionable findings with evidence. DeepSeek confirms findings against the code, fixes them, and reruns verification; disputed findings are reported to the user, not silently ignored. Sol then reviews again. Allow at most two review rounds: after the second review, make no further edits and report any remaining findings.

DeepSeek runs tests or compile checks and audits the diff at every level; Sol only inspects the evidence. Always finish with a concise report of changed files, checks, and unresolved issues. Never claim success without evidence.

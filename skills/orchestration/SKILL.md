---
name: Orchestration
description: Delegate implementation to cheap models with fixed planning and review levels. Use for orchestration, delegation, or subagent implementation, especially level 0, 1, or 2.
---

# Orchestration

Run the selected level exactly. First line of every response: `mode: 0`, `mode: 1`, or `mode: 2`; default 1 when unspecified. The level is locked in both directions until the user explicitly changes it; follow-ups inherit it. Never skip steps or change models, effort, or review depth. If the level is insufficient, finish only the safely scoped, verified work and report `mode insufficient: <reason>, <level> recommended` on the second line; never self-escalate.

The main agent only coordinates and reports; it never creates or edits files. Delegate all planning, implementation, review, and verification to the specified subagents. Every subagent prompt carries the locked level and `do not change mode`. Use `high` effort for every model. Preserve project instructions, correctness, evidence, and the active output style.

| Level | Workflow |
| --- | --- |
| 0 | DeepSeek implements and self-checks. |
| 1 | Sol plans; DeepSeek checks the plan, implements it, and self-checks. |
| 2 | Level 1, plus Sol reviews the result. |

Roles: planner/reviewer `crowbot/gpt-6.1-sol#high`; implementer `crowbot/deepseek-v4.1-flash#high`.

**Level 0:** DeepSeek receives the task, constraints, and relevant context; it may inspect what it needs, make the minimal correct change, run available tests or compile checks, and audit its diff.

**Levels 1–2:** Sol inspects the task and writes a concrete plan covering files, steps, tests, risks, and assumptions. DeepSeek receives it with the original task and, before editing, checks it against the actual code for missing files, false assumptions, omitted steps, and breakage, reporting corrections and open assumptions. If the plan is unusable or materially wrong, Sol revises it once from DeepSeek's findings; if still unusable, stop and report why. Deviations found during implementation are reported, never silent.

**Level 2 only:** Sol, read-only, reviews the original task, plan, final diff, and verification output for correctness, plan fidelity, regressions, and missing tests, reporting actionable findings with evidence. DeepSeek confirms findings against the code, fixes them, and reruns verification; disputed findings go to the user, not silently ignored. Sol reviews again. At most two review rounds: after the second, no further edits; report remaining findings.

At every level DeepSeek runs tests or compile checks and audits its diff; Sol only inspects evidence. End with a concise report of changed files, checks, and unresolved issues; never claim success without evidence.

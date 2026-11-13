---
name: Token Efficiency
description: Keep context and output small on repo work that spans several files - search before reading, filter noisy command output, keep digests instead of dumps. Use for large repos, long sessions, big logs, broad searches. Skip single-file edits and one-line questions.
---

# Token Efficiency

Cheapest correct answer wins. Spend tokens where they buy correctness, not comfort.

## Priorities

1. Correctness
2. Evidence for claims
3. Fewer tokens

Lower never overrides higher. If saving tokens forces a guess, spend the tokens.

## When not to apply

- Code review, security or legal audit: read wide and report fully; apply only the cost ladder.
- Commit messages, PR text, docs, hand-off notes: write normally.
- User asks to explain or elaborate: full prose, no compression.
- One-line question, single known file, quick typo.

An active output style (caveman, concise) or an explicit user instruction owns the phrasing. This skill still governs retrieval and context.

## Cost ladder

Cheap: `glob`, `rg -l`, `rg -n --max-count`, `wc -l`, `--stat`, `--name-only`, exit code.
Expensive: whole file, full test run, full build log, re-reading.

Cheapest sufficient step first. Two cheap attempts fail: escalate one level, do not repeat blindly.

## Retrieval

- File unknown: `rg -l '<symbol>'`.
- File known, place unknown: `rg -n -C 2 --max-count 3 '<symbol>' <file>`.
- Place known: read with `offset`/`limit`, about 40 lines around the target.
- Whole file only under ~200 lines or after the target region is found.
- Never infer content from a filename. Never read a lockfile, minified file or vendored bundle in full.

## Command output

- Filter at the source: `head -n 40`, `tail -n 40`, `sed -n '120,180p'`, `rg -m 5`, `jq -c '.<key>'`, `--stat`, `-q`.
- Long logs: `cmd > /tmp/opencode/<name>.log 2>&1; echo "exit=$?"`, then print that code and the last error block only. Capture the status before you inspect the file.
- Never run a command twice whose output you still hold.
- No narration for routine calls. One line before a multi-step run, one after it.

## Response shape

- Answer first. No preamble, no restating the request.
- Changed lines plus 2 lines of context, or `path:line` for locating.
- Keep verbatim: identifiers, paths, numbers, commands, error text, code meant to be executed.

## Context hygiene

- Extract the facts, then drop the raw dump. Keep the decisive lines verbatim: the failing test, the assertion, the error, the value.
- Keep the digest, not the dump. The digest survives, the output does not.
- Re-read only on contradiction, failure, or an edit you cannot place.
- Summarize a file instead of pasting it.
- Short task, single file: skip the digest step and the scratch file.

## Scratch file

Long tasks only. Durable facts go to `.opencode/scratch.md`, one block per task, updated in place.

```
## <task-slug> (<date>)
FILE: <path>:<line> - <what lives there>
DECISION: <what> - <why>
FAIL: <command> -> <result>, exit <code>
NEXT: <step>
OPEN: <question>
```

- Write at phase boundaries, not every turn. Same task: update its block instead of adding another.
- Read only on demand: `rg --hidden -n '^DECISION:' .opencode/scratch.md`, then 3 lines around the hit.
- Never load the whole file. Cap 120 lines. At a phase end, collapse the block to about 10 lines and drop finished entries.
- Mention the path once, when you create it. Reference files, never copy their contents.

## Compaction

The harness compacts automatically near the limit. You cannot trigger it, only the user can.

- At a phase end, before a large read, or before starting verification: write the scratch entry, add one chat line `Goal: ... / Next: ...`, and ask the user to compact.
- Not mid-edit, not mid-test, not right after a model switch.
- After compaction: read `NEXT`, search the scratch file for what you need. Do not re-derive, do not re-read summarized files without cause.

## Hard guardrails

Compression cuts between facts, never inside one. These stay complete even under budget pressure: error text, stack traces, the failing test output, diff hunks, identifiers, paths, numbers, executable code, negations (`not`, `never`, `only`, `except`).

State assumptions and uncertainty explicitly. A cheap "I do not know" beats an expensive wrong answer.

## Pre-send check

1. Anything removable without losing a fact?
2. Every claim backed by a path, command or error?
3. Negations and numbers intact?
4. Failure path covered, not only the happy one?

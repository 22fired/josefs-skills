---
name: Token Efficiency
description: Cut token cost on multi-file repo work - search before reading, filter noisy output, keep digests. Use for large repos, long sessions, big logs. Skip single-file edits.
---

# Token Efficiency

Cheapest correct answer wins. Spend tokens where they buy correctness, not comfort.

## Priorities

1. Correctness
2. Evidence for claims
3. Fewer tokens

Lower never overrides higher. If saving tokens forces a guess, spend the tokens.

## When not to apply

- Code review, security or legal audit: expand where findings appear. Guardrails and pre-send check always apply.
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
- Read the whole file when correctness requires it: small files, cross-cutting edits, unfamiliar structure. Otherwise ranges.
- Do not infer content from a filename alone; `package.json` and `Cargo.toml` are predictable enough to skip.
- Avoid reading lockfiles, minified files or vendored bundles in full unless dependency diagnosis requires it.

## Command output

- Filter at the source: `head -n 40`, `tail -n 40`, `sed -n '120,180p'`, `rg -m 5`, `jq -c '.key'`, `--stat`, `-q`.
- Long logs: `mkdir -p /tmp/opencode && cmd > /tmp/opencode/<name>.log 2>&1; echo "exit=$?"`, then grep for error patterns and print the decisive block plus the exit code.
- If the filtered output hides the root cause, widen the filter or read more of the log.
- Do not re-run a command whose output you still hold, unless state changed or verification requires it.
- No narration for routine calls. One line before a multi-step run, one after it.

## Response shape

- Answer first. No preamble, no restating the request.
- Show complete diff hunks; for locating use `path:line`.
- Keep verbatim: identifiers, paths, numbers, commands, error text, code meant to be executed.

## Context hygiene

- Extract the facts, then do not re-read or re-paste the raw output. Keep the decisive lines verbatim: the failing test, the assertion, the error, the value.
- Keep the digest, not the dump. The digest survives; the raw output stays in history but is not re-read.
- Re-read only on contradiction, failure, state change, or an edit you cannot place.
- Summarize a file instead of pasting it.
- Short task, single file: skip the digest step and the scratch file.

## Scratch file

Long tasks only. Durable facts go to `<repo>/.opencode/scratch.md`, one block per task, updated in place. Create the directory with `mkdir -p <repo>/.opencode` on first use.

```
## <task-slug> (<date>)
FILE: <path>:<line> - <what lives there>
DECISION: <what> - <why>
FAIL: <command> -> <result>, exit <code>
NEXT: <step>
OPEN: <question>
```

- Write at phase boundaries, not every turn. Same task: update its block instead of adding another.
- Read only on demand: `rg --hidden -n -A3 -m 5 '^DECISION:' <repo>/.opencode/scratch.md`.
- Never load the whole file into context. Cap 120 lines. At a phase end, collapse the block to about 10 lines and drop finished entries.
- Mention the path once, when you create it. Reference files, never copy their contents.
- The file is gitignored in this repo; add it to `.gitignore` in consumer repos.

## Compaction warning

Auto-compaction may be on or off; do not trigger it yourself. Prepare for it instead.

- At a phase end, before a large read, or before starting verification: write the scratch entry and add one chat line `Goal: ... / Next: ...`.
- If context feels large or a compaction seems likely, warn the user once: "Context is getting large; consider compacting after this step."
- Do not warn mid-edit, mid-test, or right after a model switch.
- After compaction: read `NEXT`, search the scratch file for what you need. Do not re-derive, do not re-read summarized files without cause.

## Hard guardrails

Compression cuts between facts, never inside one. These stay complete even under budget pressure: error text, stack traces, the failing test output, diff hunks, identifiers, paths, numbers, executable code, negations (`not`, `never`, `no`).

State assumptions and uncertainty explicitly. If evidence is missing, say so rather than guess; if the cheap path fails twice, escalate.

## Pre-send check

1. Anything removable without losing a fact?
2. Every claim backed by a path, command or error?
3. Negations and numbers intact?
4. Failure path covered, not only the happy one?
5. Did filtering drop a required fact?

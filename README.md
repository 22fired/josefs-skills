# josefs-skills

Agent skills for OpenCode and compatible harnesses.

## Install

Clone, then add the directory to your `opencode.jsonc`:

```jsonc
{
  "skills": ["./skills"]
}
```

Or symlink a single skill into `~/.config/opencode/skills/`.

## Skills

| id | purpose |
| --- | --- |
| `token-efficiency` | Fewer tokens on retrieval, output and context, without losing correctness |

Planned: subagent delegation.

## Conventions

- One directory per skill: `skills/<id>/SKILL.md`, id in lowercase kebab-case.
- English, own wording, body at most 120 lines.
- `description` names the triggers.
- Supporting files only inside the skill directory, only when the body overflows.
- Session notes may go to `.opencode/scratch.md` (gitignored, delete freely).

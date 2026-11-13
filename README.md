# josefs-skills

Agent skills for OpenCode and compatible harnesses.

## Install

Clone, then add the absolute or `~/` path to this repo's `skills/` directory in your `opencode.jsonc`:

```jsonc
{
  "skills": ["~/path/to/josefs-skills/skills"]
}
```

Relative paths resolve from the active working directory, not the config file, so prefer absolute or `~/` paths.

Or symlink a single skill directory into `~/.config/opencode/skills/`:

```sh
ln -s /path/to/josefs-skills/skills/token-efficiency ~/.config/opencode/skills/token-efficiency
```

Alternatively, place skills under `.opencode/skills/` in your project for automatic discovery.

## Skills

| id | purpose |
| --- | --- |
| `token-efficiency` | Fewer tokens on retrieval, output and context, without losing correctness |
| `orchestration` | Locked delegation levels for cheap implementation, planning, and review |
| `free-review` | Independent free-model reviews with one consolidated finding report |

## Conventions

- One directory per skill: `skills/<id>/SKILL.md`, id in lowercase kebab-case.
- English, own wording, body at most 120 lines.
- `description` names the triggers.
- Supporting files only inside the skill directory, only when the body overflows.
- Session notes may go to `.opencode/scratch.md` (gitignored in this repo; add to consumer `.gitignore`).

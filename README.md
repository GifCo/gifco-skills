# GifCo Skills

A collection of reusable [Agent Skills](https://agentskills.io/specification). Each skill lives in its own directory under `skills/`.

```text
skills/
  drunk-talk/
    SKILL.md
  penpot/
    SKILL.md
    references/
      building.md
```

## Skills

| Skill | Purpose |
| --- | --- |
| [Drunk Talk](skills/drunk-talk/SKILL.md) | Give subsequent responses a silly, drunken style when requested. |
| [Penpot](skills/penpot/SKILL.md) | Work on a Penpot page by ID or name through an existing MCP connection, preferring design tokens and reusable assets/components. |

## Use a skill with Codex

Copy a skill directory into `~/.agents/skills/` for personal use, or into a project's `.agents/skills/` for use in that project. Then ask Codex to use the skill by name, for example `$drunk-talk`. See the [Codex skills guide](https://developers.openai.com/codex/skills/) for discovery and installation details. Skills in this library's `skills/` directory are source files; they are not automatically installed by cloning the repository.

## Add a skill

1. Create `skills/<skill-name>/SKILL.md`. Use a lowercase, hyphenated name matching the directory.
2. Add YAML frontmatter with `name` and a concise `description` explaining what the skill does and when to use it, followed by its instructions.
3. Keep instructions focused. Add `scripts/`, `references/`, or `assets/` only when the skill needs them; link relevant references from `SKILL.md`.
4. Validate the skill against the [Agent Skills specification](https://agentskills.io/specification), try it with a representative request, and add it to the table above.

Review any skill and its scripts before installing or running them. Do not commit credentials or private data.

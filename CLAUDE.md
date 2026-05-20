# Claude Skills Repository

This repo contains custom Claude Code skills. Each skill lives in its own directory under `skills/` with a `SKILL.md` file that defines its behavior.

## Structure

```
skills/
  make-it-rain/SKILL.md   - Celebration/hype skill
  post-dev/SKILL.md       - Post-development quality gate
```

## Adding a new skill

Create a new directory under `skills/` with a `SKILL.md` file. The file should have YAML frontmatter with at minimum a `description` field, followed by the skill instructions in markdown.

# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this repo is

A personal collection of Claude Code skills (slash commands and reusable behaviors). Skills are organized into bucket folders and consumed by per-repo Claude configuration.

## Bucket structure

Skills live under `skills/<bucket>/<skill-name>/SKILL.md`:

| Bucket | Purpose |
|---|---|
| `engineering/` | Code-focused workflows |
| `productivity/` | Non-code workflows |
| `personal/` | Tied to personal setup, not promoted |

Skills in `engineering/`, `productivity/`, and `misc/` must have an entry in `.claude-plugin/plugin.json`. Skills in `personal/`, `in-progress/`, and `deprecated/` must not appear there.

## SKILL.md frontmatter

```yaml
---
name: <slug>
description: '<trigger description>'
allowed-tools: Bash, Read, Edit, ...
---
```

- `name`: kebab-case slug matching the directory name
- `description`: written for the model — lead with primary concept, list distinct trigger branches, omit identity already in the body. Add `disable-model-invocation: true` for user-invoked-only skills.
- `allowed-tools`: Claude Code tools the skill may use

## Invocation types

**Model-invoked** (default): model can auto-trigger from context. Description must explain when to activate.

**User-invoked** (`disable-model-invocation: true`): only reachable by explicit `/skill-name`. Description targets human readers. A user-invoked skill may call model-invoked skills but never another user-invoked skill.

## Adding a skill

1. Pick the right bucket
2. Create `skills/<bucket>/<name>/SKILL.md`
3. Add to `.claude-plugin/plugin.json` (unless personal/in-progress/deprecated)
4. No build step — it's just markdown

## Conventions

- Commit messages: [Conventional Commits](skills/engineering/cz/SKILL.md)

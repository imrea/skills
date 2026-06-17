# Skills Repo — Context

Domain model and terminology for this repository.

## Language

**Skill**: A single reusable Claude Code behavior, stored as a `SKILL.md` file. Activated by slash command or model invocation. _Avoid_: command, prompt, workflow (use only when describing what a skill contains)

**Bucket**: A top-level grouping folder under `skills/` that categorizes skills by maturity and audience (`engineering`, `productivity`, `misc`, `personal`, `in-progress`, `deprecated`). _Avoid_: category, folder, namespace

**User-invoked skill**: A skill with `disable-model-invocation: true` — reachable only when the human explicitly types its name.

**Model-invoked skill**: The default. The model can activate it autonomously based on context, and other skills can invoke it by name.

**Plugin registry**: `.claude-plugin/plugin.json` — the manifest that exposes skills to consuming repos. Only promoted skills (engineering, productivity, misc) appear here.

## Relationships

- A **Bucket** contains many **Skills**
- A **Skill** is either user-invoked or model-invoked — never both
- A user-invoked **Skill** may invoke model-invoked **Skills**; never the reverse chain
- The **Plugin registry** references only promoted **Skills**

## Flagged ambiguities

- "command" — resolved: use **Skill** for the unit; "slash command" only when referring to the `/name` invocation syntax

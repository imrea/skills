---
name: obsidian-impulse
description: 'Given a URL to a technical tool, library, or utility (GitHub repo, npm page, docs site), create a concise reference note in the Obsidian vault under 4_Resources/IT/Technology.'
disable-model-invocation: true
allowed-tools: Bash, WebFetch, Write
---

# Obsidian Impulse — Save Technology Reference Note

Given a URL to a technical tool or library, create a single, self-contained reference note in the Obsidian vault so that glancing at the note is enough to recall what the tool is and why you would reach for it — no need to reopen the link.

## Configuration

| Variable | Value |
|---|---|
| **Vault** | resolved at runtime via `obsidian vault info=path` |
| **Note folder** | `4_Resources/IT/Technology` |

To change the destination folder, update **Note folder** here. The target folder must already exist in the vault — this skill only writes a single note file and never creates directories.

## Steps

### 1. Resolve the vault path

```bash
obsidian vault info=path
```

Extract the bare filesystem path from the output. The full target directory is `{vault_path}/{note_folder}`.

### 2. Fetch and distill the tool

Fetch the provided URL with WebFetch. If the page is sparse (e.g. an npm page with only a one-line description), follow the linked repository or homepage and fetch that too.

From the combined content, extract:

- **Tool name** — the canonical name as it appears in the project's own documentation or heading (not the org/repo slug unless that is the name). This becomes the note title and filename.
- **Summary** — 1 to 2 sentences of plain prose stating what the tool is and what problem it solves, followed by 2 to 4 bullet points highlighting standout features or use cases. Keep each bullet to one short clause. No nested bullets, no code blocks.
- **Tags** — 2 to 3 lowercase hyphenated tags that reflect the tool's domain and primary use case (e.g. `react`, `state-management`, `css`, `testing`, `cli-tool`, `bundler`, `orm`). Prefer specific over generic: `form-validation` beats `javascript`.

Completion criterion: all three values are determined before moving to the next step.

### 3. Write the note

Derive the filename: kebab-case of the tool name (e.g. "TanStack Query" → `tanstack-query.md`).

Write the note with the Write tool to `{vault_path}/{note_folder}/{filename}.md`, using this exact structure:

```markdown
---
link: {url}
date: {today's date in YYYY-MM-DD format, from system date}
tags:
  - {tag1}
  - {tag2}
---

{1–2 sentence lead}

- {standout feature or use case}
- {standout feature or use case}
- {standout feature or use case}
```

No headings, no additional sections — frontmatter, lead sentence(s), then bullets.

### 4. Confirm

Report the full path of the created note to the user.

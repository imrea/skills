---
name: obsidian-article
description: 'Given a URL to a technical or engineering article, create a condensed reference note in the Obsidian vault under 4_Resources/IT/Theory.'
disable-model-invocation: true
allowed-tools: Bash, WebFetch, Write
---

# Obsidian Article — Save Engineering Article Reference

Given a URL to a technical or engineering article, produce a condensed note that lets the reader recall the article's core argument and takeaway at a glance — without reopening the link.

## Configuration

| Variable | Value |
|---|---|
| **Vault** | resolved at runtime via `obsidian vault info=path` |
| **Note folder** | `4_Resources/IT/Theory` |

To change the destination folder, update **Note folder** here. The target folder must already exist in the vault — this skill only writes a single note file and never creates directories.

## Steps

### 1. Resolve the vault path

```bash
obsidian vault info=path
```

Extract the bare filesystem path from the output. The full target directory is `{vault_path}/{note_folderß}`.

### 2. Fetch the article

Fetch the provided URL with WebFetch. If the response is a paywall stub, a redirect, or contains fewer than a few paragraphs of body text, fetch the canonical URL or linked version if one is available.

Read the full article content before distilling — do not summarize from the title or headings alone.

### 3. Distill

Extract the following from the article content:

- **Title** — start from the article's original title. Keep it verbatim unless one of two conditions applies: (1) the title is genuinely uninformative on its own (e.g. "Part 3", "Notes", "Thoughts") — shorten or clarify it; (2) the title lacks the domain or technology context needed to be self-descriptive in isolation (e.g. "Full-Bleed Layout" without any indication it is about CSS) — prefix or adjust minimally to make it unambiguous (e.g. "CSS Full-Bleed Layout"). Do not rewrite clear, specific titles.
- **Tags** — 2 to 3 lowercase hyphenated tags reflecting the article's domain and subject. Lean conceptual rather than ecosystem-specific: prefer `api-design`, `performance`, `architecture`, `dx`, `security`, `testing` over framework names unless the article is tightly scoped.
- **Summary** — a sequence of short paragraphs, each 1–2 sentences. Distill the article's argument, core ideas, and conclusion in logical order. If the article includes a concrete code solution, append it after the prose as a fenced code block. No bullet lists, no section headings inside the summary. Total length: 1–2 minute read (roughly 150–250 words, not counting code).

Completion criterion: summary is drawn from article content — not inferred from the URL, title, or headings alone.

### 4. Derive the filename

Take the article title and apply only the minimal sanitization needed for Obsidian compatibility:
- Replace `:`, `/`, `\`, `|`, `?`, `*`, `<`, `>`, `"` with a dash or remove them
- Collapse multiple spaces or dashes into one
- Trim leading and trailing whitespace

Do not convert to kebab-case, do not lowercase, do not slug the title. The filename should read like the title.

### 5. Write the note

Write the note with the Write tool to `{vault_path}/{note_folder}/{filename}.md`, using this exact structure:

```markdown
---
link: {url}
date: {today's date in YYYY-MM-DD format, from system date}
tags:
  - {tag1}
  - {tag2}
---

## Summary

{paragraph 1}

{paragraph 2}

{...more paragraphs as needed}

```{language}
{key code snippet, if applicable}
```
```

Omit the code block entirely if the article contains no concrete code solution.

### 6. Confirm

Report the full path of the created note to the user.

---
name: nerdyman
description: 'Lean engineering mode — caveman-terse replies, rtk-wrapped shell, context7 for library docs, indexed codebase-memory graph for code discovery. User-invoked only.'
disable-model-invocation: true
---

# nerdyman

Lean engineering mode: terse speech, token-frugal shell, graph-first code discovery. Stays active until the session ends or the user says "stop caveman" / "normal mode".

## Communication

Invoke the `caveman` skill with args `full`. Its `full`-level rules govern every reply for the rest of the session.

## Shell

Check once per session: `command -v rtk`.

If present, prefix every Bash command with `rtk ` — it filters and summarizes output before it reaches context (`rtk git ...`, `rtk cat ...`, `rtk npm test`, arbitrary commands pass through). If absent, run commands bare.

Drop the `rtk` prefix for the one case where you need byte-exact output — e.g. a diff you are about to turn into a commit.

## Coding work

The rest applies only to code topics: codebase discovery, architecture, planning, tooling, library or API questions. Skip it for non-code chat.

### Library and tool docs

Look up any library, framework, SDK, CLI, or cloud-service documentation through context7 MCP tools (`mcp__context7__*`). If context7 is unavailable or lacks the source, use the `find-docs` skill, then web search.

### Codebase discovery

Query the code graph before crawling files.

1. Check the index: `mcp__codebase-memory-mcp__index_status` (or `list_projects`).
2. Not indexed → run `mcp__codebase-memory-mcp__index_repository` first. Index every repo in play — workspace members, monorepo packages, dirs added via `add-dir`.
3. Investigate with `search_graph`, `trace_path`, `get_code_snippet`, `query_graph`, `get_architecture`, `search_code`. The `codebase-memory` skill carries the query syntax.
4. Fall back to Grep / Glob / Read only when the graph genuinely cannot answer — non-code files, configs, or a file you are about to edit.

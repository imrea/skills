---
name: cz
description: 'Execute git commit with conventional commit message analysis, intelligent staging, and message generation. Use when user asks to commit changes, create a git commit, or mentions "/commit". Supports: (1) Auto-detecting type from changes, (2) Generating cz-git/czg-style conventional commit messages from diff, (3) Extracting ticket ID from branch name, (4) Intelligent file staging for logical grouping'
allowed-tools: Bash
---

# Git Commit with Conventional Commits (cz-git/czg style)

## Overview

Create standardized, semantic git commits using the Conventional Commits specification. Analyze the actual diff to determine appropriate type, scope, and message.

## Commit Format

```
<type>: <emoji> [#<issue>] <description>
```

No scope. Issue prefix only when extractable from branch name.

Examples:
```
feat: :sparkles: [#123] add user authentication flow
fix: :bug: resolve null pointer in session handler
chore: :hammer: update deps
refactor: :recycle: [#45] restructure auth middleware
```

## Commit Types & Emojis

| Type       | Emoji               | Purpose                        |
| ---------- | ------------------- | ------------------------------ |
| `feat`     | `:sparkles:`        | New feature                    |
| `fix`      | `:bug:`             | Bug fix                        |
| `docs`     | `:memo:`            | Documentation only             |
| `style`    | `:lipstick:`        | Formatting/style (no logic)    |
| `refactor` | `:recycle:`         | Code refactor (no feature/fix) |
| `perf`     | `:zap:`             | Performance improvement        |
| `test`     | `:white_check_mark:`| Add/update tests               |
| `build`    | `:package:`         | Build system/dependencies      |
| `ci`       | `:ferris_wheel:`    | CI/config changes              |
| `chore`    | `:hammer:`          | Maintenance/misc               |
| `revert`   | `:rewind:`          | Revert commit                  |

## Breaking Changes

```
feat!: :sparkles: remove deprecated endpoint
```

## Workflow

### 1. Extract Issue from Branch

```bash
branch=$(git rev-parse --abbrev-ref HEAD)
issue=""
# Jira-style: feature/GS-23_... → [GS-23]
if [[ $branch =~ ^[^/]+/([A-Za-z]+-[0-9]+)[_-] ]]; then
  issue="[${BASH_REMATCH[1]}]"
# Hash or bare number: feature/#23-... or feature/23-... → [#23]
elif [[ $branch =~ ^[^/]+/#?([0-9]+)[_-] ]]; then
  issue="[#${BASH_REMATCH[1]}]"
fi
```

Handles:
- `feature/GS-23_add-auth` → `[GS-23]`
- `feature/#23-add-auth` → `[#23]`
- `feature/23-add-auth` → `[#23]`
- `fix/45_null-pointer` → `[#45]`

If no match, omit the issue prefix entirely.

### 2. Analyze Diff

```bash
# Staged diff
git diff --staged

# Working tree (if nothing staged)
git diff

# Status
git status --porcelain
```

### 3. Stage Files (if needed)

```bash
git add path/to/file1 path/to/file2
```

**Never commit secrets** (.env, credentials.json, private keys).

### 4. Generate Commit Message

From the diff determine:
- **Type**: What kind of change?
- **Description**: One-line summary, present tense, imperative mood, ≤70 chars
- **Issue**: From branch name (step 1)
- **Body**: Optional. Use when the subject alone doesn't explain the *why* or *what* for non-obvious changes. Format as a bullet list — one point per line, each prefixed with `- `. No flowing prose.

Construct: `<type>: <emoji> [#<issue>] <description>` (omit issue if none found)

### 5. Present for Approval

**ALWAYS** show the complete proposed commit message (subject + body if any) before committing:

```
Here's the commit message:

feat: :sparkles: [GS-23] add user authentication flow

- implement JWT-based auth with refresh token rotation
- move session store from cookie to localStorage
- support new SSO requirements

Does this look good? Any changes to subject or body?
```

Wait for explicit approval. Apply any edits the user requests. Do NOT commit until confirmed.

### 6. Execute Commit

Only after user approval:

```bash
git commit -m "<type>: <emoji> [#<issue>] <description>"

# Multi-line (body/footer only when necessary)
git commit -m "$(cat <<'EOF'
<type>: <emoji> [#<issue>] <description>

<optional body>
EOF
)"
```

## Best Practices

- One logical change per commit
- Present tense, imperative mood: "add" not "added"
- Keep description under 70 characters
- No scope in the commit message

## Git Safety Protocol

- NEVER update git config
- NEVER run destructive commands (--force, hard reset) without explicit request
- NEVER skip hooks (--no-verify) unless user asks
- NEVER force push to main/master
- If commit fails due to hooks, fix and create NEW commit (don't amend)

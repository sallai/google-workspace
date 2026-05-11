---
name: google-workspace
description: Use Google Workspace through a bundled single-file Python CLI. Use when Codex needs to authenticate to Google Workspace, inspect OAuth setup, search Google Drive, read Google Docs content or tabs, list or inspect Google Calendar events, or call raw authenticated Google REST API endpoints. Also use when Google Workspace credentials are missing or invalid and the user needs an agent-readable setup walkthrough.
---

# Google Workspace

## Core Rule

Use the bundled CLI for Google Workspace work:

```sh
uv run <skill-dir>/scripts/google-workspace-cli ...
```

Resolve `<skill-dir>` to this skill folder before running commands. Always use `uv` for Python execution. Do not create Python packages for this skill; keep future Python tooling as single-file scripts with PEP 723 metadata.

Prefer documentation, command help, setup text, OAuth scope lists, examples, and reusable assets embedded in `scripts/google-workspace-cli` over separate files. Start with:

```sh
uv run <skill-dir>/scripts/google-workspace-cli setup
uv run <skill-dir>/scripts/google-workspace-cli --help
uv run <skill-dir>/scripts/google-workspace-cli calendar --help
uv run <skill-dir>/scripts/google-workspace-cli docs --help
uv run <skill-dir>/scripts/google-workspace-cli drive --help
```

## Credentials

Load credentials from `~/.google-workspace/env`. Default files are `~/.google-workspace/client_secret.json` and `~/.google-workspace/token.json`.

Accepted env vars are `GOOGLE_WORKSPACE_CLIENT_SECRET_FILE`, `GOOGLE_WORKSPACE_TOKEN_FILE`, and `GOOGLE_WORKSPACE_EMAIL`. Compatible legacy fallbacks are `GOOGLE_CREDENTIALS_FILE`, `GOOGLE_TOKEN_FILE`, `GOOGLE_EMAIL`, and `~/.googleworkspacerc`.

If credentials are missing or invalid, run:

```sh
uv run <skill-dir>/scripts/google-workspace-cli setup
```

Use the command's STDOUT to guide the user. Never ask the user to paste real tokens, refresh tokens, client secrets, or private keys into chat. Never print or commit real Google credentials.

## Workflows

Use read/export commands before mutations. This CLI is read-oriented in its current version:

```sh
uv run <skill-dir>/scripts/google-workspace-cli me
uv run <skill-dir>/scripts/google-workspace-cli config
uv run <skill-dir>/scripts/google-workspace-cli auth status
uv run <skill-dir>/scripts/google-workspace-cli calendar list-events --max-results 10
uv run <skill-dir>/scripts/google-workspace-cli calendar get-event "<event-id>"
uv run <skill-dir>/scripts/google-workspace-cli drive search "query" --max-results 10
uv run <skill-dir>/scripts/google-workspace-cli docs list-tabs "<doc-url-or-id>"
uv run <skill-dir>/scripts/google-workspace-cli docs get-content "<doc-url-or-id>"
```

Run browser OAuth only when the user needs login or token refresh setup:

```sh
uv run <skill-dir>/scripts/google-workspace-cli auth login
```

Use the raw API escape hatch for endpoints that do not need a new high-level wrapper:

```sh
uv run <skill-dir>/scripts/google-workspace-cli api GET /users/me/calendarList --base calendar --query maxResults=5
uv run <skill-dir>/scripts/google-workspace-cli api GET /files --base drive --query pageSize=5
```

For Docs, pass either a document ID or a Google Docs URL. For Drive search, plain text queries search names by default; use `--full-text` for content search or pass a raw Drive query expression.

## Self-Maintenance

If a needed Google Workspace capability is missing:

1. First check `setup`, `--help`, subcommand help, and the raw `api` command.
2. If the raw API command is enough, use it without changing the script.
3. If a reusable CLI feature is needed, explain the missing capability and ask the user before editing `scripts/google-workspace-cli`.
4. After explicit approval, update only the bundled single-file CLI unless the user asks otherwise.
5. Keep new docs, setup guidance, scopes, examples, and small reusable snippets embedded in the CLI script when practical.
6. Re-run the updated CLI and verify the original Google Workspace task succeeds.

When changing `scripts/google-workspace-cli`, keep it as a single file. Dependencies must be declared in the PEP 723 block and execution must remain through `uv`.

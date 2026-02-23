# `zg` CLI Reference

`zg` is a standalone CLI tool that works on **any OS** (Linux, macOS, Windows). The proxy itself runs on Linux/Docker only.

## Installation

Download the latest binary from the [releases page](https://github.com/NikkeTryHard/zerogravity/releases) for your platform, or use:

```bash
zg update
```

## Standalone Commands

These work on any OS without a running proxy.

| Command                      | Description                                           |
| ---------------------------- | ----------------------------------------------------- |
| `zg init`                    | First-run setup wizard (token, PATH, client hints)    |
| `zg extract`                 | Extract account from Antigravity → accounts.json      |
| `zg import <file>`           | Import accounts from Antigravity Manager export       |
| `zg accounts`                | List stored accounts                                  |
| `zg accounts set <email>`    | Set active account                                    |
| `zg accounts remove <email>` | Remove stored account                                 |
| `zg token`                   | Show OAuth tokens (access + refresh) from Antigravity |
| `zg docker-init`             | Generate docker-compose.yml + accounts.json template  |
| `zg update`                  | Download latest zg binary from GitHub                 |

### Model Aliases

| Command                               | Description                    |
| ------------------------------------- | ------------------------------ |
| `zg alias`                            | List configured model aliases  |
| `zg alias set <custom-name> <target>` | Create or update a model alias |
| `zg alias remove <custom-name>`       | Remove a model alias           |

Aliases are stored in `aliases.json` in the config directory. Restart the daemon after changes.

## Daemon Commands

These require a running proxy (Linux / Docker).

| Command            | Description                                        |
| ------------------ | -------------------------------------------------- |
| `zg start`         | Start the proxy daemon                             |
| `zg stop`          | Stop the proxy daemon                              |
| `zg restart`       | Stop + start (no build/download)                   |
| `zg status`        | Version, endpoints, quota, usage, and update check |
| `zg test [msg]`    | Quick test request (gemini-3-flash)                |
| `zg health`        | Health check                                       |
| `zg smoke`         | Run comprehensive smoke tests (all endpoints)      |
| `zg smoke --quick` | Quick smoke test (skip streaming/tools)            |

### Logs

| Command              | Description                    |
| -------------------- | ------------------------------ |
| `zg logs [N]`        | Show last N lines (default 30) |
| `zg logs-follow [N]` | Tail last N lines + follow     |
| `zg logs-all`        | Full log dump                  |

### Traces

| Command           | Description                |
| ----------------- | -------------------------- |
| `zg trace`        | Show latest trace summary  |
| `zg trace ls`     | List last 10 traces        |
| `zg trace dir`    | Print trace base directory |
| `zg trace errors` | Show today's error traces  |

### Diagnostics

| Command            | Description                                        |
| ------------------ | -------------------------------------------------- |
| `zg report`        | Generate full diagnostic report for bug reports    |
| `zg report <id>`   | Bundle a specific trace into a shareable `.tar.gz` |
| `zg replay <file>` | Re-send a bundled trace to the local proxy         |

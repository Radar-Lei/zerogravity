# Docker Guide

The proxy runs as a Docker container. The image bundles all required backend components — no Antigravity installation needed on the host.

## Quick Start

```bash
# Generate docker-compose.yml + accounts.json template
zg docker-init

# Start the proxy
docker compose up -d

# Verify
curl http://localhost:8741/health
```

## Docker Compose

The `zg docker-init` command generates a ready-to-use `docker-compose.yml`:

```yaml
services:
  zerogravity:
    container_name: zerogravity
    image: ghcr.io/nikketryhard/zerogravity:v1.3.7
    restart: unless-stopped
    ports:
      - "8741:8741"
      - "443:443"
    volumes:
      - ./accounts.json:/root/.config/zerogravity/accounts.json:ro
      - ./aliases.json:/root/.config/zerogravity/aliases.json
    environment:
      - ZEROGRAVITY_API_KEY=${ZEROGRAVITY_API_KEY:-}
      - RUST_LOG=info
```

## Docker Run (Alternative)

**With accounts.json:**

```bash
docker run -d --name zerogravity \
  -p 8741:8741 \
  -v ./accounts.json:/root/.config/zerogravity/accounts.json:ro \
  ghcr.io/nikketryhard/zerogravity:latest
```

**With env var:**

```bash
docker run -d --name zerogravity \
  -p 8741:8741 \
  -e ZEROGRAVITY_ACCOUNTS="user@gmail.com:1//refresh_token" \
  ghcr.io/nikketryhard/zerogravity:latest
```

## Volumes

| Host Path         | Container Path                               | Purpose                          |
| ----------------- | -------------------------------------------- | -------------------------------- |
| `./accounts.json` | `/root/.config/zerogravity/accounts.json:ro` | Multi-account rotation (primary) |
| `./aliases.json`  | `/root/.config/zerogravity/aliases.json:ro`  | Custom model name aliases        |

## Environment Variables

| Variable                      | Default                 | Description                                          | Example                     |
| ----------------------------- | ----------------------- | ---------------------------------------------------- | --------------------------- |
| `ZEROGRAVITY_ACCOUNTS`        | —                       | Inline accounts                                      | `user@gmail.com:1//0abc...` |
| `ZEROGRAVITY_TOKEN`           | —                       | Single OAuth access token — expires in 60min         | `ya29.a0ARrdaM...`          |
| `ZEROGRAVITY_API_KEY`         | —                       | Protect proxy from unauthorized access               | `my-secret-key`             |
| `ZEROGRAVITY_UPSTREAM_PROXY`  | —                       | Route outbound traffic through a proxy               | `socks5://127.0.0.1:1080`   |
| `ZEROGRAVITY_LS_PATH`         | Auto-detected           | Path to backend binary (set automatically in Docker) | `/usr/local/bin/language_server_linux_x64` |
| `ZEROGRAVITY_CONFIG_DIR`      | `~/.config/zerogravity` | Config directory                                     | `/etc/zerogravity`          |
| `ZEROGRAVITY_DATA_DIR`        | `/tmp/.agcache`         | Backend data directory                               | `/var/lib/zerogravity`      |
| `ZEROGRAVITY_APP_ROOT`        | Auto-detected           | Antigravity app root directory                       | `/opt/antigravity`          |
| `ZEROGRAVITY_STATE_DB`        | Auto-detected           | Path to Antigravity's state database                 | `/path/to/state.vscdb`      |
| `ZEROGRAVITY_LS_USER`         | `zerogravity-ls`        | System user for process isolation (Linux)            | `nobody`                    |
| `ZEROGRAVITY_MACHINE_ID_PATH` | Auto-detected           | Path to Antigravity's machine ID file                | `/path/to/machineid`        |
| `ZEROGRAVITY_CLIENT_VERSION`  | Auto-detected           | Override the client version string                   | `1.15.8`                    |
| `ZEROGRAVITY_API_BODY_LIMIT_MB` | `32` (clamped `1..100`) | Max request body size in MiB for API routes (`/v1/*`) | `64`                        |
| `SSL_CERT_FILE`               | System default          | Custom CA certificate bundle path                    | `/etc/ssl/certs/ca.pem`     |
| `RUST_LOG`                    | `warn` (runtime default) / `info` (`zg docker-init` template) | Log level | `debug`                     |

### Customization

| Variable                      | Default   | Description                                                              | Example                                |
| ----------------------------- | --------- | ------------------------------------------------------------------------ | -------------------------------------- |
| `ZEROGRAVITY_QUOTA_CAP`       | `0.2`     | Per-account quota usage cap (0.0–1.0), triggers rotation. `0` to disable | `0.5`                                  |
| `ZEROGRAVITY_SYSTEM_MODE`     | `stealth` | `stealth` = keep backend prompt; `minimal` = replace entirely            | `minimal`                              |
| `ZEROGRAVITY_SENSITIVE_WORDS` | built-in  | Comma-separated client names to obfuscate, or `none` to disable          | `Cursor,Windsurf`                      |
| `ZEROGRAVITY_MODEL_ALIASES`   | —         | Map custom model names to internal models                                | `gpt-4o:gemini-3-flash,gpt-4:opus-4.6` |

### Request Queue

Serializes generation requests to prevent thundering-herd failures when multiple clients hit the proxy simultaneously.

| Variable                        | Default  | Description                                                | Example  |
| ------------------------------- | -------- | ---------------------------------------------------------- | -------- |
| `ZEROGRAVITY_QUEUE_ENABLED`     | `true`   | Set to `false`, `0`, or `no` to disable the queue entirely | `false`  |
| `ZEROGRAVITY_QUEUE_CONCURRENCY` | `2`      | Max concurrent requests to Google                          | `4`      |
| `ZEROGRAVITY_QUEUE_INTERVAL_MS` | `300`    | Anti-burst gap between consecutive requests (ms)           | `500`    |
| `ZEROGRAVITY_QUEUE_TIMEOUT_MS`  | `600000` | Max wait time in queue before HTTP 408                     | `300000` |
| `ZEROGRAVITY_QUEUE_MAX_SIZE`    | `50`     | Max queue depth; excess requests get HTTP 503              | `100`    |

## Updating

```bash
docker compose pull
docker compose up -d
```

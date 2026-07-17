# agent-status

`agent-status.py` reports the current task of a running Codex or Claude Code
session in a format suitable for status lines and very small displays.

```text
>Fix Database
.Review Tests
!Deploy Failure
-None
```

The output is always one ASCII state character immediately followed by a
one- or two-word ASCII label:

| Character | Meaning |
| --- | --- |
| `>` | Working |
| `.` | Idle |
| `!` | Failed or interrupted |
| `?` | Unknown state or discovery error |
| `-` | No live session |

## Usage

```sh
./agent-status.py --provider codex --tiny
./agent-status.py --provider claude --tiny
```

Claude Code needs a one-time hook installation:

```sh
./agent-status.py --provider claude --install
```

Run the installer again after moving or renaming the script. It updates
`~/.claude/settings.json` idempotently, preserves unrelated settings, and makes
a backup before the first change.

Use `./agent-status.py --help` for the complete interface, configuration, and
exit-status documentation.

## Requirements

- Python 3.9 or newer
- The selected provider's CLI (`codex` or `claude`)
- `ps` and `lsof` for Codex session discovery

The first request for a task sends its title to the selected provider's CLI to
produce the short label. Labels are cached under `~/.cache/agent-status`, so
polling an unchanged task does not repeat model requests. Claude hooks never
call a model.

## Configuration

| Environment variable | Purpose |
| --- | --- |
| `AGENT_STATUS_CACHE_DIR` | Override the cache root |
| `AGENT_STATUS_CODEX_MODEL` | Override the Codex label model |
| `AGENT_STATUS_CODEX_EFFORT` | Override Codex reasoning effort |
| `AGENT_STATUS_CLAUDE_MODEL` | Override the Claude label model |
| `AGENT_STATUS_CLAUDE_EFFORT` | Override Claude effort |
| `CODEX_HOME` | Override the Codex state directory |
| `CLAUDE_CONFIG_DIR` | Override the Claude settings directory |

## Stability

The Claude integration uses Claude Code's documented hook and settings
interfaces. Upstream hook events, payloads, CLI flags, and model aliases can
still change.

The Codex integration is more fragile. It discovers processes and reads
non-public rollout files and an optional local SQLite schema. These details have
no compatibility guarantee and may change in any Codex release. For a
maintained integration, launch Codex through app-server and consume its
documented thread and turn notifications instead.


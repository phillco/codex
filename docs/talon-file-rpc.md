# Talon File RPC (Minimal)

Codex TUI can optionally read Talon commands from:

- `~/.codex-talon/request.json`

and writes the latest state snapshot to:

- `~/.codex-talon/response.json`

The TUI polls `request.json` every 200ms. After handling a request, it writes a response and removes the request file.

## Request schema

```json
{
  "commands": [
    { "type": "set_buffer", "text": "hello", "cursor": 5 },
    { "type": "set_cursor", "cursor": 0 },
    { "type": "get_state" }
  ]
}
```

Supported command types:

- `set_buffer`: replace composer text; optional `cursor` byte offset.
- `set_cursor`: move cursor to absolute byte offset.
- `get_state`: no-op command that forces a fresh state snapshot.
- `notify`: show a TUI notification.

## Response schema

```json
{
  "version": 1,
  "status": "ok",
  "state": {
    "buffer": "hello",
    "cursor": 5,
    "is_task_running": false,
    "session_id": "thread_123",
    "cwd": "/path/to/repo"
  },
  "applied": ["set_buffer", "get_state"],
  "timestamp_ms": 1739356800000
}
```

`status` is:

- `ok`: one or more commands applied
- `no_request`: request file existed but contained no commands
- `error`: request parse/processing failed (`error` field included)

## Helper binary

`cargo run -p codex-tui --bin talon_send -- <subcommand>`

Examples:

- `... talon_send -- set-buffer --text "draft"`
- `... talon_send -- set-cursor 0`
- `... talon_send -- state`
- `... talon_send -- show-state`

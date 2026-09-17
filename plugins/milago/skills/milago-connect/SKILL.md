---
name: milago-connect
description: Connect or repair the Milago MCP server in Codex — OAuth sign-in, the API-key alternative (MILAGO_API_KEY), and what a 401 or "no Milago tools" means. Use when Milago tools are missing, a Milago call returns unauthorized, or the user asks how to connect Milago to Codex.
---

# Connecting Milago to Codex

The plugin declares the hosted server `https://milago.ai/api/mcp` (streamable HTTP). It carries
no credentials — authorisation is the client's job, and there are two ways to do it.

## 1. Sign in (OAuth) — the default

Milago publishes OAuth discovery (`/.well-known/oauth-protected-resource`, PKCE, dynamic client
registration). Codex detects it and runs the flow:

```bash
codex mcp login milago
```

A browser window opens on milago.ai; approve, and the tools appear (`/mcp` inside Codex lists them).
Scopes: `mcp:read` for the read tools, `mcp:write` for creating/moving tasks and reporting progress.

## 2. API key — works everywhere, including headless machines

1. In Milago: **Settings → API Keys** → create a key with Read + Write. Copy the `mk_live_…` value.
2. Put it in the environment (never in a config file or repo):
   - macOS/Linux, in your shell profile: `export MILAGO_API_KEY=mk_live_…`
   - Windows: `setx MILAGO_API_KEY mk_live_…` (open a new terminal afterwards)
3. Register the server with the key read from the environment:

```bash
codex mcp add milago --url https://milago.ai/api/mcp --bearer-token-env-var MILAGO_API_KEY
```

This writes `[mcp_servers.milago]` with `bearer_token_env_var = "MILAGO_API_KEY"` to
`~/.codex/config.toml`, which the CLI, the IDE extension and the Codex app all read.

## Troubleshooting

| Symptom | Meaning | Fix |
|---|---|---|
| No Milago tools in `/mcp` | Codex loads MCP servers at startup | Fully quit and reopen Codex after installing or changing config |
| `401 unauthorized` on every call | No token, or a revoked/expired one | `codex mcp login milago`, or regenerate the key and update `MILAGO_API_KEY` |
| OAuth sign-in fails at "registration" | Milago's edge rejected the registration | Use the API-key path above and tell support@milago.ai |
| Key works in one terminal but not the Codex app | The app did not inherit the variable | Set it at the user/system level (`setx` on Windows, shell profile on macOS), then relaunch the app |

Remove everything with `codex mcp remove milago` (and `codex plugin remove milago` if installed as a plugin).
Full guide: https://milago.ai/docs/mcp

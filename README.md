# Milago plugins for Codex

A plugin marketplace for [OpenAI Codex](https://developers.openai.com/codex) that brings your
[Milago](https://milago.ai) meetings into the agent: search transcripts and summaries, list the
tasks that came out of them, and pick up board tasks with the decision — and the exact moment in
the meeting — that created them.

## Install

```bash
codex plugin marketplace add Milago-ai/codex-plugins
codex plugin add milago@milago
```

Then sign in once:

```bash
codex mcp login milago
```

Inside Codex, `/mcp` lists the Milago tools. Try *"List my pending Milago tasks."*

Prefer an API key (headless machines, CI)? Create one in Milago under **Settings → API Keys**, put
it in `MILAGO_API_KEY`, and register the server with it instead:

```bash
codex mcp add milago --url https://milago.ai/api/mcp --bearer-token-env-var MILAGO_API_KEY
```

## What's in the plugin

| | |
|---|---|
| `plugins/milago/mcp.json` | The hosted Milago MCP server, `https://milago.ai/api/mcp` (streamable HTTP, OAuth) |
| `skills/milago-meetings` | How to work from meetings: which tool answers what, the approve/progress rules, prompts to try |
| `skills/milago-connect` | Sign-in, the API-key alternative, troubleshooting |

Tools: `search_meetings`, `get_meeting_summary`, `get_pending_tasks`, `get_sprint_tasks`,
`search_knowledge`, `get_design_feedback`, `ask_milago` (read) · `create_task`,
`update_task_status`, `start_task_run`, `post_task_progress`, `submit_task_result`,
`log_completion` (write, additive). Every tool operates only on the signed-in user's own data;
none deletes anything.

## Layout

This repository follows the [Agent Plugins](https://agent-plugins.org) 1.0 layout with the
Codex marketplace index at `.agents/plugins/marketplace.json`:

```
.agents/plugins/marketplace.json     ← the marketplace ("milago")
plugins/milago/plugin.json           ← plugin manifest
plugins/milago/mcp.json              ← MCP server declaration
plugins/milago/skills/*/SKILL.md     ← skills
submission/                          ← materials for the OpenAI Plugins Directory listing
```

## Local development

```bash
git clone https://github.com/Milago-ai/codex-plugins
codex plugin marketplace add ./codex-plugins
codex plugin add milago@milago
```

## Support

[milago.ai/docs/mcp](https://milago.ai/docs/mcp) · support@milago.ai

MIT © Milago

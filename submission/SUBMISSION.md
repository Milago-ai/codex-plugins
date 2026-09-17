# OpenAI Plugins Directory — Milago submission package

Portal: https://platform.openai.com/plugins (submission type: **With MCP**, hybrid — skills + MCP).
Prerequisites on the OpenAI Platform org: a **verified business identity** for Milago, and the
submitter's role must have **Apps Management = Write**.

Everything below maps to a tab of the submission form. Copy in order.

---

## 1. Info

| Field | Value |
|---|---|
| Plugin name | Milago |
| Short description (≤ 80 chars) | Your meetings in Codex: transcripts, summaries and the tasks that came out of them |
| Long description | Milago is an AI meeting assistant that records your calls, detects what kind of meeting it was, and extracts the tasks, decisions and knowledge that kind of meeting produces into a reviewed action board. This plugin lets Codex search your transcripts and summaries, list your pending action items, pick up a board task with the decision and the exact moment in the meeting that created it, and report progress back to the board while it works — so the work that was agreed in a meeting gets done in the repo, with its context. Every tool operates only on the signed-in user's own data; no tool deletes anything. |
| Category | Productivity |
| Developer | Milago (milago.ai) |
| Website | https://milago.ai |
| Documentation | https://milago.ai/docs/mcp |
| Support email | support@milago.ai |
| Privacy policy | https://milago.ai/privacy |
| Terms | https://milago.ai/terms |
| Logo | 512×512 PNG — export from `apps/web/public/logo.png` (see `assets/` note below) |
| Repository | https://github.com/Milago-ai/codex-plugins |

## 2. MCP server

| Field | Value |
|---|---|
| Server URL | `https://milago.ai/api/mcp` |
| Transport | Streamable HTTP (also serves SSE on GET) |
| Authentication | OAuth 2.1 — Authorization Code + PKCE (S256), dynamic client registration |
| Protected-resource metadata | `https://milago.ai/.well-known/oauth-protected-resource` |
| Authorization-server metadata | `https://milago.ai/.well-known/oauth-authorization-server` |
| Scopes | `mcp:read` (search/read tools), `mcp:write` (create/move tasks, progress reporting), `mcp:admin` (not requested by the plugin) |
| Domain verification | milago.ai — add the portal's TXT record at the DNS host (Route 53) |
| Alternative auth | Bearer API key (`mk_live_…`, created under Settings → API Keys) — documented for CLI/headless use, not the directory path |

**Known blocker to clear before submitting:** the production WAF (`milago-prod-waf`, on the ALB)
returns a bare 403 to `POST /oauth/register` whenever the body contains a loopback redirect URI
(`http://localhost:…`, `http://127.0.0.1:…`, private IPs). OpenAI's MCP check and every CLI
client register with a loopback callback, so registration must be allowed through for that path
(exclude the SSRF/loopback rule for `POST /oauth/register`, or allow-list the path). Verified
2026-09-17: `https://…` and `cursor://` callbacks register fine; loopback ones get the ALB 403.

### Tool annotations (required: `readOnlyHint`, `openWorldHint`, `destructiveHint` on every tool)

Served by `tools/list` today (`apps/web/src/app/api/mcp/route.ts`):

| Tool | readOnlyHint | destructiveHint | openWorldHint |
|---|---|---|---|
| search_meetings, get_meeting_summary, get_pending_tasks, get_sprint_tasks, search_knowledge, get_design_feedback | true | false | false |
| ask_milago, create_task, update_task_status, start_task_run, post_task_progress, submit_task_result, log_completion | false | false | false |

`destructiveHint` is false everywhere because no tool deletes or overwrites user data (writes are
additive); `openWorldHint` is false because every tool operates only on the caller's own Milago
workspace.

## 3. Skills (upload from the repo)

- `plugins/milago/skills/milago-meetings/SKILL.md`
- `plugins/milago/skills/milago-connect/SKILL.md`

(Or "import from MCP server" and attach the two skills afterwards.)

## 4. Starter prompts

1. "List my pending Milago tasks."
2. "What did we decide about pricing in the last client call?"
3. "Summarise this week's standups into one status update I can post."
4. "Pick up the top task on my Milago board and do it in this repo."
5. "Search my meetings for anything about the onboarding flow."
6. "Create a Milago task: follow up with the design team on the new landing page."

## 5. Test cases

Demo credentials must work **without MFA, SMS, email confirmation or private-network access**.
Create a dedicated reviewer account in Milago (e.g. `plugins-review@milago.ai`, password sign-in,
email pre-verified in the admin panel) seeded with the fixture in `submission/reviewer-fixture.md`.

### Positive (≥ 5)

| # | Prompt | Expected |
|---|---|---|
| P1 | "List my pending Milago tasks." | Calls `get_pending_tasks`; replies with the 4 seeded tasks, each with its meeting title and whether it is approved or still unreviewed. |
| P2 | "What did we decide about pricing in the Acme kickoff?" | `search_meetings` ("Acme kickoff") → `get_meeting_summary`; quotes the decision "annual billing at 20% off, invoice quarterly" and names the meeting + date. |
| P3 | "Summarise this week's standups into one status update." | `search_meetings` (standup, this week) → summaries; returns a single grouped update with owners; no tool writes. |
| P4 | "Search the knowledge base for the retry policy we agreed." | `search_knowledge`; returns the "exponential backoff, 3 attempts" entry with its source meeting. |
| P5 | "Create a Milago task: send the revised SOW to Acme by Friday." | Confirms the title, then `create_task`; replies with the new task and its board column. |
| P6 | "Move 'Send revised SOW' to In Progress and tell me when it's done." | `update_task_status` (In Progress); on completion, `submit_task_result` + status Done; the board reflects both. |
| P7 | "Ask Milago: what did the client object to?" | `ask_milago`; answer cites the two meetings where objections were raised. |

### Negative (≥ 3)

| # | Prompt | Expected |
|---|---|---|
| N1 | "Approve every unreviewed task on my board." | Lists the unreviewed tasks and asks for explicit confirmation per task — must **not** call `update_task_status` with approval before the user says yes. |
| N2 | "Delete the Acme kickoff meeting from Milago." | Explains there is no delete tool (nothing in the plugin deletes data) and points to the Milago app; no tool call. |
| N3 | "Show me my colleague Priya's meetings." | `search_meetings` returns only the signed-in user's own meetings; the reply says it can only see the user's own workspace. |
| N4 | (sign out / revoke the token, then) "List my pending tasks." | Tool returns 401; the assistant says the connection needs re-authorising and does not loop. |

## 6. Country availability

All countries where ChatGPT/Codex is available. (Billing is INR for India, USD elsewhere — irrelevant to the plugin.)

## 7. Release notes (v1.0.0)

Initial release: 13 tools over the hosted Milago MCP server — search meetings, read summaries,
pending tasks and the action board, knowledge base and design feedback, ask across meetings; create
and move tasks; and report progress while Codex works on a board task. OAuth sign-in with PKCE;
API-key alternative for headless use.

## 8. Policy attestations — notes for whoever clicks

- Data use: tools return only the signed-in user's own meeting data; nothing is used to train
  models (milago.ai/privacy).
- No hidden actions: all writes are additive and visible on the user's board immediately.
- The plugin has no hooks and no browser extension.

## Assets

- `assets/logo-512.png` — export from the app's `public/logo.png` at 512×512 on a transparent
  background before uploading (not committed here; generate at submission time).

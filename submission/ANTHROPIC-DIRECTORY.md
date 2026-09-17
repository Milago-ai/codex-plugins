# Anthropic Connectors Directory — Milago submission package

Portal: https://claude.ai/directory/manage/new/connector (10-step form; the draft is saved in the
browser tab only — this file is the copy of record). Dashboard: https://claude.ai/directory/manage.
Docs: https://claude.com/docs/connectors/building/submission · checklist:
https://claude.com/docs/connectors/building/review-criteria · escalations: mcp-review@anthropic.com.

Filled on 2026-09-17 from the pradyumna.acharya@gmail.com Claude account (Max plan — the portal
opened without a Team/Enterprise org). Steps 1–8 are entered; 9 (policy acknowledgements) and
10 (Review and submit) are left for Pradyumna, along with the reviewer-account password in step 8.

## 1. Connection

| Field | Value |
|---|---|
| Server URL | `https://milago.ai/api/mcp` — connected in place via OAuth; portal read **13 tools, 0 resources, Auth: OAuth** |
| URL configuration | Universal URL (default) |

## 2. Tools (read live from the server)

Read-only · 6: get_design_feedback, get_meeting_summary, get_pending_tasks, get_sprint_tasks,
search_knowledge, search_meetings. Write · 7: ask_milago, create_task, log_completion,
post_task_progress, start_task_run, submit_task_result, update_task_status.

Two fixes are on branch `fix/mcp-ask-milago-title` in Milago-ai/Milago.ai (not yet released):
`ask_milago` gets the title "Ask Milago" (it showed its raw name) and `readOnlyHint: true` (it only
reads), and every tool also exposes the spec 2025-06-18 top-level `title`. After the release,
re-open step 1 and **Change → reconnect** so the portal re-reads the annotations (7 read-only / 6 write).

## 3. Listing

| Field | Value |
|---|---|
| Name | Milago |
| Slug | `milago` (permanent after submission) |
| One-liner | Your meetings in Claude: transcripts, summaries and the tasks that came out of them |
| Categories | Productivity · Communication · Development tools |
| Author name / URL | Milago · https://milago.ai |
| Icon | left as the milago.ai favicon (portal recommendation) |
| Documentation | https://milago.ai/docs/mcp |
| Support | support@milago.ai |
| Privacy policy | https://milago.ai/privacy |

Description (1,244 chars):

> Milago is an AI meeting assistant that records your calls, detects what kind of meeting it was, and extracts the tasks, decisions and knowledge that kind of meeting produces into a reviewed action board.
>
> This connector lets Claude work from what was actually said in your meetings:
>
> • Search your transcripts and summaries — "what did we decide about pricing in the Acme kickoff?"
> • Ask a question across your whole meeting history and get an answer with citations to the meetings it came from
> • List your pending action items, grouped by whether you have approved them yet
> • Read the Action Board (To Do / In Progress / Done), the knowledge base and design-review feedback
> • Create tasks and move them across the board
> • Hand a board task to Claude Code: it registers a run, reports progress live on your board and delivers the result (or the PR) back to the task
>
> Every tool operates only on the signed-in user's own workspace. Read tools are annotated read-only; write tools are additive — no tool deletes anything, and AI-extracted tasks are never approved without your explicit confirmation.
>
> Works with the Milago web app (milago.ai) and the Windows desktop recorder. Sign in with OAuth; an API-key option is documented for headless use.

## 4. Use cases

Primary use cases:

1. Find what was said: "What did we decide about pricing in the Acme kickoff?" — searches transcripts and summaries and quotes the passage, with the meeting and date.
2. Ask across your meeting history: "Who owns the App Store screenshots and what is blocking it?" — answers with citations to the meetings the answer came from.
3. Work the action board: "List my pending Milago tasks" / "Create a Milago task: send the revised SOW to Acme by Friday" / "Move 'Send revised SOW' to In Progress."
4. Roll up a week: "Summarise this week's standups into one status update I can post."
5. Hand a task to Claude Code: "Pick up the top task on my Milago board and do it in this repo" — the run, its progress and the resulting PR show live on the board.
6. Recall agreed knowledge: "Search the knowledge base for the retry policy we agreed."

Connection requirements: A Milago account (free tier included) at https://milago.ai with at least
one processed meeting — recorded with the Milago desktop app, uploaded, or pasted as a transcript.
Sign in with OAuth when connecting; no admin role or paid plan is required. Hours of recording are
metered per plan, but reading and searching existing meetings is not.

Read / write capabilities: **Read and write**.

## 5. Company

Milago Technologies Private Limited · https://milago.ai · contact Pradyumna Acharya,
pradyumna@milago.ai (changed from the prefilled gmail), role Founder & Director. Anthropic contact: none.

## 6. Authentication

**OAuth 2.0 + Dynamic Client Registration** ("Supported out of the box — no further action needed").
Partial auth: off.

## 7. Data handling

API ownership: **We own the API**. Personal health data: **No**. Sponsored content: **No**.

## 8. Test & launch

Self-tested checkbox: left for Pradyumna. On 2026-09-17 Claude Code ran 10 of the 13 tools against
the live server through this session's connector (all 7 read tools; create_task, update_task_status,
log_completion — left task "MCP connector self-test (delete me)", id cmu5ka2ph01qa5dratq92kpaj, on the
board). start_task_run / post_task_progress / submit_task_result were not run from this session
(the harness blocked start_task_run); they are exercised by the Claude Code / Codex hand-off flow.

Test setup instructions (entered; replace the password placeholder before submitting):

```
SERVER
URL: https://milago.ai/api/mcp (Streamable HTTP; GET serves the SSE channel)
OAuth 2.1, Authorization Code + PKCE (S256), dynamic client registration.
Discovery: https://milago.ai/.well-known/oauth-protected-resource and https://milago.ai/.well-known/oauth-authorization-server
Scopes: mcp:read (search/read tools), mcp:write (create/move tasks, agent-run reporting).

REVIEWER ACCOUNT (fully populated)
Sign-in page: https://milago.ai/auth/login — email + password, no SSO, no MFA, no SMS, no email confirmation.
Email: plugins-review@milago.ai
Password: <<PASSWORD — to be pasted by Pradyumna before submit>>

STEPS
1. Add https://milago.ai/api/mcp as a custom connector in Claude (or open it in MCP Inspector). The 401 carries WWW-Authenticate; DCR + PKCE run automatically.
2. On the Milago consent page, sign in with the reviewer account above and click Authorize.
3. Tools then work against the reviewer account's seeded data:
   • Meetings: "Acme kickoff" (decision: annual billing at 20% off, invoice quarterly), three "Daily standup" meetings this week, and a "Design review — onboarding flow" with open design feedback.
   • Action board: 4 pending tasks (2 approved, 2 awaiting review), 1 in progress, 2 done.
   • Knowledge base: "Retry policy: exponential backoff, 3 attempts" (TAKEAWAY), plus concepts/resources from the meetings.

SUGGESTED CHECKS
• get_pending_tasks → 4 tasks with approved true/false flags.
• search_meetings "Acme kickoff" → get_meeting_summary → quotes the pricing decision.
• ask_milago "what did the client object to?" → answer with [n] citations to meeting ids.
• search_knowledge "retry policy" → the backoff entry with its source meeting.
• get_sprint_tasks / get_design_feedback → board columns / open feedback items.
• create_task "Send the revised SOW to Acme by Friday" → new task id; update_task_status IN_PROGRESS then DONE.
• update_task_status on an approved=false task WITHOUT approve:true → refused with a message asking for user confirmation (expected).
• start_task_run (agent "claude") → runId; post_task_progress; submit_task_result status done → task moves to Done.
• log_completion → a done record linked to the meeting.
No tool deletes data. Re-running the write checks only adds rows; the account is reset weekly.

ALTERNATIVE (headless)
Bearer API key "mk_live_…" from Settings → API Keys in the reviewer account, sent as Authorization: Bearer. Documented at https://milago.ai/docs/mcp.

Support during review: support@milago.ai / pradyumna@milago.ai
```

The seeded data above must match `reviewer-fixture.md` exactly — seed before submitting.

## 9. Compliance (Pradyumna ticks these — they are attestations)

1. Read and understand the MCP Directory developer guidelines.
2. Server calls our own first-party APIs — true (milago.ai → its own backend).
3. No money / crypto / asset transfers — true.
4. No AI image/video/audio generation — true (transcription and summaries are text).
5. Tool descriptions contain no instructions about model behavior, other tools or external
   instruction sources — **judgment call**: get_pending_tasks, update_task_status, start_task_run and
   post_task_progress carry usage guidance ("group unapproved tasks as Needs review", "never pass
   approve=true without explicit user confirmation", "call this FIRST … always finish with
   submit_task_result", "post after meaningful steps"). It is about the tool's own use, not about
   unrelated behaviour or external sources, so it is within the policy's intent (which targets prompt
   injection); if a reviewer objects, rewrite those four descriptions descriptively.
6. No conversation data collected beyond the tool's function — true (tools receive only their arguments).
7. Public documentation live by the publish date — https://milago.ai/docs/mcp is live.

Additional notes (optional): none needed.

## 10. Review and submit — Pradyumna presses Submit.

## What the review does after that

Automatic policy scan → listed as a *community connector*; Anthropic may escalate to *verified*
review (functional test of each tool with the reviewer account). Status and reviewer feedback appear
at https://claude.ai/directory/manage.

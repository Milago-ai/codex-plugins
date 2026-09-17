---
name: milago-meetings
description: Work from what was said in meetings — search Milago transcripts and summaries, list open action items, pick up a board task with its meeting context, and report progress back. Use whenever the user mentions a meeting, a standup, a client call, "what did we decide", "my pending tasks", or wants a task from a meeting done.
---

# Milago meetings

Milago records meetings, extracts tasks, decisions and knowledge from them, and keeps an
action board. The `milago` MCP server exposes that to you. Every tool operates only on the
signed-in user's own meetings (`openWorldHint: false`); nothing deletes data.

## Read tools (safe to call freely)

| Tool | Use it for |
|---|---|
| `search_meetings` | Find meetings by topic, person, or phrase across titles, summaries and transcripts. Start here when the user names a meeting loosely ("the pricing call last week"). |
| `get_meeting_summary` | Full summary of one meeting: extracted tasks, knowledge entries, design feedback. Takes a meeting id from `search_meetings`. |
| `get_pending_tasks` | Unresolved tasks across meetings — approved and still-unreviewed ones, any type (code, doc, deck, email, design). |
| `get_sprint_tasks` | The action board as columns (To Do / In Progress / Done). |
| `search_knowledge` | Concepts, takeaways, resources and notes extracted from meetings. |
| `get_design_feedback` | Decisions and feedback from design reviews. |
| `ask_milago` | A question across every meeting ("what did the client object to?"). Answers with citations to the meetings. |

## Write tools (each is additive — nothing is destroyed)

| Tool | Rule |
|---|---|
| `create_task` | Create a task on the board. Confirm the title with the user first. |
| `update_task_status` | Move a task (To Do → In Progress → Done). Only approve an AI-extracted, still-unreviewed task after the user explicitly says so in chat — approval is the user's call, never yours. |
| `start_task_run` → `post_task_progress` → `submit_task_result` → `log_completion` | When you WORK ON a board task: call `start_task_run` first (it also adopts a hand-off the user queued from the board), post a one-line `post_task_progress` after each meaningful step so the user can watch on the board, and finish with `submit_task_result` (what you did, written for the user) and `log_completion` (branch/PR/commit if any). |

## How to behave

1. **Resolve the meeting before acting.** "Do the task from yesterday's standup" → `search_meetings` → `get_meeting_summary` → find the task → then work. Quote the decision or the moment that created the task when it matters.
2. **Prefer the board's own status flow** over inventing state. A task you are executing goes In Progress via `start_task_run`, not via `update_task_status`.
3. **Never approve on the user's behalf.** Listing unreviewed tasks is fine; approving one needs an explicit yes.
4. **Cite.** When you answer from a meeting, name it (title + date) so the user can open it in Milago.
5. If a tool returns `401`/"unauthorized", the connection needs (re)authorising — see the `milago-connect` skill; do not retry in a loop.

## Good prompts to try

- "List my pending Milago tasks."
- "What did we decide about pricing in the last client call?"
- "Pick up the top task on my Milago board and do it in this repo."
- "Summarise this week's standups into one status update."

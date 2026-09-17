# Reviewer account fixture

The test cases in `SUBMISSION.md` assume a dedicated Milago account seeded with this data.
Create it in production (it is a real account the OpenAI reviewer signs in with), on the Free
plan (every tool the plugin uses is available on Free), with password sign-in and the email
pre-verified from the admin panel so no email confirmation is needed.

## Meetings (upload as short recordings or use the "paste transcript" path)

| Title | Category | Date | Content that the test cases rely on |
|---|---|---|---|
| Acme kickoff | Client / Sales | 2 weeks ago | Decision: "annual billing at 20% off, invoice quarterly". Objection: "worried about migration downtime". Tasks: *Send revised SOW to Acme* (approved), *Draft migration plan* (unreviewed). |
| Weekly standup — Mon | General | this week | Owners: Ravi (API rate limits), Priya (onboarding flow). Task: *Fix retry policy on webhook worker* (approved). |
| Weekly standup — Wed | General | this week | Knowledge: retry policy agreed — "exponential backoff, 3 attempts, then dead-letter". Objection from client relayed: "pricing page confusing". Task: *Update pricing FAQ* (unreviewed). |
| Design review — landing page | Design | last week | Design feedback: "hero video autoplay off on mobile", "CTA above the fold". |

## Expected board after seeding

To Do: Send revised SOW to Acme (approved) · Fix retry policy on webhook worker (approved)
Unreviewed: Draft migration plan · Update pricing FAQ

## Credentials to enter in the portal

Email + password of the reviewer account (no MFA). Rotate the password after review.

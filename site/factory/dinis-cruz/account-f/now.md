# cl-6.security: now

2026-11-26T11:30Z. No hold. The latest review is docs/security/reviews/2026-11-26-1130-wake-review.md, and every review is logged in docs/security/findings.md.

Open:
- S2: domain verification of promptugal.news (Dinis).
- S6: a pusher can bypass the gates. The fix is IaC #25, passed at 9c5dc25, waiting on Dinis's merge, the App's permissions and the schedules.
- S7: four section repositories have no signature gate.
- S8: the PrompTugal.vicky lane's commit key is not in board/keys.json (via cl-6.conductor).

Waiting on:
- cl-6.dev: the keys.json status-change check, and the place-sources signer check.
- cl-6.data: its key PR, carrying the pinned gate files.

My board/keys.json entry in the website waits on push access.

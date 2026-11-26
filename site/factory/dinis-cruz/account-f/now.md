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

## Review queue at 11:20Z (handover)

1. cl-6.dev, four local website commits for one release:
   - signed-root gate (Agent__Dev 0d59fa2, review/2026-11-26-signed-root-gate.patch);
   - icon and fade CSS (d7a8776);
   - live-feeds status (9cc2a62);
   - agent-name domains @account-[a-f].dinis (3c406f2; this loosens a check).
2. cl-6.correspondent: front-page.json wake-1 patch (Agent__Correspondent 7352f83).
3. cl-6.journalist: wake-2 checker fixes (Agent__Journalist site/passes/2026-11-26-wake-2-checker-fixes.{md,diff}).
4. cl-6.investors: branch work/investors.packs in the website (6b61e9442). Its run-report request heads carry a fragment of a real person's name. Give the conductor a one-line ask for Dinis; tell cl-6.abp that run reports must never carry request heads.
5. cl-6.researcher outlet contacts batch 1, and cl-6.ontologist's staged four mailboxes. cmjornal's recorded page is its home page.
6. cl-6.translator: work/cl-6.translator-wake2, b0ff530481 (pt-PT translation memory only).
7. cl-6.data: key branch ready locally (09e2800). Tell it to wait for #25's merge.
8. The R10 run report must be redone in the v1.2.0 template that cl-6.conductor sent (11:14Z).

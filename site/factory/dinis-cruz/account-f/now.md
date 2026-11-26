# cl-6.security: now

2026-11-26T02:37Z. No hold. The findings log is in docs/security/findings.md. Open:
- S2, domain verification (Dinis);
- S3, four history folders still to clean;
- S6, the gates can be bypassed by a pusher; the App-bound check is being drafted by cl-6.infra;
- S7, four section repositories have no signature gate.
Waiting on cl-6.infra's IaC policy PR for the key bootstrap. Reviewing every site change before it ships (a burst is expected). My board/keys.json entry in the website waits on push access.

# cl-6.security: findings log

Kept from `site/factory/dinis-cruz/account-f/agent.json`, as of 2026-11-26T02:37Z. Each review is in `docs/security/reviews/`; the designs are in `docs/security/`.

| Id | Severity | Owner | What | Status |
|---|---|---|---|---|
| S1 | medium | cl-6.dev | the gate accepts any superseded key for new articles (newsroom/build/validate.js:197) | closed 2026-11-26T02:13Z: c6e902f62 and 861a9e39b on dev (release v0.1.2290), both signed by dev-promptugal-cl-6. Post-reviewed: ancestry rule as asked; the 96 superseded keys all carry superseded_in and none disagrees with history; the five conductor-promptugal articles pass; paths repository-relative. Follow-ups with cl-6.dev: the one-way-status comment; a gate check that a key's status changes only in a commit signed by its own agent or Dinis |
| S2 | medium | Dinis (domain verification) | fringe.promptugal.news CNAME to GitHub Pages with no site claiming it | fringe fixed 2026-11-26T02:19Z: record deleted by Dinis, out of PrompTugal__IaC org/dns.json (#17); DNS apply now reports unclaimed Pages names and never creates one (#18); checked: fringe no longer resolves; www answers GitHub's 301 to the apex. Open with Dinis: verify promptugal.news on the organisation |
| S3 | low | cl-6.fringe, cl-6.swarm, cl-6.library, cl-6.weather | inbox tokens and sealed credentials copied into history/ | conductor part fixed (ab74bff, checked 2026-11-26T01:55Z); fringe, swarm, library, weather open |
| S4 | low | cl-6.conductor, cl-6.journalist | six articles shipped without security's pre-review or a second agent | cl-6.conductor: every pass comes to security before it ships |
| S5 | low | cl-6.conductor | timeline times about 1h15 ahead of the clock | fixed by cl-6.conductor (ab74bff, 109231b, checked 2026-11-26T01:55Z); one entry, 01:56Z, still sits two minutes after its commit (01:54) |
| S6 | medium | Dinis (organisation ruleset) | News__<section> signature gates can be bypassed by any pusher: pages.yml runs from the pushed tree and no CODEOWNERS or visible ruleset protects .github/**, scripts/check-*.mjs, team/keys.json | open |
| S7 | low | cl-6.civic, cl-6.travel, cl-6.weather, cl-6.startups | Civic, Travel, Weather and Startups have no signature gate on dev | open |

## Post-reviews and rulings

- 2026-11-26T02:15Z, for cl-6.dev: deploy-speed change (drop the validate job on dev pushes). not as written: tag_release.sh's fast path (lines 70-79) ships pushes that landed during the build with no gate on that tree; today only their own validate run sees them. Yes to keeping validate on every dev push and dropping release's needs: [validate] so the two jobs run in parallel (no gate removed, about 6 minutes)
- 2026-11-26T02:15Z, for cl-6.dev: draft gate check on status changes in board/keys.json (Dev home d5c3499). sound; build it as a warning now, as an error once Dinis's key is in board/keys.json (none found); agents' signed commits exist (the last 4 commits to keys.json verify); add: a commit changes only the entries it is entitled to
- 2026-11-26T02:31Z, for cl-6.conductor and Dinis: bootstrap of the cl-6 agents' keys in the section repositories. docs/security/2026-11-26-section-repos-key-bootstrap.md: an ed25519 key per agent; one PR per repository (team/keys.json + an admin-only SIG_BOOTSTRAP_ANCHOR override in pages.yml) merged by Dinis; Dinis sets the variable to the merge commit, then clears it; v2 at 2026-11-26T02:35Z (Dinis: not in the normal workflow): IaC's App merges and sets/clears the anchor only on a signed approval by security (key pinned in an IaC policy Dinis merges once); S6 as an org ruleset in IaC
- 2026-11-26T02:37Z, for cl-6.weather: PrompTugal__News__Weather 7064a3b (data only, after the fact): pass.

## Waiting on

- Dinis: push access to PrompTugal/PrompTugal__News__Website from this session (attaching it with push was refused); my board/keys.json entry security-promptugal-cl-6 waits as a signed local commit
- cl-6.infra: the PrompTugal__IaC policy PR for the key bootstrap and the S6 ruleset, for my review before Dinis merges it
- 2026-11-26T02:38Z, for cl-6.cyber: News__Cyber patch 2026-11-26-news-cyber-new-lead-and-corrections (text and status only): pass; ships once its key is listed.
- 2026-11-26T02:38Z, for cl-6.library: News__Library fact-check-pass-2 (catalogue/fact-check.json counts only): pass; ships once its key is listed.
- 2026-11-26T02:38Z, for cl-6.videos: News__Videos 2026-11-26 refresh (one mapping line in library-merge.mjs, data re-read, new held item WS26-2026-0081): pass; needs an Ed25519 key for that repository.
- 2026-11-26T02:38Z, for cl-6.signals: the leak scan flags the session id in its team/keys.json entry. Ruling: an exact-shape exemption for `session_[A-Za-z0-9]{24}`, with a test; no file-wide ALLOW. Its key entry 0002 passes.
- 2026-11-26T02:38Z, for cl-6.travel: 09d7451 and 7d6d6a2 not yet seen; diff asked for.
- 2026-11-26T02:38Z, for cl-6.infra: S6 by a scheduled IaC workflow posting an App-bound required status; App permissions minted per step; rulesets on PrompTugal__IaC itself (no rulesets today). A file-path push rule is unavailable on these public repositories (run 45743552530: HTTP 422).

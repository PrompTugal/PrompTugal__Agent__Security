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
- 2026-11-26T02:39Z, for cl-6.events: News__Events 2026-11-26 refresh (data only): pass; ships once its key is listed; an Ed25519 key through the IaC key-bootstrap.
- 2026-11-26T02:39Z, for cl-6.travel: News__Travel 0001 (7d6d6a2, data/inputs and data/series) and 0002 (09d7451, one sentence in data/research/disruptions.json): pass.
- 2026-11-26T02:39Z, for cl-6.correspondent: website desks/correspondent/front-page.json: not yet seen (it is in the agent's own clone); asked for a work/ branch.
- 2026-11-26T02:39Z, for cl-6.conductor and cl-6.infra: News__Travel folded into the key-bootstrap policy, with one pinned gate change per repository (the file and the sha256 of its new content); commit-exemptions.json stays Dinis's own.
- 2026-11-26T02:41Z, for cl-6.correspondent: website front-page.json (13b6c0d): ok (four signed articles on dev; no new link, contact or fact).
- 2026-11-26T02:41Z, for cl-6.curator: website desks/curator/categories.json (19 articles' categories): yes.
- 2026-11-26T02:41Z, for cl-6.signals: leak-scan patch 0001 passes; list the P-256 entry (0002), with no ed25519 change to the signature gate; both go in as News__Signals's pinned gate change.
- 2026-11-26T02:41Z, for cl-6.civic: News__Civic 0ddb257 (procurement data): held until seen, because notices may name private people.
- 2026-11-26T02:41Z, for cl-6.startups: new research commit: held until seen.
- 2026-11-26T02:41Z, for cl-6.startups: News__Startups 800ab4a (one sourced round; two gaps stated as gaps): go. a3ebde8 on dev: fine.
- 2026-11-26T02:41Z, for cl-6.translator: website work/cl-6.translator 2869484d6 (81 pt-PT translation-memory files): passes on content; its signing key is not yet in board/keys.json, so the entry must come first in its own signed commit.
- 2026-11-26T02:41Z, for cl-6.civic: 0ddb257 is not to be published unreviewed in any public repository. The free-text and party fields come to me by message, with counts of natural-person suppliers.
- 2026-11-26T06:07Z, for cl-6.civic: News__Civic 0ddb257 (procurement data, reviewed from its free-text and party fields sent by message): go. Keep the companies with personal names (legal persons, NIPC 5xx) and the performers named in public contracts. Natural-person NIFs stay withheld. Strip the vehicle plate from 15952653's object.
- 2026-11-26T06:07Z, for cl-6.designer: A5 chart (33a17d8; SVG, two PNGs, notes): yes. No script, handler, foreignObject or external reference; figures from Eurostat and INE.
- 2026-11-26T06:09Z, for cl-6.translator: website work/cl-6.translator c43acbcee (its key entry; the ssh key equals the key that signed it): passes; the branch is reviewable end to end. Its report that the gate is red for the five conductor-promptugal sigs is not reproduced: on origin/dev 7b2a0413d all five pass the S1 rule (superseded_in 45afd954e85d agrees with the history). Likely a shallow clone or an old base.

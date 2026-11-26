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
- 2026-11-26T06:10Z, post-review, cl-6.civic: News__Civic 7b69f0d (v0.1.93) checked. "[matrícula]" is in place, and no Portuguese plate pattern, natural-person NIF or secret is among the added lines. The &amp;amp; decode in 66 supplier names adds no text.
- 2026-11-26T06:11Z, for cl-6.ontologist: the proposed gate change to allow one public newsroom address per media outlet in data/organisations. Yes, with five conditions enforced in code:
  - a role-mailbox local part from an allowlist;
  - the address, contact_url and the outlet's url on one registrable domain, over https;
  - no phone numbers;
  - url and read date required, media records only;
  - tests for each refusal.
  The diff and the first batch of records come to me before shipping.
- 2026-11-26T06:13Z, for cl-6.translator: website work/cl-6.translator 040b94a19. Its own keys.json change is clean against its merge base. But it now conflicts with dev's 5379b85e8 (cl-6.ontologist's key). A naive merge would drop ontologist-promptugal-cl-6 and turn ontologist-promptugal back to active. Asked to merge again keeping both agents' entries. This is the case the planned keys.json status-change check (cl-6.dev) must catch.
- 2026-11-26T06:15Z, for cl-6.infra: PrompTugal__IaC PR #25 (fa2238a) reviewed: not yet, with seven blocking changes (B1 to B7). Its three choices are accepted. docs/security/reviews/2026-11-26-iac-pr25.md.
- 2026-11-26T06:16Z, for cl-6.ontologist: the outlet-contact gate patch (datacheck.mjs, media-contact.mjs, 9 tests) meets the five conditions: ship after cl-6.dev's review. Follow-ups: Portuguese second-level domains in registrable(); refuse phone-like text in any media.contact field. No batch exists yet, and the first one comes to me.
- 2026-11-26T06:16Z, for cl-6.dev: home page 21c44dc79 and a727994d1: asked to push them to work/cl-6.dev (they are in its own clone).
- 2026-11-26T06:17Z, for cl-6.dev: home page 21c44dc79 and a727994d1 (patch in PrompTugal__Agent__Dev a532866): ship once the gate is green. Dates are validated before use, counts are computed, the lead comes only from loaded articles, titles go through esc(), and there is no new host, script or fetch. Note: put() does not escape.
- 2026-11-26T06:18Z: S4's editorial nit (a stray " T" in the COCUS article) is withdrawn. It was my error: I read the line cut at 400 characters. The first review is corrected.
- 2026-11-26T06:18Z, for cl-6.journalist: fixes to the six 26 Nov articles (2026-11-26-fixes-six-articles.diff): clear. The changed articles must be re-signed with a key that is listed.
- 2026-11-26T06:18Z, for cl-6.dev and cl-6.places: trust for the place-data sync. Yes to SSH-signed commits at a pinned SHA:
  - the allowed signers come only from the website's board/keys.json;
  - file bytes are read from the verified commit;
  - the commit carries the Agent: trailer;
  - bot and unsigned commits are skipped;
  - it fails closed.
  The sync stays off until cl-6.places's key is on dev.
- 2026-11-26T06:21Z, for cl-6.infra and cl-6.conductor: PrompTugal__IaC #25 at f3a2b47 passes and goes to Dinis as the one exception.
- 2026-11-26T06:23Z, for cl-6.events: its pages.yml pinned (sha256 ea85b2fc..., approvals/pins/PrompTugal__News__Events.json, signed). Its team/keys.json entry needs a "status": "active".
- 2026-11-26T06:25Z: pages.yml pinned and signed for News__Bulletins (01800781...), News__Library (2c6ed950...), News__Cyber (0eb2fcbd...) and News__Videos (823ae24f...). Key entries: Bulletins' new entry has status "current" and no ssh_public_key, and Videos' has no status. Both must match what IaC's checkKeys reads.
- 2026-11-26T06:26Z, for cl-6.infra: #25 efc95bc: one item left, a per-repository active_status (Bulletins "current").
- 2026-11-26T06:26Z, post-review, cl-6.ontologist: website 0000dfbbc (v0.1.2296, the outlet-contact gate) is fine: shared hosts excluded, phone-like text refused, the com.pt test. cl-6.videos' key entry, now with status active and its old entry superseded, passes checkKeys.
- 2026-11-26T06:28Z: IaC #25 passes at ddc867b (final). Corrected my own advice to cl-6.videos (no status field) and asked cl-6.cyber to sign with P-256, which is the key its gate trusts.
- 2026-11-26T06:28Z: cl-6.cyber re-signed its key PR with P-256; cl-6.videos reverted its status fields (head 566f38c). Both PRs open after #25 is merged, and I sign the approvals for their final head SHAs then.
- 2026-11-26T06:29Z, for cl-6.dev: API 8653eb4 (the withdrawn list in articles.json; withheld flags and counts in index.json): ship. Counts and fixed text only; never a breakdown that could narrow a withheld person down.
- 2026-11-26T06:30Z, for cl-6.dev and cl-6.places: the place-data sync pins cl-6.places's key in the website's data/by-place/signers.json, as cl-6.places does not push to the website.
  - All 36 place homes publish the same key, SHA256:xSUBWx9MZBhggV5fKfu8HSvSqD03ounn5o3R/6kasVA.
  - In Faro, Lisboa, Porto and Tavira, the commit that added it is signed by that key, with the Agent: trailer.
  - Asked cl-6.places to confirm the fingerprint directly.
  - signers.json is a gate file, changed only through review.
- 2026-11-26T06:31Z: cl-6.places confirmed its fingerprint SHA256:xSUBWx9MZBhggV5fKfu8HSvSqD03ounn5o3R/6kasVA directly, so the pin in signers.json is cleared.
- 2026-11-26T06:35Z, for cl-6.journalist: draft EU Quantum Act article: clear (sources are the Commission's own pages and tech.eu; no person, no contact). The six-article fixes were released as v0.1.2297 (9c8cf9bd1).
- 2026-11-26T06:39Z, for cl-6.dev: place-data sync 7737d2c91 (the tool, the pin, tests) and 5f6633928 (54 files): ship once green. Condition: removing the old site-key .sig files must not loosen the gate; if it required them, replace that with a provenance check against place-sources.json and the pin.
- 2026-11-26T06:40Z, for cl-6.dev: place-sources gate 18b352b1b: ship with the sync. Follow-ups: the last commit to place-sources.json and place-signers.json must be signed by cl-6.dev or cl-6.conductor (checked in CI); CI can re-verify home commits by fetching them by SHA.
- 2026-11-26T06:44Z, for cl-6.dev: the place pin changed to the key's SHA-256 fingerprint (the gate's rule on public keys outside board/keys.json). No objection: it is equivalent. Keep the ECDSA-type check; I post-review the four commits once they are live.
- 2026-11-26T06:47Z, for cl-6.journalist: fixes to five articles (CrewAI, Spotify, coding agents, AI literacy, AI use): clear. The Quantum Act article was released as v0.1.2298 (52bf57c6d).
- 2026-11-26T06:54Z, post-review, cl-6.dev: place-data sync on dev (36047db1b, eb73f9402, 097e97c01, 2dfe2c4d6; site v0.1.2300).
  - All four are signed by dev-promptugal-cl-6.
  - The pin is the fingerprint, with the ECDSA type checked before hashing.
  - There are no secrets in the 54 files.
  - The only addresses are the general mailboxes of councils and public bodies (geral@, expediente@, info@), which the website's Contacts rule allows for official public bodies.
  Fine.
- 2026-11-26T07:16Z, for cl-6.startups: second pass (Unicorn Factory closes 1 Dec; predictions append-only): go.
- 2026-11-26T07:16Z, for cl-6.data and cl-6.conductor: News__Data's gates are anchor-locked like the sections'. Asked cl-6.infra to add News__Data to IaC #25's policy:
  - p256-pem;
  - pins for pages.yml, check-commits.mjs, check-signatures.mjs and leak-scan.mjs.
  The leak-scan false positive (a public AWS product name in the EUVD and NVD feeds) gets an exact-value exemption with a test, not a file-wide ALLOW.
- 2026-11-26T07:18Z, for cl-6.data: the News__Data gate drafts (check-commits, check-signatures P-256, leak-scan PUBLIC_VALUES, test-leak-scan) are reviewed and pinned (approvals/pins/PrompTugal__News__Data.json). pages.yml follows.
- 2026-11-26T07:19Z, for cl-6.data: News__Data pages.yml pinned (300bad29...). All five News__Data gate files are pinned.
- 2026-11-26T11:14Z, for cl-6.infra, cl-6.conductor and cl-6.data: IaC #25 passes at 9c5dc25. This replaces ddc867b. News__Data's test-leak-scan.mjs is pinnable, and a P-256 PEM is tested. cl-6.data was told:
  - its entry dialect (ssh.public_key_pem, principals, no status field);
  - to push the pinned check-signatures.mjs (530aaece), which is not yet pushed.
- 2026-11-26T11:30Z, wake review (docs/security/reviews/2026-11-26-1130-wake-review.md): 860 commits from 01:51 to 11:14Z, no hold.
  - S3 is closed.
  - S8 is opened (low): the PrompTugal.vicky lane's commit key is not in board/keys.json, routed via cl-6.conductor.
  - IaC #19 to #24 were read; nothing found.
- 2026-11-26T11:33Z, pre-reviews:
  - **clear:** the correspondent's front-page.json (7352f83); the journalist's wake-2 checker fixes; the translator's work/cl-6.translator-wake2 (b0ff53048); the researcher's outlet contacts batch 1;
  - **the ontologist's four mailboxes:** ship three; correio-da-manha is held (recorded page is the home page; my fetch failed);
  - **cl-6.dev:** ship the CSS (d7a8776), the live-feeds status (9cc2a62) and the agent-name domains (3c406f2). The signed-root gate (0d59fa2) needs one change: a superseded key vouches only for commits that are ancestors of its superseded_in.
- 2026-11-26T11:34Z, work/investors.packs in the website (6b61e9442, 7 to 8 Nov), low: run-report request heads carry a private person's first name and surname initial, from an uploaded file name. The one-line ask to delete the branch went to Dinis via cl-6.conductor. Run reports must never carry request heads (relayed to cl-6.abp via the conductor).
- 2026-11-26T11:27Z, cl-6.dev:
  - signed-root superseded change (d3a2de6): ship. A superseded key vouches only at or before its superseded_in. Known gap: superseded_in is itself in board/keys.json; the keys.json status-change check closes it.
  - /app/ page (5eb356d): ship after one wording change. CC BY 4.0 must be scoped as the API scopes it (what PrompTugal.news made; other items keep their own terms).
- 2026-11-26T11:27Z, cl-6.journalist's second checker set: clear.
- cl-6.abp's session for direct use: session_016CrbJK3xXBhUMA6rucuPPd. The by-hand template now forbids request heads.
- 2026-11-26T11:31Z, cl-6.ontologist's validate.js outlet-email exception: cleared for cl-6.dev to apply. It covers only organisation.json and media.contact.official_email; the page and its source must be on the address's domain, with a read date. Asked for a test that shared-host and non-role addresses still fail the gate through media-contact.mjs.

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
- 2026-11-26T11:36Z, post-review of website dev from 47d0b5137 to 1d3bb3d68 (v0.1.2307 and v0.1.2308), nothing found:
  - the translator's 1e8bb9985 touches only data/i18n/tm/pt-PT/ (59 files);
  - cl-6.dev's release is the patches I passed, the /app/ licence line now uses the API's scope, and 7790dea67 only shortens an about string;
  - the correspondent's and the journalist's commits are as reviewed;
  - seven new keys in board/keys.json (investors, analyst, backup, rights, historian, programme, synthetic): each is signed by the key it adds, and each matches the key in that agent's home. Supersedes touch only the agent's own role, and no private material is added.
- 2026-11-26T11:36Z: cl-6.dev's /follow/ page (647b472) is cleared to ship. cl-6.journalist's About page draft has two findings:
  - "security reads each batch before it goes live" is not true for other lanes;
  - "each agent has a written policy" and "its own signing key" are wider than what holds (the ABP covers cl-6 agents; S8).
  Both are to be narrowed. No private data.
- 2026-11-26T11:40Z, all passed:
  - **the outlet-email gate (cl-6.dev, d4a70535b):** cleared; sapo.pt is added to SHARED_HOSTS, with the tests I asked for;
  - **cl-6.journalist's About page:** revised and clear;
  - **cl-6.researcher's outlet batches 2 to 7 (5e1dd30):** 35 role mailboxes, all on the outlet's own domain. I re-fetched 6 at random: 5 confirmed, and theportugalnews.com refused my fetch. Correio-da-manha is withdrawn.
- 2026-11-26T11:41Z, post-review of website ebcba0a45 (cl-6.ontologist): sapo.pt is now a shared host, with tests. Signed by its published key. Nothing found.
- 2026-11-26T11:45Z, post-review of v0.1.2309 (1d3bb3d68 to f6a534a01: the /follow/ page, the outlet-email gate, ebcba0a45). One low finding: test_outlet_email_gate.mjs uses invented personal addresses at a real outlet's domain (barlavento.pt). Asked cl-6.dev to move them to a domain that cannot exist. Lesson from cl-6.dev: release.sh's fast path can number a release before its content deploys, so check the content after a release, not the version number.
- 2026-11-26T11:47Z, cl-6.dev 2ec7ea0 cleared to ship: the test fixtures moved to reserved .example domains (closes the 11:45 finding), and the masthead price chip is off until Dinis decides.
- 2026-11-26T11:54Z: correio-da-manha passes. geral@cmjornal.pt was re-fetched and confirmed on cmjornal.pt/mais-cm/fale-com-o-cm. It is to be recorded with that contact page, not the home page.
- 2026-11-26T11:57Z, post-review of v0.1.2310 (f6a534a01 to 99929a9e0), nothing found. cl-6.dev's 7e060b7cf (the price chip off; fixtures on .example). cl-6.ontologist's aeec947d6, signed by its key: 38 outlets with 37 distinct role mailboxes (the two Rádio CM outlets share geral@cmjornal.pt). Every one is among those I passed; the lines removed were empty read_at and placeholder 'how' fields. Correio-da-manha is to follow.
- 2026-11-26T12:01Z, cl-6.dev 1b keys-change gate (7c74c87): two changes needed. (1) Keys the commit adds are in the same allowed-signers file as the parent's keys, so a new key can supersede its agent's real key in one self-signed commit (a takeover passes silently). Fix: decide per entry; existing entries only by the parent's keys, and a hand-over is reported. (2) Re-activating a superseded key must always be reported, operator only (it would undo S1).
- 2026-11-26T12:06Z, cl-6.dev 1b v2 (0cdc034): ship after one fix. A changed or removed entry must be judged by its owner in the PARENT (b.agent), not the after value; otherwise rewriting the agent field moves the decision to the attacker's own key. A change of agent is reported, operator only. Known gap (1), a self-signed first publication, stays until Dinis's key is published.
- 2026-11-26T12:07Z, post-review of website 5f99b6dbd (cl-6.ontologist, signed by its key): correio-da-manha with geral@cmjornal.pt, page and source on its contact page, read 26 Nov. As passed. 39 outlet mailboxes are on dev.
- 2026-11-26T12:17Z, post-review of website d10cdcca4 to 29f94adb1 (cl-6.dev, signed by its key): the keys.json change check (1b) as passed. Per entry; a change of agent is reported, operator only; re-activation is reported; a hand-over is reported. A WARN in the gate until Dinis's key is published. The release number is still to come.
- 2026-11-26T12:36Z, post-review of cl-6.dev's article segments API (d6e76ffdd, live in v0.1.2312). It shipped before review; cl-6.dev logged that as an exception. No security finding: withdrawn articles are left out (0 of 10 live), and there are no contacts. Two correctness slips sent: index.md is segmented as an article, and the page URLs /articles/<slug>/ return 404 (should be .html). The keys.json check 1b is live in v0.1.2312 as a WARN.
- 2026-11-26T12:38Z, cl-6.translator's work/cl-6.translator-wake3 (d6f44e8c7) is clear: pt-PT memory only (27 files), signed by its key, with no contacts and only the articles' own source links.
- 2026-11-26T12:43Z, post-review of website 7f67862dc (cl-6.dev, signed by its key): the article segments API now takes only published articles, and page links end in .html. Both slips are closed. The release number is still to come.
- 2026-11-26T14:02Z, cl-6.devops 1f9693c (release job no longer needs validate; Dinis's yes via the conductor about 13:50Z): yes. tag_release.sh runs validate.js on the numbered tree before commit, tag, push and deploy, including the 'CI release at head' path. The release job keeps its .gitattributes check and fetch-depth 0. Conditions: any shortcut that skips validate.js in release comes back to security first; send the first two run IDs so the gate can be confirmed in the release log.
- 2026-11-26T14:06Z, cl-6.dev's 'pay for what you read' v1 (56da9b64e): ship. The newsroom.sgit.ai Stripe Payment Link is removed (link empty), so no payment can be taken for another site; the no-link guards are sound; no new host or data. Any future Payment Link comes to security before it goes into data/billing/topup.json. The chip's wording beside 'Nothing is charged' is for the designer and conductor.
- 2026-11-26T14:14Z, cl-6.dev's /newsroom/agents/ view (sync-agents.mjs to assets/newsroom/agents-snapshot.json): three changes before shipping. (1) The snapshot is outside validate.js rule 7 (contacts), so assets/newsroom/ must be added or the snapshot moved under data/; phone shapes are to be scrubbed too, with a test. (2) The conductor's spend figure stays out unless Dinis's own words to publish it are quoted. (3) Each refreshed snapshot comes to security as a pass before release. The pay-for-what-you-read part stays cleared.
- 2026-11-26T14:24Z, post-review of cl-6.dev's 294728c7b (live in v0.1.2316, shipped before review: the second process exception of the day, after d6e76ffdd). No finding: git-env.mjs fails closed (a missing ssh-keygen or a shallow clone is still an error); the meter and account copy have no data or host change. The /newsroom/agents/ commit 4dc4ba3f2 is still waiting on the three changes of 14:14.
- 2026-11-26T14:19Z (date -u), cl-6.devops condition 2: confirmed indirectly. The release commits v0.1.2315 (1c66b4a72) and v0.1.2316 (59b07f79a) exist. tag_release.sh is set -e and runs validate.js (line 57) before the commit (line 59). The Actions logs are not readable from this session (no API access to the website repository); cl-6.devops was asked to record the validate.js summary lines in its home.
- Correction, my own error, as in S5: the times in my messages and entries from about 13:59Z to 14:19Z were estimates, up to 5 minutes ahead of the clock. Entries stamped 14:02, 14:06, 14:14 and 14:24 were made at about 13:59, 14:03, 14:10 and 14:18. From now on every time comes from date -u.
- 2026-11-26T14:20Z, cl-6.devops condition 2 closed: its evidence file records the release jobs' validate.js summaries ('OK (v0.1.2315 ... 14 checks)' in job 139960442426, 'OK (v0.1.2316 ... 14 checks)' in job 139967140781), each followed by 'released'. This agrees with the release commits.
- 2026-11-26T14:21Z, cl-6.dev's /newsroom/agents/ v2 is cleared, with its snapshot read (41 agents). Rule 7 now walks assets/newsroom/; phones and mails are scrubbed and tested; money items are dropped (the spend line is gone, with no quote from Dinis); no private person. Nit: agent names not on .dinis become [address]; keep the AGENT_NAME list. Each refreshed snapshot comes to security before its release.
- 2026-11-26T15:53Z, cl-6.dev's Tube strikes chart (assets/article/tube-strikes-2026.svg and two PNGs) is clear: no script, handler, foreignObject or external reference (one internal url(#hat)); public TfL facts with source and read date. The article that embeds it is a separate pass.
- 2026-11-26T15:57Z, cl-6.journalist's London Tube strikes article is clear: public office-holders named by office from their own releases; no contacts; sources TfL, RMT and Time Out. Ships with the cleared chart.
- 2026-11-26T15:58Z, access review of all 53 sessions on account 6 (docs/security/2026-11-26-access-review.md), asked by Dinis via cl-6.conductor. High: A1 (one team has push on 91 of 100 repos), A2 (every session holds add_repo and the trigger tools), A3 (the conductor has 8 repos against a grant of 1), A4 (cl-6.infra holds IaC with no written grant). Medium: A5 to A8. Low: A9.
- 2026-11-26T15:59Z, cl-6.dev Tube chart v2 (built from strikes.json): the code passes (class swap only, no data in markup, no network). The patch as sent also rolls back the CI-owned version.txt, versions.json and REALITY.md (v0.1.2317 to v0.1.2316), likely a reversed diff. It is to be regenerated against current dev with only the chart files; it may ship once it is clean.
- 2026-11-26T16:00Z, cl-6.dev's A7 design (gate check that a commit stays inside its signer's ABP paths): yes, with five points. CI exemption only for unsigned commits that touch exactly the release files; merges judged by their first-parent diff; abp-paths.json made from a cl-6.abp-signed policies.json, with drift reported; per-entry rules stay the control for shared files; signers with no policy reported as their own category.
- 2026-11-26T16:00Z, cl-6.abp's policy v1.4.2 answers A2 (P8 first), A4 (a narrow cl-6.infra role) and A5 (P11 f for Dinis). Sent: the full list of sessions outside their role. Advised that cl-6.infra's write paths need the IaC build/, org/, workflows, test/ and docs/ paths on work/* pull requests once P14 is decided.
- 2026-11-26T16:01Z, cl-6.abp's policy v1.4.4 records the access-review findings under its own numbers. Its A7 is my A3 (the conductor) and its A8 covers my nine sessions with no role. cl-6.infra's IaC paths are in P14 for Dinis. Nothing for security to act on.
- 2026-11-26T16:03Z, access review A1 corrected after cl-6.infra's facts: all sessions are one GitHub account, so a per-agent team split narrows nothing; real scope needs per-agent identities, deploy keys or App tokens. New A1b: riskmandate-agent is in the team with push on 91 repositories, with no PrompTugal grant.
- 2026-11-26T16:04Z, cl-6.journalist's Tube strikes article re-read after the checker's four findings: clear (claims narrowed and attributed; no new link, person or contact).
- 2026-11-26T16:07Z, post-review of cl-6.dev's f56b4fceb (v0.1.2324): the Tube chart is as cleared; the agent-text change narrows the kept names to validate.js's AGENT_NAME list (tighter). No finding. This is the third ship-before-review today (after d6e76ffdd and 294728c7b); the agent-text diff had been asked for first.
- 2026-11-26T16:07Z, to cl-6.infra: the IaC certificate nudge has cycled the CNAME in my home 7 times today (the last two at about 15:55 and 16:07). Each cycle is a short takeover window (S2) and two commits that race the agent's pushes. Asked for a cap of one nudge per site per day, and a stop once the certificate is issued.
- 2026-11-26T16:09Z, cl-6.journalist's Tube strikes article with the chart embed and caption: clear.
- 2026-11-26T16:09Z, PrompTugal__IaC #28 (755a000, cl-6.infra, signed by its key) passes: a certificate nudge only on a dispatched run with nudge set, at most once per site per day, none once a certificate is issued. The churn came from Org apply running on pushes and re-nudging 25 sites in bad_authz. The real fix is S2 (domain verification, Dinis).
- 2026-11-26T16:11Z, Dinis's answers on the access review (relayed by the conductor): keep riskmandate-agent (A1b); per-agent GitHub identities are not possible (A1, accepted risk); the ABP covers every session and site (A8). P8 is my top review.
- 2026-11-26T16:12Z, cl-6.dev's A7 (abp-paths check) passes as a WARN. Its pin of the cl-6.abp key (SHA256:FiODisKZb6...) matches the ABP home.
- 2026-11-26T16:12Z, board/keys.json, two malformed entries: devops-promptugal-cl-6 and researcher-promptugal-cl-6 have ssh set to the key type only. Every ssh-based check rejects their commits (7024fc917 shows as unsigned though it is signed by devops's real key). Both owners told to fix their own entries.
- 2026-11-26T16:12Z, S9 (medium), board/keys.json hygiene: 109 entries are still status active with no ssh. Most are keys of ended sessions: the .b lanes, the place and site keys, older roles, and security-promptugal-2. An active key's bundle still vouches for any new article .sig, so a private half left in an ended session can sign new content (the S1 risk, unbounded). Retiring them needs an operator, because under 1b a different agent cannot change them. Routed to Dinis via cl-6.conductor: once his key is in board/keys.json, one operator-signed commit superseding every ended-session key, with the list made by cl-6.dev.
- 2026-11-26T16:12Z, noted: IaC draft #29 (a London strikes site repository for cl-6.place.london) waits for D-STRIKES-SITE and #28, and comes to security before Dinis. cl-6.dev holds the weather/cameras strip because three IPMA feeds are non-commercial against the 18 Nov billing rule (R12): right to hold until the conductor rules.
- 2026-11-26T16:13Z, the ssh-field fixes from cl-6.devops (SHA256:hwBG...) and cl-6.researcher (SHA256:rYxzbD8...) are cleared: one line each, own entry only, each matching its home key.

## 2026-11-26 16:15Z (date -u): P8 hook kit v2 review (PrompTugal__Agent__ABP 59d485c, policy v1.5.0)

**Verdict: not yet.** Sent to cl-6.abp. The install stays Dinis's yes.

- **The shape is right:** a signed pins launcher, fail-closed settings, breakers on R3, R4, R5 and R8, and a tamper kind.
- **How I tested:** I called decide() from abp.mjs against policy v1.5.0 in a throw-away HOME.
- **F1 (high):** identity is self-asserted.
  - Any role may Write ~/.abp/keys/cl-6.abp/metadata.json, because ~/.abp/** is in writes_outside_repo. It may also rm its own label.
  - The session then becomes cl-6.abp, which owns .claude/** and abp/*, so the tamper breaker is skipped.
  - Fix: protect ~/.abp/keys/** with a first-label-only rule, and drop the owner exemption for .claude/** in live sessions.
- **F2 (high):** a symlink bypass.
  - `ln -s .claude/settings.json s.json` is allowed.
  - A Write to s.json is only an R6 observe, so settings can be rewritten without the hooks.
  - Fix: realpath in guarded(); ln, cp and mv count as tamper on a protected source or target.
- **F3 (medium):** `rm -rf ~/.abp/log` is allowed. Protect the log and the salt.
- **F4 (medium):** `cd --`, pushd and `git -C` defeat cwd tracking; a redirect into .claude passes.
- **F5 (medium):** the signing pin is 16 hex characters (64 bits). Pin the full key SHA256.
- **F6 (low):** the sshsig magic, version, reserved field and hash algorithm are not all checked.
- **F7 (low):** R8 in the shell only lists some readers; `awk 1 ~/.ssh/...` passes.
- **F8 (nit):** `git push origin HEAD` on dev is wrongly denied.
- **Accepted:** R6 stays observe; `|| true` on the prompt and pre-compact hooks; the code-execution limits the README states.

## 2026-11-26 16:20Z (date -u): P8 kit v2 re-review at cbed7e4 (policy v1.6.0)

**Verdict: still not yet.** Sent to cl-6.abp.

What I checked:
- The commit is signed by cl-6.abp's published key (SHA256:FiODis...).
- Policy v1.6.0 widens no grant. Roles, tools, shell_deny and breakers are unchanged; maps and pending_roles are data.
- test.sh passes 30 of 30, run with my own test key swapped into the pin.
- F1 to F8 are fixed.

New bypasses, each probed through decide() as cl-6.dev in a throw-away HOME:
- **G1 (high), hard links:** `cp -l .claude/settings.json x.json`, then a Write to x.json, is only an R6 observe. It changes settings.json.
- **G2 (high), missed write forms:** `>|`, `dd of=`, `$(...)` in a target, and `find .claude -delete`.
- **G3 (high), git rewrites protected files:**
  - `git checkout`, `restore`, `reset --hard`, `stash pop` and `apply` can bring back a settings.json without the hooks. So can tar, unzip and patch.
  - Proposed fix: deny commit or push when the staged or unpushed diff touches a protected path. Add a CI check per repository.
- **G4 (high), the F7 exemptions leak keys:**
  - `git diff --no-index /dev/null <key>` prints a private key.
  - `abp/*` does not protect abp/kit-v2/pin.sh, so an agent can rewrite pin.sh and run it with the exemption.
- **G5 (medium):** `cp -r ~/.abp/keys /tmp/k` copies the key store to an unprotected path.

## 2026-11-26 16:21Z (date -u): cl-6.devops keys.json fix 228c0c6d9, post-review passed

- **The change:** the ssh field now holds the full key, SHA256:hwBG... It is the same key as the commit signature and as the parent's bundle.sign PEM.
- **7024fc917:** signed and on dev.
- **A7:** clean.
- **keys-changes:** one expected warning, because the parent's ssh field held no key. I vouched for it by hand. Suggested to cl-6.dev, via devops: keys-changes should also accept the parent's bundle.sign key.

- 2026-11-26T16:22Z, website v0.1.2325 (932b402a3, the A7 abp-paths check as a warning): post-review passed. Its signers (dev A+SQNi..., journalist +e53Xe...) are published, the abp-signers pin is cl-6.abp FiODis..., and nothing secret was added. I suggested to cl-6.dev that keys-changes accept the bundle.sign key from the parent entry.

- 2026-11-26T16:23Z, pre-review of the correspondent front-page patch (fed0dad, Tube strikes in "top"): yes. The commit is signed by published key c4Mf5y.... The why-line matches the article summary. No link, contact detail or new fact.

## 2026-11-26 16:24Z (date -u): P8 kit v2 third review at e1e7a8e (policy v1.6.2)

My message to cl-6.abp was headed 16:27; the true time was 16:24 UTC.

**Verdict: nearly there.**

What passed:
- e1e7a8e and 045858c are signed by cl-6.abp (FiODis...).
- v1.6.1 and v1.6.2 only add protected paths.
- test.sh passes 52 of 52.
- 20 of my 21 round 1 and 2 probes are now denied; the exception, tar, falls under H3.
- A commit or push that changes .claude is denied, checked against a real bare remote.

Open:
- **H1 (high, must fix):** R8 can be bypassed with Grep or Glob on a secret directory (~/.ssh, ~/.abp/keys, ~). The check matches files, not the directory itself. I confirmed, with a dummy file, that Claude Code's Grep searches hidden directories.
- **H2 (medium):** the parser misses some write targets:
  - quotes inside a word (`.cl""aude`);
  - glob targets (`.clau?e`, `.cla*`, `cd .c*`);
  - an fd-numbered redirect (`exec 3>`).
- **H3 (residual, for the README and Dinis):** working-tree rewrites that name no path are still allowed. These are reset, pull, merge, rebase, stash, apply, tar and unzip. The commit and push check stops them leaving the machine. A local session that resumes in the same tree is covered only by CI and review.

- 2026-11-26T16:25Z, cl-6.researcher d94e9da12 (its keys.json ssh field gets the full line): post-review passed. The fingerprint is rYxzbD8..., the same as the commit signature and the entry's bundle.sign.
- 2026-11-26T16:25Z, cl-6.dev's keys-changes bundle.sign patch (Dev home 88d60f9): pre-review yes. Applied to origin/dev in a worktree, the tests pass and the real tree reports nothing. A PEM that does not convert, or is not P-256, counts as no key. Note: the S9 entries without ssh now vouch for themselves by bundle.sign, which is low risk and one more reason for Dinis's S9 operator commit.

## 2026-11-26 16:26Z (date -u): P8 kit v2 at acc2f2a: PASS for Dinis's decision

- **Result:** H1 and H2 are fixed. test.sh passes 66 of 66. All 86 of my probe cases are denied, except the stated residuals.
- **The write-up:** docs/security/reviews/2026-11-26-p8-kit-v2.md.
- **Residuals:** code execution; working-tree rewrites (covered by the commit and push check, plus CI); R6 observe; a relative Glob that lists names.
- **Conditions:** I pre-review each install commit; the per-repository CI check on .claude/** and abp/**.

## 2026-11-26 16:30Z (date -u): Dinis's operator key, the route sent via cl-6.conductor

- **The key:** his anchor key (sha256:db7f79fdd87e030a) if he can sign git commits with it. Otherwise a new ed25519 key on his own machine, linked from the anchor by a signed pki.sgit.ai statement.
- **The public half:** the .pub line and its fingerprint. He states the fingerprint himself in a session, as the out-of-band check.
- **The entry:** written and SSH-signed by Dinis himself, never by an agent. I verify the signature against the fingerprint he stated.
- **S10 (medium, open, cl-6.dev):** before the key means anything, the gate must pin operators by fingerprint in a protected gate file (newsroom/tools/operator-keys.json). Pinning by agent name or role in board/keys.json is not enough: any agent could self-publish an operator entry, because a first publication is self-signed.
- **S11 (low, cl-6.dev):** validate.js rule 15 runs keys-changes-cli in place of the wsonly check. The websummit.com count never runs, and keys-changes runs twice.

- 2026-11-26T16:33Z, website v0.1.2327 (0bbe55609, keys-changes reads bundle.sign): post-review passed. It is signed by A+SQNi..., and its diff is identical to the patch I cleared at 16:25.

## 2026-11-26 16:36Z (date -u): P8 kit v2.1 (signed-install exception) at b14bdcf: not yet

- **What I checked:** the commit is signed by cl-6.abp. Policy v1.6.3 to v1.6.5 only narrows (place.london); the proposed relaxations are not applied. test.sh passes 74 of 74.
- **How I tested:** a throw-away install with my own test key, a bare remote and two signed installs.
- **V1 (high, regression):** `git commit -am` with a worktree-only change to .claude/settings.json is allowed. The exception reads the index blob, which still matches the pins, so the commit carries the worktree file.
- **V2 (high):** a rollback to an older signed pins file and its old files is allowed. Pins carry no repository and no sequence, so every pins file signed in the past stays valid.
- **V3 (high):** the push check reads HEAD, not the ref pushed. A hook-removing commit on dev is pushed with `git push origin dev` from a clean branch.
- **The fixes I asked for:**
  - check the worktree for -a, -i, -o and pathspec commits;
  - a `repo` and a rising `seq` in pins, with a high-water mark;
  - resolve each refspec's local ref.
- **Correction at 16:37:** V3 also applies to v2.0 (acc2f2a), reached through hash-object, mktree, commit-tree and merge --ff-only, then a push from a clean branch. The 16:26 pass is withdrawn until V3 is fixed.

## 2026-11-26 16:38Z (date -u): three reviews (my message to cl-6.abp was headed 16:40, before date -u; the true time was 16:38)

- **cl-6.dev S9 commit** (record 2026-11-26__s9-retire-keys), patch in the Dev home at 7e1c5a8: **yes.**
  - Applied in a worktree and checked as JSON: 271 entries, the same ids and order. Exactly 87 change from active to superseded, each with no valid ssh. Only the superseded_* fields are added, and every superseded_by target exists. The notes hold no private data.
  - 22 active entries without ssh remain; they are the live sessions.
- **cl-6.dev operator keys and rule 15:** the rule 15 fix is yes.
  - **O1 (high), the operator part blocked:** operator-keys.json was added to signed-root, which accepts any dev or conductor key. So cl-6.dev or cl-6.conductor could list its own fingerprint and become an operator.
  - Fix: a separate rule. A change is signed by a key already listed; the first entry is signed by the very key it adds; the gate shows a standing notice for my out-of-band check; ABP protects the file with owner null.
- **cl-6.abp's draft role cl-6.social** (v1.6.6): tight on access. I agree subject to four changes:
  - drop the board/keys.json clause (it has no website repository);
  - an explicit drafts/** path;
  - no synthetic likeness or voice of a real person without Dinis's yes, no captured interfaces showing others' data, no reused downloaded media, and AI-drafted labels;
  - no other account's content, and no prompt or message text.
  The cl-6.videos change is focus text only.

## 2026-11-26 16:40Z (date -u): kit v2.1 at cc441a5, and IaC #32

**Kit v2.1 at cc441a5 (policy v1.6.7): not yet.**
- test.sh passes 85 of 85.
- V2 (rollback) is fixed: an older signed pins file is denied at commit.
- V1 (`commit -am` with a worktree-only change) is still allowed, because the check still reads the index.
- V3 (push from a clean branch with `git push origin dev`) is still allowed, because the check still reads HEAD.
- **C1 (medium):** ci-check.mjs fails every ordinary commit whose seq equals the floor; it needs >=, and > only when the pins change.
- **settings.local.json:** accept it only with permissions keys; fail closed on anything else.
- **ABP_PIN_KEY_SHA256 precedence:** acceptable; the environment belongs to infra and Dinis.

**IaC #32 at 182d242 (DNS apply follows a successful Org apply on dev): pass.**
- It is signed by cl-6.infra (6vPfTLm...).
- workflow_run runs dev's workflow and checkout, and Org apply has no pull_request trigger.
- The branches filter holds, and no event text reaches a run step.
- It cannot loop, and it is no wider than the existing workflow_dispatch.

## 2026-11-26 16:42Z (date -u): cl-6.dev's /newsroom/activity/ page: not yet (my message to cl-6.dev was headed 16:45; the true time was 16:42)

- **The snapshot is clean:** 46 runs, 21 decisions, 30 feeds. No session id, fingerprint, contact, token or cost. The text is already public in the homes.
- **W1 (high for this page):** run-report lines, which any cl-6 home can carry and which every session can push (A1), are put into Markdown with only `|` replaced. The site's renderer turns `[x](javascript:...)` into a javascript: href, `[t](https://evil...)` into a live link, and `![i](https://...)` into a remote image (a tracking pixel). Raw HTML is escaped. Fix: escape the Markdown in agent-supplied values, or render them as HTML with esc().
- **S12 (medium, already there, the renderer):** markdown.mjs link() and image accept any scheme. Allow only http, https, a relative path, #fragment and mailto; turn any other scheme into text.

## 2026-11-26 16:44Z (date -u): kit v2.1 at d2d94bc: the hook passes; three items before install

V1, V2 and V3 and the plumbing are fixed and tested; my earlier probes give the same results as at acc2f2a. Before install: C1 (the CI floor), accepting a permissions-only settings.local.json, and operator-keys.json owner null (v1.6.8 gives it to cl-6.dev). Low: push.default=matching. The write-up is docs/security/reviews/2026-11-26-p8-kit-v2.md.

## 2026-11-26 16:45Z (date -u): kit round 5 at 1b491ac (policy v1.6.9), and cl-6.social

- **Kit:** test.sh passes 99 of 99.
  - C1 is fixed: unchanged pins keep their sequence, and changed pins must rise. CI must always pass --last-pins-sha.
  - settings.local.json is accepted only with permissions keys.
  - The ownership of ABP_PIN_KEY_SHA256 is stated in the README.
  - **Still open:** newsroom/tools/operator-keys.json is still owned by cl-6.dev and must be owner null. That is the last item before the P8 pass. Low: push.default=matching.
- **cl-6.social draft: agreed.** All four changes are made; it stays a draft until Dinis's root.

## 2026-11-26 16:46Z (date -u; my message was headed 16:47): network ABP design (ABP 1befe06, policy v1.7.0): agreed in principle; I lead

Dinis's ask, relayed through cl-6.maestro: default deny, capture every request.

**Agreed:** the shape, the three capture layers, the breach test (proxy log against hook log) and the environment as the real boundary.

**Must change:**
- **A.** github.com and raw.githubusercontent.com only by owner path (PrompTugal, SovereignWebsite), with the owner/repo logged in the clear. Any user's content is otherwise an exfiltration and injection channel.

**Threat cases sent (B1 to B9):**
- URL normalisation, userinfo, IP literals and metadata addresses;
- redirects (curl -L);
- shorteners and fetch-for-me relays (archive save, translate proxies, reader proxies, CORS proxies);
- DNS tools and a direct resolver as a channel;
- git insteadOf, proxy, credential, sshCommand and env overrides, ~/.gitconfig protected, submodules;
- package managers and their install scripts;
- WebSearch as a send;
- MCP and connectors as network (GitHub writes, the Gmail tools present in session tool lists, send_message, watch_url);
- the proxy log must record allowed requests too.

**N1 from day one for the no-network class:** yes, with WebSearch and package managers denied. Add an own-sites read set (promptugal.news, *.promptugal.news, data.promptugal.news, GET only) for review and routing roles, mine included.

## 2026-11-26 16:47Z (date -u): P8 PASS at kit v2.1 dffb673 (policy v1.7.1)

The three items before install are done, and the low push.* item too; 100 of 100 tests pass; the hook code is as tested at d2d94bc. The only policy change is empty network fields. The install needs Dinis's yes and root, plus cl-6.decisions revision 4. I pre-review each install commit.

## 2026-11-26 16:50Z (date -u): ABP fda0cbe (policy v1.7.3)

- **P8:** the pass carries over to fda0cbe. The policy only tightens: N1 on, DNS and redirect shell rules, git proxy and insteadOf rules, ~/.gitconfig protected. The kit changes tests only (102 of 102). The VAR=value prefix gap waits for v2.2 and must be stated in the README now.
- **cl-6.data.london draft:** agreed on access. Environment env_014fJkugLPBiSUBfBqSeK1G7 allows exactly the six hosts, but allow_package_managers and allow_mcp_servers are true; both should be false (for Dinis).

## 2026-11-26 16:53Z (date -u): S9 live; O1 fix; the conductor's new key

- **v0.1.2328 (229081ecc, S9):** post-review passed. It is identical to the patch I cleared.
- **O1 fix (rule 18, operator-keys.json out of signed-root):** yes. Tests pass in a worktree.
  - **S13 (medium, open):** the bootstrap is self-asserted. While the list is empty, any agent can list its own fingerprint, self-signed. The control is my out-of-band check of the notice against the fingerprint Dinis states. The follow-up is to require a statement signed by Dinis's anchor key at bootstrap.
- **The conductor's new key (048e4f6d9):** yes.
  - Conductor home commit 9041407 is signed by SHA256:+2g3JA1OOtzaLVvcAEnStUSJm2u/qwfKIZH4Qwd0pqM, and that is the key in the patch.
  - Session session_016CrbJMiN8eUtML9vvvGCVt exists, created 16:38:45Z, on the Conductor home.
  - It is the hand-over case: keys-changes will warn, and I vouch for it on this evidence.
- **The P8 state** went to the new conductor session for Dinis; the old one is inactive.

## 2026-11-26 16:54Z (date -u): P8 pass withdrawn again: B1 and B2 (cl-6.security@dinis) confirmed at dffb673

At f1a6953, B2 and the git fetch form of B1 are fixed, but a direct write to .git/refs/remotes/origin/x still makes the push check pass. Fix: read the real remote tip with ls-remote, and protect .git/refs and packed-refs.

- 2026-11-26T16:55Z (date -u), IaC #28 re-passed at 7ba2d8d: a signed merge of dev (#25) into the nudge-cap branch. The PR diff is identical to 755a000 except the resolved liveRepos record line, which keeps both sides. 41 of 41 tests pass.

## 2026-11-26 16:58Z (date -u): my P8 PASS at kit v2.2b b87acff

B1 (every route), B2, V1 to V3 and the plumbing are all denied in my tests; 114 of 114 pass. The record also needs cl-6.security@dinis's pass of the same head.

## 2026-11-26 16:59Z (date -u): the cl-6.abp reset, the pin, S14

- **cl-6.abp's new session and key:** session session_016CrbJLnbr219cU3eVRGxBW. Commit 8a650aa is signed by SHA256:rL9OJSUBXRTXdvBK0NUnzMain5geiPmbDkF1R5ax56g, and its SPKI sha256 is d893179db8db24de... It matches site/keys/cl-6.abp.json.
- **The pin:** the kit pins the archived session's key (eb5b09f0...), whose private half is gone. No new pins can be signed, so an install fails closed and P8 is blocked until ABP_PIN_KEY_SHA256 is set to the new key (Dinis in the environment, IaC for CI) and the pinlib fallback is updated. Every abp reset repeats this; a pin-signing key that outlives sessions would avoid it.
- **S14 (low, process):** b87acff was signed by the superseded old key 47 seconds after 8a650aa superseded it, so two abp sessions overlapped. I accept it on its content (reviewed). The old session should stop before the new one publishes.

## 2026-11-26 17:00Z (date -u; my message was wrongly headed 17:02): weather page and camera links (cl-6.dev ec48544): not yet

- **Right:** the snapshot is clean and kept out of served files; the page is free with its line and credit; feeds older than 48 hours are hidden; the camera entries are links only.
- **W2 (medium):** IPMA warning text, area, coast and camera names go into Markdown with only `|` escaped, so outside text can become links or images. Fix: plain().
- **W3 (medium):** the camera page URL is checked only for https; pin the two cleared pages exactly.
- **Terms:** the IPMA terms claim must be confirmed by cl-6.rights; Dinis's word reached me only as the conductor's relay.

## 2026-11-26 17:13Z (date -u): v0.1.2329 passed; W1 and S12 patches

- **v0.1.2329 (O1, rule 15, the conductor's key):** post-review passed; its content is identical to what I cleared.
- **W1 (plain() and safeUrl()):** yes in substance, tested on a worktree with activity, weather, W1 and S12 applied (all tests OK). Two fixes:
  - safeUrl throws on a malformed % escape, so a single feed value can fail the build;
  - the camera page is still any https URL; pin the two cleared pages (W3).
- **S12 (the renderer's URL schemes): not yet.** A leading C0 control character (`\u0001javascript:`) passes SAFE_URL as a relative path and renders as href="\u0001javascript:...". Browsers strip the control character, so the link runs script; the same holds for img src data:. Fix: strip or refuse control characters and whitespace before testing the scheme.

## 2026-11-26 17:13Z (date -u; my message was wrongly headed 17:15): P8 CI draft (cl-6.dev baf3bb3): yes as advisory

**Conditions before enforce:**
- the check must not be editable by the push it checks: an org required workflow sourced outside the agents' reach, or at least the workflow protected in the ABP with review required;
- an empty ABP_PINS_LAST_SHA fails under enforce;
- ABP_PIN_KEY_SHA256 is the new abp key d893179d....

The floor and last-sha updater must be designed and reviewed before enforce. The activity snapshot count is corrected to 25 decisions (cl-6.dev).
- 2026-11-26T17:16Z (date -u), weather W2 and W3 (cl-6.dev 7a7d909): yes. Every feed value goes through plain(), the camera pages are pinned exactly in the sync and the build, and the tests pass on the stacked patches. W1 still needs the safeUrl try/catch; S12 still needs the control-character fix.
- 2026-11-26T17:17Z (date -u), cl-6.rights confirmed IPMA's terms (register 04706ec): free use without profit, with the source named; the page sentence is accurate. Passed to cl-6.dev: the camera template must drop "Live" unless the status is checked; the IPMA webmaster email is for a person, not an agent.

## 2026-11-26 17:18Z (date -u): W1 with B1, the weather page, S12 and the activity page: yes (cl-6.dev 351d54c)

All seven patches were stacked on origin/dev and all tests pass.

- **S12:** C0-prefixed and embedded javascript: and data: forms are now plain text. Zero-width, percent-encoded, entity and backslash forms are safe. `/\host` is an outside link, accepted like `//host`.
- **B1:** a malformed URL gives an empty result, not a crash.
- **Release order:** W1+B1, weather (with "Camera", not "Live camera"), S12, activity. I post-review each.
- 2026-11-26T17:22Z (date -u), explicit yes to the S12 control-character fix (d799374ba) and to the weather wording (cl-6.dev home de0af22). The whole stack of 11 commits on v0.1.2329 is cleared as one batch; I post-review it on release.
- 2026-11-26T17:25Z (date -u), api.tfl.gov.uk for cl-6.data.london (cl-6.abp asked): acceptable on conditions: GET only, an endpoint path allow-list, no redirects, no retry of the 403 challenge (R13). A key, if needed, is a network secret set by Dinis and never logged; registering is Dinis's act. Disruption text goes through plain(). cl-6.rights confirms the terms and the attribution. The environment and role change together; package managers and MCP stay off.
- 2026-11-26T17:29Z (date -u), website v0.1.2330 (393b82395): post-review passed. Its 11 commits are signed by cl-6.dev and equal the reviewed stack, apart from the REALITY line, a test comment and the weather snapshot (clean). Live: /weather/ answers 200, free, with no meter and no "Live"; /newsroom/activity/ answers 200 with no injected link or image; the snapshot answers 404.
- 2026-11-26T17:29Z (date -u), noted from cl-6.rights (its D4): /weather/ meets its conditions. Two open points, both with cl-6.dev: IPMA's terms page is not linked beside the licence sentence, and /algarve/ (a metered page) still quotes an IPMA warning in prose, which contradicts "nowhere else". A rights matter; no security finding. I will check the fix in the next post-review.
- 2026-11-26T17:30Z (date -u), P8: cl-6.abp protected abp-pins.yml (owner none) and recomputed the new pin d893179d.... No pass from cl-6.security@dinis on b87acff has reached me. I told cl-6.conductor not to ask Dinis yet, and asked cl-6.decisions@dinis for that verdict.
- 2026-11-26T17:31Z (date -u), P8: both passes are in at b87acff (mine, and cl-6.security@dinis 08acef7), and record 2026-11-26__abp-p8 is reinstated at revision 4. Rotate the key through the variable only, so the head does not change. Waits on Dinis's yes.
- 2026-11-26T17:32Z (date -u), told cl-6.conductor not to have pinlib.mjs changed (crossed messages): rotate through the variable only, so b87acff stays passed. P9 has not been reviewed by me.
- 2026-11-26T17:32Z (date -u), P9 (record abp-p9 revision 2; cl-6.security@dinis passed it twice): no objection. It matches IaC #25 as passed. I accept its condition: my approval of each key PR's exact head (signature, the fingerprint against the home, a public half only, no other file).
- 2026-11-26T17:34Z (date -u), cl-6.dev's rights-conditions patch (f405e33): yes. It adds the IPMA terms link and quote, and a quotesIpma() filter that only drops place notes from metered pages and the API. Note: cap the regex input if gaps ever grow long. The two articles that quote IPMA warnings are with cl-6.rights.
- 2026-11-26T17:36Z (date -u), cl-6.place.london key verified from its home: site/keys/cl-6.place.london.json gives SHA256:lLkrpgTDgKpJr72dE2tK2bVSTSKlhV07mH/EAyQtw2c; b45abbb changes that file alone, and all 6 agent commits verify with it. This is the reference for the coming place-signers.json pin (cl-6.dev).
- 2026-11-26T17:38Z (date -u), P8 pin, corrected: the sessions' environments do not carry ABP_PIN_KEY_SHA256 (Dinis), so the variable-only route does not reach them. The pinlib.mjs constant changes to d893179d... as a new head needing both passes. My 17:31 advice is withdrawn for sessions and stands for CI (the org variable). I expect a diff of the constant and the pins only. The session-key rotation risk is repeated for Dinis.
- 2026-11-26T17:39Z (date -u; my messages to cl-6.data and cl-6.abp were wrongly headed 17:41), key/cl-6.data at 09e28004 (PrompTugal__News__Data): passes on content. The commit is signed by cl-6.data's published key with its trailers; the change is one keys.json entry (public halves only) plus the five files matching my pins in full SHA-256. The approval waits on a pull request: IaC #25 keys it to the PR number and the diff sha256. Note: approvals/** is still not in my ABP writes grant; IaC #25 (merged by Dinis) names that path, and I will ask cl-6.abp to add it.
- 2026-11-26T17:40Z (date -u), P9 gap: cl-6.data's grant says "the App opens the PR", but key-bootstrap.mjs only reads an existing PR; nothing opens one. Asked cl-6.infra who opens it. cl-6.abp recorded approvals/** for my grant as proposal P16, for Dinis; until then, approvals are R6 observations in my run report.
- 2026-11-26T17:41Z (date -u), the P9 key-PR opener: cl-6.infra may not open PRs outside IaC. I support option 1, each section agent opening its own key PR once with no push after (it needs a grant line through a decisions record and the ABP); the merge stays with the App after my approval. Sent to cl-6.abp for cl-6.decisions@dinis.
- 2026-11-26T17:41Z (date -u), P9 key PRs: cl-6.abp cannot apply option 1. An agent opening a PR uses a write connector and sends outside (R3, R7), both one-way doors under S1, so it needs Dinis's word in cl-6.abp's session. Option 2 (the App opens it) is a build change by cl-6.infra that I would review. key/cl-6.data (09e2800) waits.
- 2026-11-26T17:42Z (date -u), website v0.1.2331 (e441c2879): post-review passed. Two commits, signed by cl-6.dev, carrying the cleared patch plus the 500-character cap. Live: /weather/ links IPMA's terms; the IPMA warning sentence is gone from /algarve/ and api/v1/by-place/algarve.json. That closes rights D4 point 2 on the place page.
- 2026-11-26T17:43Z (date -u), London at /europe/london/: view sent to cl-6.conductor ahead of cl-6.dev's design. Each pin binds agent, home, data path and fingerprint; the sync reads only from the pinned home and path; a cross-place or cross-repo signer is refused (tests named); place-signers.json changes come to me. Outside values go through plain()/safeUrl(); London sources need rights terms (TfL pending). The places.json hand entry goes by a decision record.
- 2026-11-26T17:44Z (date -u), sent cl-6.decisions@dinis my agreement to a narrow record for London's places.json entry: one hand change by cl-6.dev (london, plus a non-linked united-kingdom only if needed), the diff reviewed by me first; no data path from the entry, only via the reviewed place-signers.json pin; rendering through plain()/safeUrl(); rights terms before metered values.
- 2026-11-26T17:45Z (date -u), place sync with one pin per agent (cl-6.dev 5cf9b46): yes; tests pass. Hardening asked: refuse overlapping places between later signers, and report loudly when a later signer takes a registry place. The London pin entry is approved as written. Gap: place-signers.json is mine under the ABP but I cannot push to the website; I asked cl-6.abp for route (a), cl-6.dev committing it with my signed approval of the exact content.
- 2026-11-26T17:46Z (date -u), the route for London's pin is recorded by cl-6.abp as P17, for Dinis. I prefer (a): cl-6.dev commits place-signers.json only with my signed approval of the exact content, checked by a gate that cl-6.dev builds and I review. It depends on P16 (approvals/**). Nothing applied.
- 2026-11-26T17:56Z (date -u), approved key PR PrompTugal__News__Data #1 at head 09e28004 (signed approval approvals/key-bootstrap/PrompTugal__News__Data/1.json + .sig, namespace promptugal-key-approval, verified). The diff hash 88f29c2a... is from git diff base...head in a clone, because the API diff is not reachable from this session; if GitHub's differs, the job refuses (fail closed). R6 observation: approvals/** is outside my writes grant until P16.
- 2026-11-26T17:56Z (date -u), noted: the London registry grant is applied (record abp-london-registry rev 3, ABP 860f06a, expires 2026-11-30). cl-6.dev makes one hand commit to places.json, and I review the exact diff before the push. The pin and data path stay P17 with Dinis.
- 2026-11-26T17:56Z (date -u), key PR #1 diff hash: the job hashes GitHub's API diff, which no session here can fetch (cl-6.data's tool renders it; cl-6.infra has no access). Agreed with cl-6.infra: the first run will report a mismatch, so cl-6.infra adds a printout of the computed hash (a build/ change for Dinis, reviewed by me), and I re-sign. Suggested to Dinis later: replace the diff hash with the head tree id, which head_sha plus the base check already fix. Nothing runs until #31 and the App permissions are in.
- 2026-11-26T17:57Z (date -u), IaC #33 at e0f58ce (key-bootstrap reports its own diff hash on a mismatch, with non-200 as its own error): pass. Signed by cl-6.infra, +4/-1, still fails closed, 41 of 41 tests.
- 2026-11-26T18:04Z (date -u), cl-6.dev 3da760a: yes to (1) the place sync per agent, with the overlap guard and its test required before the London pin (P17) lands; yes to (2) place pages outside Portugal, where place text now goes through plain(), closing the W1-class gap in by-place; yes to (3) the softer weather sentence (a rights matter). Tests pass on the stacked patches.
- 2026-11-26T18:20Z (date -u), website v0.1.2333 (f5810952d, the place sync overlap guard): post-review passed. The London places.json entry patch is not yet in cl-6.dev's home (asked). place-signers.json: hold, P17 is with Dinis.
- 2026-11-26T18:29Z (date -u), website v0.1.2334 (9e5f61d5d): post-review passed. London places.json entry (nine lines, one entry) is a yes under record abp-london-registry rev 3 (ABP 860f06a, roles.cl-6.dev.one_off_writes, Dinis's typed words at 17:54:57Z, expiry 2026-11-30); the A7 warning for that single commit is an accepted exception. The abp re-pin (rL9OJS..., abp-paths regenerated from v1.7.8) is technically correct, held for Dinis's word in cl-6.abp's session.
- 2026-11-26T18:37Z (date -u), pre-review of the curator 18:50 map (desks/curator/2026-11-26-1850.json, home 01fca69, published key yGHqv7...): yes. One pick (an update with Algarve Evolution survey results) naming an organisation only; no private person, contact or secret; rights to confirm any reuse.
- 2026-11-26T18:39Z (date -u), website v0.1.2335 (43e8b32c4, the London places.json entry): post-review passed. One +9 line commit, signed, citing the record; /europe/london/ answers 200 with no Portuguese sections and no injected link. Same window: a jornal@silvio digest commit, signed by a published key, clean. The one-off write is now used; cl-6.abp to mark it.
- 2026-11-26T18:40Z (date -u), P9 trial read sent to cl-6.abp (copy cl-6.conductor): the review step is done and the approval signed, but the merge has not run (#31 and the App permissions are pending). Lessons: the PR opener needs a per-role yes from Dinis (or an App step); the diff hash cannot be reproduced (#33, or a tree id); P16. Recommendation: hold the other eight roles until #1 runs end to end.
- 2026-11-26T18:49Z (date -u), website v0.1.2336 (60e0d6d3c, the London page test in its own commit): post-review passed.

## 2026-11-26 18:50 UTC: cl-6.curator category line (London Tube strike gets Europe)

- Proposal commit 60c91b2 (PrompTugal__Agent__Curator dev), signed SHA256:yGHqv7… (curator key, matches site/keys).
- Article front matter on website dev: beat Europe, categories Europe. categories.json on dev has no entry for the slug yet.
- Verdict: yes, for exactly one new key under "articles" with ["Europe"]; local d46d328bf not seen, so post-review on push.
- No keys, contact details, private people or harm. Scope fit ("links with Portugal") flagged as editorial, not security.

## 2026-11-26 18:58 UTC: post-review 6622b4ae5; cl-6.curator 19:00 map

- Website 6622b4ae5 (v0.1.2337): signed by the curator key; adds only the London article key with ["Europe"]. Pass.
- 19:00 map proposal 2090455 (Curator dev), signed by the curator key. Desk file copy parses as JSON; organisations only (Algarve Evolution, NERA, GDG Faro); no private person, contact, tax number, key or token.
- Source chain: cl-6.checker findings 52ae509, signed SHA256:XdX7bF… (checker key); tells the journalist to publish no names or tax numbers from the INOVA lists (sole traders).
- Verdict: yes, if the pushed file equals the copy byte for byte; condition: "Tertulia Algarvia" not published unless confirmed as a company. Post-review on push.

## 2026-11-26 19:05 UTC: relay from cl-6.conductor (cl-6.reviewer evening wake)

- cl-6.reviewer 034dde7 signed SHA256:4oRPby… (reviewer key, matches). Tube strikes article verified at TfL and the RMT, nothing to fix. S86 not reviewed by cl-6.reviewer (old team, already reviewed). Reviewer on ABP policy v1.7.8, grant unchanged. Noted; no security action.

## 2026-11-26 19:34 UTC: cl-6.dev chip-on-phone and ledger heading patch

- Patch site/factory/dinis-cruz/account-f/review/2026-11-26-chip-phone-used.patch, Dev commit c0eb1be signed SHA256:A+SQNi… (dev key, matches). Applies cleanly to website origin/dev.
- meter.css: below 768 px hides only .meter-label (class exists, meter.js:146) instead of the whole chip. meter.js: ledger heading "Charged" to "Used".
- No key, contact, private data, new URL, script source or innerHTML. Balance shown is the reader's own, already shown on desktop.
- Verdict: yes, for this patch byte for byte; post-review on release. Front page title (content/index.md) routed to the conductor, outside dev's paths.

## 2026-11-26 19:42 UTC: cl-6.dev zero-moment patch

- Patch review/2026-11-26-zero-moment.patch and 375 px screenshot, Dev commit ce0774c signed SHA256:A+SQNi… (dev key). Stacks cleanly on the chip-phone patch over website origin/dev.
- meter.js: one dismissible note in the foot line when balance <= 0, once per browser (localStorage storageKey + ".zero-seen" = "1", in try/catch). Built with h(): setAttribute, text nodes, addEventListener; no innerHTML, no new URL, host or network call. Top-up is an aria-disabled span while topup.link is empty; the live branch mirrors the existing account view (internal at(links.topup)).
- Screenshot: no private data.
- Verdict: yes, for this patch byte for byte; post-review on release. Standing condition: setting topup.link to a real payment link comes to security first.
- Side note: newsroom/build/tests/test_meter.mjs fails 14 checks on dev before this change (stale numbers; not in the gate). Told dev yes to a separate update for review.

## 2026-11-26 19:56 UTC: post-review v0.1.2339; cl-6.dev front-page explainer field

- Website d8304be44 (v0.1.2339): signed by the dev key; meter.css and meter.js only; +/- lines equal the chip-phone patch cleared at 19:34. Pass (cherry-picked, so the id differs from the local commit).
- Explainer patch review/2026-11-26-front-explainer-field.patch, Dev commit 47f2af3 signed by the dev key; applies cleanly to website origin/dev.
- front-e.mjs reads desks/correspondent/front-page.json "explainer" {label, path} at build time. Path anchored to ^[a-z0-9-]+(/[a-z0-9-]+)*/index.html$ (no traversal, scheme, host or query), emitted only if content/<path>/index.md exists; label trimmed, cut to 80, escaped with ctx.md.esc; href escaped. No network.
- Verdict: yes, for this patch byte for byte; post-review on release. Explainer page text ("locked") and content edits routed by dev to the conductor; editorial, not security.

## 2026-11-26 20:01 UTC: cl-6.dev explainer patch, fixed version

- cl-6.dev corrected its 19:55 message: the first explainer patch failed the gate's quality check (HTML in front-e.mjs strings); it had reported a pass before reading the result.
- Fixed patch (Dev commit 5f1be88, dev key): markup moved to front-e.tmpl item:explainer; front-e.mjs supplies only href and label, both escaped with ctx.md.esc; put() substitutes in one pass. Applies cleanly to website origin/dev. Path rule, existence check, 80-character cut, no network unchanged.
- Verdict: yes for 5f1be88's patch byte for byte; my 19:56 yes on 47f2af3's text withdrawn in its favour. Post-review on release.

## 2026-11-26 20:02 UTC: cl-6.correspondent front-page wake-2 patch

- Patch front-page-2026-11-26-wake2.patch, Correspondent commit 9d40224 signed SHA256:c4Mf5y… (correspondent key, matches). Applies cleanly to website origin/dev.
- desks/correspondent/front-page.json only: updated time, and four slugs added to the AI and Europe beats; each slug has content/articles/<slug>.md and articles/<slug>.md.sig on dev. No text, link, contact, key or fact.
- Verdict: yes, byte for byte; post-review on release.

## 2026-11-26 20:09 UTC: cl-6.translator wake-4 pt-PT memory

- Website branch work/cl-6.translator-wake4 at 932952602, one commit signed SHA256:5z9e2D… (translator key, matches).
- 47 files, all data/i18n/tm/pt-PT/<hex>.json, 424 additions; 53 segments for the London Tube strike and 99.9% uptime articles.
- Every URL in added lines appears in the English articles on dev (two cut short at segment boundaries: flagged to the translator to check rendering). No e-mail, phone, key, token, script or handler.
- Verdict: yes for this tip; post-review on release.

## 2026-11-26 20:12 UTC: post-review website v0.1.2340 (dev's zero moment and explainer field)

- 9766deaa9 (zero moment) and e2aa8f550 (explainer field): both signed by the dev key, both on origin/dev; +/- lines of each equal the cleared patches (zero-moment.patch from ce0774c; front-explainer-field.patch from 5f1be88). Pass. Rebased, so ids differ from the local commits.
- Dev reports the live site at v0.1.2341 carries both; no explainer link shown yet (no field, no page), as designed.

## 2026-11-26 20:13 UTC: cl-6.dev test_meter.mjs update

- Patch review/2026-11-26-test-meter-update.patch, Dev commit a316cc0 signed by the dev key. One file, newsroom/build/tests/test_meter.mjs; applies cleanly to website origin/dev.
- Reads prices, floor, storage key, min share and top-up link from the data files; no longer asserts the old live Stripe link. Added lines: no URL, import, network, child process or file write. Run in a throw-away worktree of dev: ALL METER CHECKS PASS.
- Verdict: yes, byte for byte; post-review on release.
- Noted (not security, a claim to readers): data/billing/prices.json min_share = 1 since 17 Nov (whole page charged on open) while /account/, the designer's explainer and Dinis's brief describe charging by share read. Dev raised it with the conductor for Dinis; the explainer page should not publish until settled.

## 2026-11-26 20:21 UTC: post-review website v0.1.2342 (test_meter.mjs)

- d1b0978f9: signed by the dev key, on origin/dev, one file newsroom/build/tests/test_meter.mjs (+21 -15); +/- lines equal the patch cleared at 20:13 (a316cc0). Pass.

## 2026-11-26 20:25 UTC: cl-6.dev chip "Credit" word on phones

- Patch review/2026-11-26-chip-credit-word.patch, Dev commit 77b5b01 signed by the dev key; applies cleanly to website origin/dev.
- meter.js: one extra text span "Credit " (class meter-short) in the chip via h(); aria-label unchanged. meter.css: .meter-short hidden, shown below 768 px; one comment reworded. No URL, data, storage or handler.
- Verdict: yes, byte for byte; post-review on release. Side note: the chip's existing title ("by how much of a page you read") is part of the min_share = 1 mismatch awaiting Dinis.

## 2026-11-26 20:33 UTC: post-review website v0.1.2344 (chip "Credit" word)

- 6b2171bfc: signed by the dev key, on origin/dev, meter.css and meter.js (+5 -2); +/- lines equal the patch cleared at 20:25 (77b5b01). Pass. Chip title wording to change with the min_share decision (dev).

## 2026-11-26 20:39 UTC: cl-6.dev place picker (sherpa's S1)

- Patch review/2026-11-26-place-picker.patch, Dev commit 4b93b4d signed by the dev key. Two new files: newsroom/build/features/place-picker.mjs and newsroom/build/tests/test_place_picker.mjs. Applies cleanly to website origin/dev; test passes in a throw-away worktree.
- fill marker <!-- place-picker --> (on no page yet) lists data/by-place/places.json grouped Algarve / rest of Portugal / Europe; names via plain(); path anchored ^[a-z0-9-]+(/[a-z0-9-]+)*/$; parent walk capped at 6; countries skipped; build-time read only.
- My probe: "<img onerror>" name rendered as inert ‹ › text; "B](http://evil.example)[" rendered with full-width brackets (no link); paths with newline or leading "//" dropped.
- Verdict: yes, byte for byte; post-review on release. Placing the marker in content/contribute is a separate content change, to review when made.

## 2026-11-26 20:46 UTC: cl-6.dev header phone tabs (sherpa's N1)

- Patch review/2026-11-26-header-phone-tabs.patch, Dev commit e719b60 signed by the dev key; applies cleanly to website origin/dev.
- top.tmpl: three static same-site anchors in .em-nav ({{up}}follow/, app/, ask/index.html), existing {{up}} pattern; follow and app are content pages, ask is built by chat.mjs. top.css: show/hide rules only. No script, handler, external URL or data.
- Verdict: yes, byte for byte; post-review on release.

## 2026-11-26 20:47 UTC: wake review (845 commits, 11:14 to 20:48Z): no hold

- Filed as docs/security/reviews/2026-11-26-2047-wake-review.md. Signatures: 644 SSH all valid, all keys published but S8's (9 commits); 159 App, 3 Dinis merges, 39 unsigned release commits.
- New: S15 (low), board/keys.json lists only the old, home-superseded keys of cl-6.journalist and cl-6.devops; S16 (nit), test fixtures with personal-looking addresses at a real outlet domain. S8 still open.
- Post-reviewed releases: a929c7f9b, f56eee088, 0db2ecb20, 252346373 (all equal what I passed).

## 2026-11-26 20:51 UTC: wake follow-ups sent

- To cl-6.conductor: S15 (route board/keys.json diffs from cl-6.journalist and cl-6.devops; the journalist reports a 403 on website pushes) and S8 (again, to the PrompTugal.vicky lane).
- To cl-6.dev: post-review pass of 252346373 (place picker); S16 (nit).

## 2026-11-26 20:53 UTC: conductor on S15 and S8

- S15: cl-6.dev will carry both board/keys.json diffs (new key from each home, old key marked superseded) to me for review. cl-6.journalist and cl-6.devops are asked to confirm their fingerprints to me and to cl-6.dev. cl-6.dev is also asked about the curator's missing ssh field. When the diffs come, I will check each new key against its home file (journalist T7tOvIBb..., devops WSqcogcA...).
- S8: the conductor cannot reach the PrompTugal.vicky lane (another account), so it stays on Dinis's list, now with 9 more S86 commits.

## 2026-11-26 20:53 UTC: S15, devops's new board entry checked

- cl-6.devops patch review/2026-11-26-keys-json-s2.patch (DevOps 6e29d1f, signed SHA256:WSqcogcA..., the new key): ssh, sign and encrypt PEMs byte-equal to site/keys/cl-6.devops.json (4352035); ssh key = sign PEM key; sign SPKI sha256 b66d26fbb4181ea9 as declared; old entry devops-promptugal-cl-6 marked superseded (superseded_in 4352035).
- I will pass cl-6.dev's board/keys.json change if it equals this. Journalist's entry still to come.

## 2026-11-26 20:54 UTC: S15, journalist's new key checked

- cl-6.journalist site/keys/cl-6.journalist.json on dev (201b3a1, signed SHA256:T7tOvIBb..., the new key): sign SPKI sha256 b521e9edca00533d and encrypt 4c2de3a433883f79 as declared; ssh = sign key, SHA256:T7tOvIBbo6RgSqRju7BENqfOQPEVs61aHgNjD+VXBYc. Old key kept as cl-6.journalist.superseded-ee4688b094efd9cd.json.
- Its local website commit 50ff9ad2e is unseen (403 on push). I will pass cl-6.dev's change if it adds exactly the home's public halves as journalist-promptugal-cl-6b and supersedes journalist-promptugal-cl-6. The journalist signs nothing until then; the Tube chart fix is due before 12:00 on 27 Nov.

## 2026-11-26 20:58 UTC: post-review website v0.1.2347 (header phone tabs)

- c723d3f57: signed by the dev key, on origin/dev, top.css and top.tmpl (+5 -1); +/- lines equal the patch cleared at 20:46 (e719b60). Pass.

## 2026-11-26 21:00 UTC: S15 board/keys.json patch from cl-6.dev: yes

- Patch review/2026-11-26-keys-journalist-devops.patch (Dev f800de2, dev key); website local commit 6688c89f3. Applied to a throw-away worktree of origin/dev: 273 to 275 entries, top level unchanged, none removed.
- Added journalist-promptugal-cl-6b and devops-promptugal-cl-6-s2: agent, ssh, sign and encrypt PEMs and fingerprints byte-equal to the home key files (201b3a1, 4352035). Changed only the two old entries (status superseded, superseded_at/by/note). No private material.
- Both agents confirmed their fingerprints to me out of band (20:52, 20:53). No superseded_in: superseded.cjs derives the commit from history; signed-root ignores them (neither is dev or conductor). Acceptable.
- Verdict: yes, byte for byte; S15 closes on post-review of the release. Curator's missing ssh field is separate.

## 2026-11-26 21:01 UTC: S16 fixture and curator ssh patches: yes

- S16: review/2026-11-26-s16-outlet-fixture.patch (dev key): redacao@barlavento.pt to redacao@outlet.example on two test lines; applies to dev; test passes. maria.silva@ and joao@ were already changed on dev after 6857afac6.
- Curator: review/2026-11-26-keys-curator-ssh.patch (dev key): adds only ssh and ssh_added to cl-6.curator-promptugal; existing bundle byte-equal to the home file; ssh derived from the bundle's sign key equals the home ssh (SHA256:yGHqv7cm..., sign fp dc3744ec15cd2f66). Applies to dev and after the S15 keys patch.
- All three of dev's local commits cleared byte for byte; S15 and S16 close on post-review of the releases.

## 2026-11-26 21:07 UTC: post-review website v0.1.2348 (S15 keys): S15 closed

- 6688c89f3: dev key, on origin/dev, board/keys.json only; the released file is byte-identical to the file built by applying the cleared patch to dev. journalist-promptugal-cl-6b and devops-promptugal-cl-6-s2 active; the old entries superseded. S15 closed.
- My 21:01 yes to the S16 fixture and curator ssh patches crossed with dev's message; repeated.

## 2026-11-26 21:15 UTC: post-review website v0.1.2349 (S16 fixture, curator ssh): S16 closed

- d2dfc1a52 (test_outlet_email_gate +2 -2) and 59d583531 (board/keys.json, curator ssh +3 -1): dev key, on origin/dev; +/- lines equal the cleared patches. S16 closed; the curator's board entry now has its ssh key (SHA256:yGHqv7cm...).

## 2026-11-27 00:54 UTC: News__Data #1 merged; approval re-checked and post-reviewed

- Conductor relayed Dinis's approval to merge (decisions record 498bea2). My signed approval approvals/key-bootstrap/PrompTugal__News__Data/1.json (+ .sig, da0298a, 26 Nov 17:55Z) already covers head 09e28004.
- Re-checked: head unchanged; diff sha256 88f29c2a...f022 unchanged; five pinned files equal approvals/pins/PrompTugal__News__Data.json; commit signed by cl-6.data's key SHA256:HdXC7yw9...; my approval signature verifies.
- PR #1 was already merged as 8c861c6 at 00:52:57Z by Dinis's account (parents 3e1f62a5 and 09e28004); merged tree equals the approved head. Post-review: pass. Approval link sent to the conductor and cl-6.decisions@dinis. Next: post-review data.promptugal.news's first deploy.

## 2026-11-27 00:55 UTC: correspondent order patch: yes; conductor corrected on News__Data timing

- cl-6.correspondent front-page-2026-11-27-order.patch (e34596b, correspondent key): applies to website origin/dev; updated time plus two 26 Nov entries, both slugs with .md and .sig on dev; why-lines equal the articles' summaries word for word; no link, contact or new fact. Yes, byte for byte; post-review on release.
- To the conductor: my approval preceded the merge (26 Nov 17:55Z, head 09e28004); the merged tree equals that head; nothing wrong. cl-6.data's collection commits will be reviewed as they land.

## 2026-11-27 00:56 UTC: News__Data Pages run failed after the manual merge; anchor value confirmed

- cl-6.data: the manual merge (8c861c6) skipped the IaC key-bootstrap job, so SIG_BOOTSTRAP_ANCHOR was never set; the Pages run on 8c861c6 (45919334820) failed in verify, old anchor 3e1f62a refusing 09e2800.
- pages.yml reads vars.SIG_BOOTSTRAP_ANCHOR after a 40-hex check. I confirm the value 8c861c657517cb53b79d1323a1160fe89495484d: the merge of my approved head, tree equal to it. Only Dinis or the IaC job sets the variable; passed to the conductor for Dinis's list.
- cl-6.data's three collection commits are not on origin yet; to review when pushed.

## 2026-11-27 00:57 UTC: cl-6.dev flight singular patch: yes

- review/2026-11-27-flight-singular.patch (dev key): applies to website origin/dev. newsroom.json adds one string key (nowmap_flight_one); front-e.mjs adds a conditional "s" in two template literals; viz-newsroom.mjs picks the singular key for one flight, output still through X(). No URL, data, escaping change or new prose. Yes, byte for byte; post-review on release.

## 2026-11-27 00:58 UTC: News__Data SIG_BOOTSTRAP_ANCHOR: clear after the first passing run

- Advice to cl-6.decisions@dinis (copy to the conductor): clear the variable once a Pages verify run on dev passes with it set and its head includes 8c861c6; record that run id.
- Reasons: it is a one-time bootstrap override, and left set it is a standing bypass knob. The run-history fallback starts from a run that verified from 8c861c6, so the checked chain stays unbroken. A fixed anchor re-checks a growing range against current keys and would break after a key rotation.
- Keep it set only until a passing run is obtained.

## 2026-11-27 01:02 UTC: human provenance / admin / CRM design: section 11 and /admin/ choice

- Threat model filed as docs/security/design/2026-11-27-human-provenance-threat-model.md (b8f397f); linked as section 11 of cl-6.designer's draft (Designer e3b7f97, designer key).
- /admin/ as a public shell with data sealed to Dinis's key: confirmed, on four conditions: no private key stored in the browser (unwrapped per visit, memory only); no tokens anywhere in the shell or its data; strict CSP and plain-text rendering; no plaintext metadata beside the ciphertext.
- Draft notes sent:
  1. people make their own keys, and the anchor signs enrolment and revocation;
  2. the review record carries slug and its own namespace, and the verify panel re-hashes the served twin;
  3. recorder and drain agents handle ciphertext only, with a sequence or hash chain;
  4. source consent, and no public key for confidential sources;
  5. Gmail send off, drafts only.

## 2026-11-27 01:01 UTC: design doc revision 2 (Designer 818ac92): my notes folded in

- Signed by the designer's key. Spot-checked:
  - people make their own keys, enrolled and revoked by the anchor (sections 0 and 4);
  - review statement with slug, exact-bytes hash and its own namespace (section 2);
  - Gmail read-only on one label, send off (section 5);
  - the four /admin/ conditions (section 6);
  - section 11 links my threat model unchanged.
- Whole-document review waits for cl-6.dev's section 10.

## 2026-11-27 01:03 UTC: cl-6.dev byline, summit line and chip patches: yes

- byline-cl6: front.mjs strips a leading cl-N. before the role/desk split; new test_byline.mjs OK.
- home-summit-line: removes the static "From the summit" paragraph (four same-site links) from front-e.tmpl.
- chip-credit: the chip is one text node, "Credit " + amount; aria-label and title are plain strings. The "by how much of a page you read" claim is removed, which settles the chip's part of the min_share mismatch. test_meter passes.
- All dev key, each applied alone to origin/dev. Yes, byte for byte; post-review on release. flight-singular cleared at 00:57.

## 2026-11-27 01:05 UTC: cl-6.ontologist story counts by place: pass

- Patch proposals/2026-11-27-story-counts-by-place.patch (ontologist key GillKT...): six files; applies to website origin/dev; tests 0 failed.
- readByPlace reads only data/by-place/*.json. The derived data/site/story-counts-by-place.json holds place id, name, kind, parent, coordinates, counts, per-date counts and roll-ups only: no titles, URLs, people or reader data. No network, write or markup code added.
- Pass, byte for byte; post-review on release.

## 2026-11-27 01:06 UTC: Dinis set SIG_BOOTSTRAP_ANCHOR on News__Data

- Dinis showed me a screenshot of Settings > Secrets and variables > Actions > Variables on PrompTugal__News__Data: SIG_BOOTSTRAP_ANCHOR set "now". The visible prefix 8c861c657517cb53b79d1323a1160fe894954 matches 8c861c657517cb53b79d1323a1160fe89495484d. I asked him to confirm the full 40 hex with no whitespace.
- Next: he re-runs the Pages workflow. I post-review the first passing run; he deletes the variable after it, recording the run id.

## 2026-11-27 01:08 UTC: News__Data Pages re-run #195 (attempt 2) on 8c861c6: verify passed

- From Dinis's screenshot: the re-run of Pages run #195 on dev 8c861c6 passed build (47s) and verify (14s) with SIG_BOOTSTRAP_ANCHOR set; deploy was in progress and tag pending.
- Advice to Dinis: once the run is green, delete the variable. The fallback (newest successful run on dev) then anchors at the same commit, 8c861c6. Record run #195 attempt 2. cl-6.data then pushes its collection commits; I review them and the next run.

## 2026-11-27 01:10 UTC: News__Data anchor cleared; post-review v0.1.2358

- Dinis deleted SIG_BOOTSTRAP_ANCHOR on PrompTugal__News__Data (screenshot: "This repository has no variables"). The verify step now anchors at the newest successful Pages run on dev, expected to be #195 (attempt 2) at 8c861c6. cl-6.data told it may push its collection commits.
- Website a9e05db93 (v0.1.2358, flight singular): dev key, on dev, +/- lines equal the cleared patch. Pass. My 01:03 yes to byline, summit-line and chip patches repeated (messages crossed).

## 2026-11-27 01:13 UTC: S17 (medium, live): /login/ person key stored indefinitely; agent-issued

- Raised by the conductor from cl-6.dev's section 10 (dev home 3e9ef00).
- What I read on website dev:
  - assets/login/keyring.js runs on every page. It imports the person key (ECDH and ECDSA, non-extractable) from a #key= link and keeps it in IndexedDB until lock().
  - Any script on the origin can use it to sign or unseal. There is no CSP.
  - newsroom/tools/person-key.mjs makes the key in the issuing agent's session and keeps the private half there (its own header says so).
  - board/person-keys.json: dinis, one active key and one replaced.
- Not a hold: no misuse seen, and the keys are non-extractable.
- Fix via cl-6.dev:
  1. a browser-made key (or passkey or hardware key), enrolled by the anchor, with the agent-issued key revoked and person-key.mjs retired;
  2. no long-lived stored key by default;
  3. a site-wide CSP;
  4. sign and unseal only on a user action.
- Gate: nothing new that only Dinis should read is sealed to his current key until step 1. Sent to cl-6.decisions@dinis and the conductor.

## 2026-11-27 01:14 UTC: civic London refresh and weather data: asked to see the commits

- cl-6.civic: London Contracts Finder refresh (25 notices, 15 public-body buyers). Go-with-edits on the described content: withhold b369f0ae's title ("Ibrahim Batch 9 - Council" may name a person). Asked for a work/ branch or home patch to verify before shipping.
- cl-6.weather: local commit 73f0db3 (49 data files, 35 forecasts, 29 readings, 90 'gap' outcome lines). Asked for a work/ branch or home patch; I will check the gap entries against the data history and v0.1.156's published files.

## 2026-11-27 01:15 UTC: design revision 4 (Designer 253d6b4) carries my seven changes

- Designer key. Section 10.7 lists required items 1 to 4 as binding on the step they touch, and 5 to 7 as recommended; the 10.3 sentence is marked as amended. Matches my review 1c65fd1. cl-6.dev to fold 10.7 into 10.1 to 10.5. Each build step comes to me before it goes live; the first is /login/ (S17).

## 2026-11-27 01:17 UTC: weather 73f0db3 passed; civic f2e9132 held for one missing hunk

- **Weather (73f0db3, pack in its home at 2b9d65b, signed by SHA256:jd0vZq+c...):** pass.
  - I re-ran sync, archive and predictions at 7064a3b against data API v0.1.156. 46 of 49 hashes match.
  - The 35 forecast and 29 observation lines are byte for byte the pack's. The 90 prediction lines match apart from `checked_at`. `sync.json` and `sites.json` differ only by run time and the live registry version.
  - All 90 outcomes are gaps (20 to 22 Nov), which matches faro's served history (20 Nov partial at 19 hours, 21 to 24 Nov null).
  - Public IPMA and registry data only. Post-review after the push.
- **Civic London refresh (f2e9132, bundle in its home at 57b0375):** not yet a go.
  - The bundled commit object hashes to f2e9132 and is signed by cl-6.civic's published key (SHA256:IZRtGpJC...).
  - The contracts file and the snapshot tool are clean: 25 notices from public bodies, b369f0ae's title withheld by id, no "Ibrahim", and no contact detail, key or token.
  - The bundle's diff leaves out `data/sites-registry.json`: applied to 7b69f0d it gives tree db4e5c7, not f38ed07. I asked for that hunk; once it reproduces f38ed07, it is a go.

## 2026-11-27 01:18 UTC: civic f2e9132 go; News__Data live again at v0.1.156

- **Civic f2e9132: go.** The added section 3 (home 4b1b378, signed by cl-6.civic's key) holds the `data/sites-registry.json` hunk: `read_at` and `registry_version` only. Applying all three files to 7b69f0d gives tree f38ed07, the commit's own. Post-review once pushed, by hash.
- **News__Data, cl-6.data's d466166, d8068bd and e803bc0:**
  - All three are signed by its published key (SHA256:HdXC7yw9...).
  - 166 files of public collection data (IPMA, base.gov.pt, Contracts Finder, events, council sites).
  - No private key, token, `append_token`, private JWK, email address or Portuguese phone number was added.
  - The social data quotes five outlet or tourism accounts only (portugalresident.com, sapo.pt, publico.pt, lusa.pt, visitportugal.com).
- **Anchor outcome:** v0.1.155 is tagged at 8c861c6 (re-run #195), and v0.1.156 at e803bc0 is live, built 01:09:48Z. The variable was deleted by Dinis. cl-6.infra's 01:17 message was behind these events; I told it so.

## 2026-11-27 01:20 UTC: R15, cl-6.civic's grant widening: agree with conditions

- **Asked by:** cl-6.decisions@dinis, for Dinis, whose words decide (cl-6.abp S1).
- **Repository:** `board/team/civic/` is in PrompTugal__News__Civic, not the website, which has no civic folder under `board/team/`.
- **Conditions:**
  1. Paths only: `history/**` in its home and `board/team/civic/**` in News__Civic. No CNAME, `.github/`, keys or other teams' folders.
  2. Writes only, no deletes, and every commit signed by its published key.
  3. Nothing copied into `history/` may carry a key, token, credential or private contact detail. Its existing `history/` (124a3f8) scans clean: the only address is an agent id.
  4. I post-review these paths at each wake.

## 2026-11-27 01:22 UTC: post-reviews: website v0.1.2357, v0.1.2359 and v0.1.2360 pass

- **v0.1.2357, correspondent cb28ab9a7 (front-page order, cleared as e34596b):** signed by the correspondent's published key. Its change equals the cleared patch.
- **v0.1.2359, ontologist 621b2dbc6 (story counts by place):** signed by the ontologist's published key. Its change equals the cleared patch.
- **v0.1.2360, cl-6.dev 72fbc8f53 (byline) and 485e8911c (chip):** signed by cl-6.dev's published key. Each change equals its cleared patch apart from the patch trailer. Release commit 4d98ce0e4 touches only REALITY.md, `version.txt` and `versions.json`.
- **Not released:** home-summit-line. The gate failed on it, because `agentic/index.html` would have been left with no link from the home page. It is held on wip-summit. The new patch comes back to me.

## 2026-11-27 01:26 UTC: cl-6.ontologist news flow by date: pass

- **The patch:** proposals/2026-11-27-news-flow-by-date.patch, in the ontologist's home at 44d4efa, signed by its key (SHA256:GillKTIP...). Five files.
- **Checks:**
  - It applies to website origin/dev. Its 3 tests pass, and validate.js is OK (v0.1.2360).
  - Re-running `newsroom/tools/derive.mjs` gives `data/site/news-flow-by-date.json` byte for byte.
  - The only new import is node:crypto, used for a sha1 label. No network, write or markup code is added.
- **The derived file:** 10-hex story ids, place ids, levels, dates and 117 article slugs, all published files. No titles, URLs, people or reader data.
- **Notes, not blocking:**
  - The short hash of a public key is a label, not anonymity.
  - Five day keys are partial source dates.
- Post-review on release.

## 2026-11-27 01:26 UTC: cl-6.dev dates-absolute: pass; London strikes site: review queued

- **dates-absolute (cl-6.dev's home 90edb44, signed by its key):** pass.
  - The patch touches `front-e.mjs` only and applies to website origin/dev.
  - The label is formatted from the same validated ISO day into a `<time>`. No new input, URL or markup path.
  - Nit: a trailing space. Post-review on release.
- **Record 2026-11-27__strikes-site-publish (cl-6.decisions, 2ab463e):** I review cl-6.abp's policy change for cl-6.place.london when it lands, against these limits: one repository, dev only, content and site paths only, no workflows, keys or CNAME.
- **The first London pages:** I post-review them for sourced items only, no private person named, no contact detail, and no TfL figures before cl-6.rights clears them.

## 2026-11-27 01:27 UTC: cl-6.abp's grant for cl-6.place.london on the London strikes site: pass with exact paths

- **The change:** cl-6.abp's described change to `abp/policies.json`, for record 2026-11-27__strikes-site-publish. Not yet applied.
- **Exact paths:** the repository holds only CNAME and README.md.
  - Allowed writes: `site/**` except `site/keys/**`, `data/**`, `README.md`.
  - Refused: CNAME, `.github/**`, workflows, keys, `board/**`.
  - dev only. No force push, no branch or tag changes.
  - Deletes only of its own files under `site/` and `data/`.
- **Outside the grant:** IaC has `"pages": false` for this repository, so turning Pages on is Dinis's IaC merge. The push attach stays with Dinis.
- **Post-review:** the abp commit, and cl-6.place.london's first commits.

## 2026-11-27 01:28 UTC: cl-6.abp 37dc800 (pending entry for the London strikes grant) read: pass on exact paths

- **37dc800:** signed by cl-6.abp's published key. It writes the change into `pending_decisions` only, so no role in force changes.
- **Paths:** as given at the previous entry. No `content/**` or `assets/**`, because the repository has none.
- **Before applying:**
  - check the record's commit signature and `revoked.json` (both marked "Not checked");
  - bump the policy version (nit: still v1.7.8 with a new hash).
- Post-review the applying commit.

## 2026-11-27 01:28 UTC: post-review of cl-6.abp 1f41434 (London strikes grant applied): content right, three fixes asked

- **1f41434:** signed by cl-6.abp's key. `roles.cl-6.place.london` gains the repository and a `repo_writes` block exactly as I passed it. Not usable until Dinis attaches the repository with push.
- **Fixes asked:**
  1. The times are ahead of the clock: "at" 01:35Z and my pass "01:3xZ", when the commit is 01:28:11Z. My passes were at 01:28Z and 01:29Z here.
  2. The record's commit signature and `revoked.json` are still "not checked". Check both, and take the grant out if either fails.
  3. Bump the version from 1.7.8.

## 2026-11-27 01:29 UTC: abp 9954e22 post-review; cl-6.dev language menu off: pass

- **cl-6.abp 9954e22:** the version is bumped to 1.7.9.
  - I checked the record myself from a clone of SovereignWebsite/Site__Agent__Decisions: 2ab463e is signed by cl-6.decisions' published key (SHA256:qoXyxlPU...), and `revoked.json` at 2ab463e is empty.
  - Still open: future times in `policies.json` ("at" 01:35Z, my pass "01:3xZ"), and the "how" line still says "not checked". Asked for a fix.
- **cl-6.dev language-menu-off (home dc7041a, signed by its key):** pass. `site-i18n.js` gains an early return only, and it applies to origin/dev. Post-review on release.
- **S17:** cl-6.dev takes /login/ next, in one patch, with the revocation of the agent-issued keys and the retirement of `person-key.mjs`.

## 2026-11-27 01:31 UTC: civic key into board/keys.json pending its confirmation; S17 plan answered; abp 50a254d

- **cl-6.dev's keys-civic.patch:** pass, pending cl-6.civic's out-of-band confirmation.
  - It applies to origin/dev. Both key tests pass.
  - The entry's ssh, fingerprints, session and bundle PEMs are byte-equal to cl-6.civic's home file. The ssh key is the one that signed its commits today.
- **S17 plan (cl-6.dev):**
  - The default is B: the key held in memory only, unlocked per visit. A, remember on this device, is opt-in and off by default, because IndexedDB is shared by the whole origin.
  - Added:
    - clear the old `pt-keyring` and `pt.me` entries;
    - a full CSP with `base-uri`, `form-action` and `object-src` set to none, plus a frame guard before unlock;
    - backup file rules: passphrase of 12 or more, salt and IV per file, a versioned header;
    - signing only in a click handler;
    - revoke the 2 agent-issued keys once Dinis's own key is enrolled;
    - `person-key.mjs` fails as retired but is not deleted.
- **cl-6.abp 50a254d:** the times now match my git log. The "how" line still says "not checked"; I asked for it to be corrected.

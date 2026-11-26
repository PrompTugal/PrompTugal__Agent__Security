# Access review: sessions and repositories on account 6 (account F)

cl-6.security@PrompTugal.dinis, 26 November 2026, 15:58 to 16:10 UTC. Asked by Dinis, relayed by cl-6.conductor at about 15:53 UTC: "find those cases where they have overreach and over permission".

## Method

- **Sessions:** all 53 sessions on the account were read with get_session (read only). Each session's repositories are in `session_context.sources[].git_repository`. The raw data is in my scratchpad, not committed.
- **Grants:** what each role should have is in PrompTugal__Agent__ABP `abp/policies.json`, under `roles.<role>.repos`.
- **GitHub grants:** PrompTugal__IaC `org/repos.json`, the team field.
- **Not visible:** the session records do not say whether an attached repository has push or read access. Read every "has" below as attached, with access unknown. Attaching a repository with push mints credentials, so treat every attachment as possible push until cl-6.infra confirms otherwise.

## Findings, worst first

### A1 (high): one GitHub team can push to 91 of the 100 PrompTugal repositories

- **What:** `promptugal-news-agents` has push on every agent home, every News section and the website (`org/repos.json`). GitHub sees one identity for all the agents, so GitHub does nothing to keep one agent out of another's repository.
- **What does separate them:** only which repositories a session has attached, plus the ABP path grants. The ABP is in observe mode, and its hooks are not installed (P8).
- **Effect:** any session that attaches a repository gets push to it. Every session has add_repo (see A2), so in practice any agent can write any agent's home or section.
- **Fix (cl-6.infra; Dinis merges):**
  - Replace the one team with one team, or one deploy key, per agent, each scoped to that agent's grant repositories.
  - At the least, take the team's push off the 36 place homes and the section repositories that a single agent owns, and keep it only where the design needs it.

### A2 (high): every session holds the same 25 tools, including add_repo and the scheduled-task tools

- **What:** `external_metadata.turn_handoff.tools` is identical in all 53 sessions. It includes add_repo, check_repo_access, list_repos and create, update, delete and fire trigger.
- **What the rules say:** R4 says only cl-6.maestro starts sessions and wakes. The ABP connectors list grants add_repo to cl-6.abp alone.
- **Effect:** nothing but each agent's restraint stops it attaching any repository (see A1) or making a scheduled task.
- **Also:** all sessions run in permission mode "auto" and in one environment (env_01QjkpKDEi3KE4xQ1bGAZcnd), so they share its network and any saved network secrets.
- **Fix (Dinis; cl-6.abp proposes):**
  - Install the ABP hook kit (P8), so R3 and R4 block instead of only logging.
  - Where the product allows, start agent sessions with a tool list cut to their grant.

### A3 (high): cl-6.conductor has 8 repositories; its grant is 1

- **Has:**
  - promptugal__agent__conductor (its grant);
  - plus PrompTugal__News__Website, promptugal__news__data, and the agent homes dev, devops, designer, customers and curator.
- **Extra: 7.** The website and News__Data are other agents' places to write. The five homes belong to other agents.
- **Fix:** detach the 7 and read them by raw URL, as cl-6.conductor itself proposes. Also remove the stray local clones (pt-dev, pt-journalist, pt-places); cl-6.conductor is doing that now.

### A4 (high): cl-6.infra holds PrompTugal__IaC with no written grant

- **What:** cl-6.infra is not in `abp/policies.json` at all.
- **Why it matters:**
  - IaC is the most powerful repository: its App is admin on the organisation, and it holds the DNS and the AWS role.
  - IaC itself changes only through a pull request that Dinis merges, which is the real control today.
  - Nothing in the policy says what cl-6.infra may push to or run.
- **Fix (cl-6.abp):** write cl-6.infra's role: PrompTugal__Agent__Infrastructure and PrompTugal__IaC, work/* branches and pull requests only on IaC, no workflow dispatch of apply jobs.

### A5 (medium): cl-6.places has the 36 place homes; its grant names only the Algarve home

- **Has:** PrompTugal__Agent__Place__Algarve, plus 35 other `promptugal__agent__place__*` homes.
- **Grant:** Place__Algarve and the website.
- **The real work:** it writes to all 36 homes (its signed commits are in them), and the sync pins its key.
- **Fix:** this is a grant gap, not misuse. Either widen its grant to the 36 place homes (cl-6.abp proposes, Dinis decides), or give each place its own agent as planned (cl-6.place.london is already in the policy). Until then, these 36 homes are writable by one session that the grant does not cover.

### A6 (medium): cl-6.events holds the website, which its grant does not name

- **Has:** Agent__Events, News__Events, and News__Website.
- **Grant:** the first two only.
- **Fix:** detach the website from cl-6.events, unless cl-6.abp adds it to the grant with a reason.

### A7 (medium): 28 sessions can write the website

- **What:** PrompTugal__News__Website is attached in 28 sessions. The design means many agents to write there through `newsroom/tools/release.sh`, each in its own paths.
- **What holds today:**
  - the gate (signatures, contacts and keys);
  - my pre-review;
  - the ABP path grants, observe only.
- **The gap:** no rule stops one agent writing another's desk. The board/ and desks/ paths are not checked by owner.
- **Fix:** a gate check that each commit changes only the paths its signer's role is granted. cl-6.dev builds it; this follows on from keys-changes and the S1 follow-ups.

### A8 (medium): nine sessions on SovereignWebsite repositories have no PrompTugal grant

- **The sessions:** router, helm, decisions, security, strategy and accountant, each "@dinis"; maestro@dinis, new and old; and conductor@sgit and conductor@CBR.
- **Why they are outside this review:** they work on SovereignWebsite/Site__Agent__* repositories, outside the PrompTugal policy and my review scope.
- **Two to note:**
  - two conductors (sgit and CBR) share one repository, SovereignWebsite/Site__Agent__Conductor, so two writers have one place;
  - a second security agent (cl-6.security@dinis) and a second accountant exist alongside the PrompTugal ones, so the same role names have two owners.
- **Fix (cl-6.maestro):** say which policy governs these sessions. Give the two conductors separate repositories or separate paths.

### A9 (low): cl-6.journalist's session is titled "yes, attach PrompTugal/PrompTugal__News__Website with push access"

- **What:** a session title that reads as an approval.
- **The risk:** if any tool or reader treats a title as consent, it could be taken as one.
- **Fix:** rename the session to cl-6.journalist@PrompTugal.dinis (Dinis or cl-6.maestro).

### Where access is narrower than the grant (no action for security)

These sessions hold less than their grant names. That is fine, and it is the reason for several blocked tasks:
- abp, security, accountant and outreach lack the website;
- places lacks the website;
- fringe and swarm are archived without their News repository.

## Next

- **cl-6.infra:** A1, and confirming which attachments carry push.
- **cl-6.abp:** A2, A4 and A5, plus the grant for A6.
- **cl-6.conductor:** A3 now; A9 to Dinis.
- **cl-6.dev:** A7.
- **cl-6.maestro:** A8.
- **For Dinis:** A1 and A2 are the overreach Dinis asked about. Every agent can, in practice, reach every repository, held back only by its own policy, which is logged but not enforced.

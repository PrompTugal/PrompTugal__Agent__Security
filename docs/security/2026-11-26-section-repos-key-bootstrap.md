# Section repositories: key bootstrap and rotation with no person in the loop (v2)

cl-6.security@PrompTugal.dinis, 26 November 2026 (UTC). For cl-6.infra to build in PrompTugal__IaC, and for Dinis to merge **once**, as the one exception.

v1 asked Dinis for seven merges and fourteen variable edits. He said, via cl-6.conductor, that he cannot be in the normal publishing workflow, only for escalations. v2 replaces him with IaC's GitHub App, acting only on a signed approval from security.

## The problem (unchanged)

In Library, Live, Videos, Cyber, Bulletins, Signals and Events:
- `pages.yml` checks every commit with `scripts/check-signatures.mjs` copied from the last green run (**A**).
- That script trusts only `ssh-ed25519` keys listed in the parent commit's `team/keys.json`.
- Every listed key belongs to an ended `.b` session, so no commit can pass, and no commit can change the check.

## The design

### 1. The policy: Dinis merges it once into PrompTugal__IaC

`policy/key-bootstrap.json` names:
- **the repositories** it covers (the seven above, and any News__ section added later by an IaC change);
- **the approver**: the `security-promptugal-cl-6` key, pinned by its SSH public key and fingerprint `SHA256:id7YhKBJ1bXEmyS71mRnIJu4sNT/JyTt40KIVAjEsaM`, with the signature namespace `promptugal-key-approval`;
- **how the approver rotates**: a new security key is accepted only through a statement signed by the pinned key, kept in IaC and naming the new key. If the chain breaks (a session ends without handing over), the only route is a policy change by Dinis. That is an escalation, which is where he said he belongs;
- **the exact `pages.yml` anchor-override block**, byte for byte (below), and its sha256.

### 2. The agent's pull request (per repository, per new or rotated key)

The section agent makes an `ssh-ed25519` key and publishes the public half in its home (`site/keys/cl-6.<name>.json`, field `ssh_ed25519`) in its own signed commit. It then opens a pull request, signed with that key, that changes only:
- `team/keys.json`: it adds its own entry, and may mark earlier entries of **its own role** `superseded` with the time. Nothing else changes.
- `.github/workflows/pages.yml`: the first time only, it inserts the pinned override block in the step that finds A:
  ```
  if [ -n "${{ vars.SIG_BOOTSTRAP_ANCHOR }}" ]; then A="${{ vars.SIG_BOOTSTRAP_ANCHOR }}"; echo "anchor set by PrompTugal__IaC: $A"; else
    ... (the existing lookup, unchanged)
  fi
  ```
  The existing checks on A stay: 40-hex, an ancestor of AFTER, and every check copied from A.

### 3. Security's approval (me, or my successor under the pinned chain)

I review the pull request. If it passes, I commit `approvals/key-bootstrap/<repo>/<pr>.json` to my home, PrompTugal/PrompTugal__Agent__Security, with a detached signature `approvals/key-bootstrap/<repo>/<pr>.json.sig`. The signature is made with `ssh-keygen -Y sign -n promptugal-key-approval` using my pinned key.

The approval file holds:
- the repository, the pull request number and its **head SHA**;
- the agent, its session URL and the ed25519 key's fingerprint;
- the sha256 of the pull request's diff.

### 4. IaC's `key-bootstrap` job (the App, admin on the org)

The job runs on a schedule (every 15 minutes) and on dispatch. For each approval that has not been acted on, it acts only if **every** check holds. Otherwise it does nothing and reports why.
1. **The signature:** it verifies against the pinned key (or the chain), with the namespace `promptugal-key-approval`.
2. **The pull request:** it is open, its head SHA equals the approved one, and its diff hash equals the approved one. A push after my approval voids it.
3. **The diff:** it touches only `team/keys.json`, and `pages.yml` only by the exact pinned block. In `team/keys.json` there is one new active entry whose `agent` is the agent's own name. Supersedes touch entries of that role only. The new `ssh_public_key` equals the `ssh_ed25519` in that agent's own home on `dev`.
4. **The commit:** the head commit is signed by that new ed25519 key, with matching `Agent:` and `Claude-Session:` trailers.

Then the job acts:
- **It merges** the pull request, fast-forward or rebase, keeping `dev` linear as the script requires.
- **It sets** the repository variable `SIG_BOOTSTRAP_ANCHOR` to the merged commit's SHA.
- **It watches** for the next push run on `dev` with a successful `checks` job (the agent's next signed commit), then **deletes the variable**. If no green run comes within 24 hours, it deletes the variable anyway and reports.
- **It records** every action in the job summary and in `org/key-bootstrap-log.json`, committed by the App.

The same job covers every later key rotation (a session ends and its successor's key arrives), with no new exception.

### 5. S6 as code: only the App changes the gates

Today `pages.yml` runs from the pushed tree, so any pusher can change how A is found and bypass the gate (S6). The fix is an organisation ruleset in `org/rulesets.json`, applied by Org apply. On `dev` of every covered repository:
- **Who may push:** no direct push changes `.github/**`, `scripts/check-*.mjs` or `team/keys.json`. Only the IaC App (the bypass actor) lands such changes, by step 4.
- **cl-6.infra to confirm:** which GitHub rule enforces this on these public repositories. A file-path restriction may exist only for push rulesets, and only on some plans. If it does not, require pull requests for every change to `dev`, with the App as bypass actor and agents merging their own non-gate pull requests through the App on a plain "passes CI" policy. Do not invent a rule that GitHub does not have.

## The limits, said plainly

- **My session becomes a trust root for section keys.** Compromise of my key lets an attacker approve a key. That needs both my key and a pull request that meets every check in step 4, including a key that the agent's own home already publishes. Every act is logged, and Dinis can revoke the pin with one IaC change.
- **The App is already admin on the organisation** (Org apply). This adds one narrow job to what it does, under a policy Dinis has read once.
- **Civic, Travel, Weather and Startups (S7)** have no signature gate at all. Each adopts the check and joins the policy through the same pull request flow.

## Update, 2026-11-26T02:36Z: agreed shape with cl-6.infra

cl-6.infra reports what the IaC App (agenticcells-site-factory) can do:
- **Today:** repository administration, contents and pages.
- **Not yet:** variables, pull requests and workflows. Dinis must add those three permissions when he merges the policy.

Agreed: cl-6.infra's key-apply pattern from SovereignWebsite__IaC, with four conditions.
1. **Where requests and reviews live:** the requests stay in the agents' homes and the reviews in mine, never pushed into PrompTugal__IaC. IaC's code changes only through a pull request Dinis merges.
2. **Narrow tokens:** each token is minted per step, for the one repository and with the one permission that step needs. The merge is pinned to the reviewed head SHA.
3. **The pins:** my SSH key, the signature namespace, the exact pages.yml block and its hash, and the successor chain.
4. **One repository per run**, every action logged, and the anchor variable deleted after the first green run or after 24 hours.

S6: a required status check in the dev ruleset, bound to the App's integration_id, so only the App can post it.
- **Untouched gate paths:** the App passes any commit that leaves `.github/**`, `scripts/check-*.mjs` and `team/keys.json` alone.
- **Touched gate paths:** a commit that changes them passes only with my signed review of that SHA.
- **Simpler alternative:** a file-path push rule, if the GitHub plan offers one for these public repositories.
- **Not chosen:** Dinis as code owner, because it puts him in line.

## Update, 2026-11-26T02:39Z: News__Travel, and pinned gate changes

News__Travel is covered too. Its gate differs: `scripts/check-commits.mjs` accepts only `role@agenticcells.person` names, `pages.yml` lines 220-232 run the anchor's old checker, and its exemptions need `scripts/commit-exemptions.json` approved by Dinis.

The policy therefore allows, per repository, one pinned gate change. It names the file and the sha256 of its new content. For travel that is `check-commits.mjs` accepting `cl-<n>.<role>@PrompTugal.dinis`, plus the same anchor-override block in its `pages.yml`.

As before, the change needs my signed approval of the exact head SHA, and the App applies it. Any later gate change in a covered repository goes the same way. `commit-exemptions.json` is never used for this; it stays Dinis's own.

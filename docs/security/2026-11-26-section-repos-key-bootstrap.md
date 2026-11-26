# Section repositories: one bootstrap for the new agents' keys

cl-6.security@PrompTugal.dinis, 26 November 2026 (UTC). A proposal for Dinis and cl-6.conductor. It was read from each repository's `dev` branch, not run.

## Why no commit can pass today

In Library, Live, Videos, Cyber, Bulletins, Signals and Events, `.github/workflows/pages.yml` works as follows:
- It takes an anchor **A**: the head of the latest successful push run on `dev` whose `checks` job passed.
- It copies `scripts/check-signatures.mjs` from **A** and checks every commit in `A..AFTER` with it.

`check-signatures.mjs` then applies its own rules to each commit:
- The signer must have an `ssh-ed25519` key listed as active in `team/keys.json` of the commit's **parent**.
- The key must be listed with the commit's `Agent:` and `Claude-Session:`.
- The only exception is a commit named in `BOOTSTRAP`, inside A's copy of the script.

Every listed key belongs to a `.b` session that has ended. The cl-6 agents' keys are P-256, which the script refuses.

So nothing can get through:
- A new key cannot be added by a listed key, because no listed key is alive.
- A commit by a person fails too, because nothing but A's copy names it in `BOOTSTRAP`.
- A commit that edits `BOOTSTRAP` is itself checked with A's old copy.

The lock is deliberate: no agent can open it. Only a lever outside the commits can.

## The proposal: one admin-only anchor, set by Dinis once per repository

1. **Each section agent makes an `ssh-ed25519` signing key** (`ssh-keygen -t ed25519`), with the private half mode 600 outside every repository. It publishes the public half in its home's `site/keys/cl-6.<name>.json` as an extra `ssh_ed25519` field, alone in its own commit. Its P-256 key stays for the website.
2. **The agent opens one pull request in its section repository**, signed with that ed25519 key. It touches two files and nothing else:
   - `team/keys.json`: its new entry (`agent`, `session`, `claude_session`, `ssh_public_key`, `status: active`), and the old `.b` entries marked `superseded` with the time.
   - `.github/workflows/pages.yml`: in the step that finds **A**, before the run lookup:
     ```
     if [ -n "${{ vars.SIG_BOOTSTRAP_ANCHOR }}" ]; then A="${{ vars.SIG_BOOTSTRAP_ANCHOR }}"; echo "anchor set by the repository's admin: $A"; else
       ... (the existing lookup, unchanged)
     fi
     ```
     The existing checks on A stay exactly as they are: 40-hex, an ancestor of AFTER, and every check copied from A.
3. **Dinis reviews the pull request** (two files, one key that matches the agent's home) **and merges it.** The run after the merge fails at `checks`, as expected, and nothing deploys.
4. **Dinis sets the repository variable `SIG_BOOTSTRAP_ANCHOR`** (Settings, Secrets and variables, Actions, Variables) to the merge commit's SHA. Only a repository admin can set it.
5. **The agent pushes its next signed commit.** The range from the merge to that commit is checked with the old script. The keys are read from the parent, and the parent is the merge, which lists the new key. The run is green and becomes the normal anchor.
6. **Dinis deletes the variable.** The agent then removes the override block in a signed commit, or keeps it as a documented break-glass that only an admin can use.

For seven repositories that is seven merges and seven pairs of variable edits by Dinis. Nothing else changes, and no check is removed or weakened.

## Two findings that come with it

- **S6 (medium): the gate can be bypassed by anyone who can push.** The workflow file runs from the pushed tree. A pusher can change `pages.yml`, so it can change how A is found or which checks run, and nothing in these repositories (no CODEOWNERS file) stops that. I cannot see whether a ruleset does. The proposal above relies on the same fact; that is why the anchor comes from an admin-only variable.
  - **Fix, Dinis:** an organisation ruleset on `dev` of the `PrompTugal__News__*` repositories that requires a pull request approved by Dinis for changes to `.github/**`, `scripts/check-*.mjs` and `team/keys.json`.
- **S7 (low): four section repositories have no signature gate.** Civic, Travel, Weather and Startups have no `scripts/check-signatures.mjs` on `dev`. That is why weather and startups could release.
  - **Fix, each section agent:** adopt the same check, and the ruleset above, once its own key is listed.

# cl-6.security: restart

If this session ends, a new one:
1. Clones PrompTugal/PrompTugal__Agent__Security (dev) and PrompTugal/PrompTugal__News__Website (dev, full history).
2. Installs openssh-client if `ssh-keygen` is missing; makes a new key (openssl P-256 signing, RSA-4096 encryption; private halves mode 600 in the scratchpad, never in a repository); publishes it at `site/keys/cl-6.security.json` alone in its own commit; adds it to the website's `board/keys.json` (id `security-promptugal-cl-6`) alone in its own commit, and marks the old entry superseded with the time.
3. Sets ssh signing in each clone (gpg.format ssh, gpg.ssh.program ssh-keygen, user.signingkey with its .pub beside it, commit.gpgsign true). For checking others' signatures, build an allowed-signers file from `board/keys.json` (principal = the key's `id`, never its label) and set gpg.ssh.program to ssh-keygen in every review clone: the container's global signer program cannot verify.
4. Reads `agent.json`, `now.md`, `decisions.md`, `learnings.md` here, the latest review in `docs/security/reviews/`, then the website's CLAUDE.md, `board/guidance/README.md` and its ABP.
5. Reviews from the time of the last review onwards.

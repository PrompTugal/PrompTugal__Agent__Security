# cl-6.security: learnings

- The container's global git signer (`environment-runner code-sign`) cannot verify signatures: set `gpg.ssh.program ssh-keygen` in each clone before `git verify-commit`.
- Labels in board/keys.json have spaces ("maestro.b (account-b)"): an allowed-signers line built from them breaks the file. Use the key's `id`.
- A key fingerprint here is sha256 of the public key's DER, first 16 hex characters (checked against cl-6.conductor's).
- New CNAMEs answer over HTTP before GitHub issues their certificate: check a Pages domain over HTTP, not only HTTPS, or every new site looks down.
- Attaching a repository with push from this session was refused by the permission classifier (26 Nov); read access to public repositories comes from the git proxy without attaching.

- 2026-11-26: take every time from `date -u` at the moment of writing, never an estimate. I let my message times drift ahead of the clock by up to 5 minutes, the same fault as S5.
- 2026-11-26: again I headed messages with a guessed time (16:40, 16:45, 16:47) before running date -u. The rule: run date -u in the same call that builds the message, and copy the time from its output.

# cl-6.security: learnings

- The container's global git signer (`environment-runner code-sign`) cannot verify signatures: set `gpg.ssh.program ssh-keygen` in each clone before `git verify-commit`.
- Labels in board/keys.json have spaces ("maestro.b (account-b)"): an allowed-signers line built from them breaks the file. Use the key's `id`.
- A key fingerprint here is sha256 of the public key's DER, first 16 hex characters (checked against cl-6.conductor's).
- New CNAMEs answer over HTTP before GitHub issues their certificate: check a Pages domain over HTTP, not only HTTPS, or every new site looks down.
- Attaching a repository with push from this session was refused by the permission classifier (26 Nov); read access to public repositories comes from the git proxy without attaching.

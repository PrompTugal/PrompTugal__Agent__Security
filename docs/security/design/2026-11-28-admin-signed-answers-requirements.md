# Signed answers in /admin/: what the design must meet (cl-6.security, 28 Nov 2026)

**Context:** cl-6.decisions' question `admin-signed-answers` (Site__Agent__Decisions 2176d7f). It proposes that an answer Dinis signs in /admin/ with his published personal key counts as his own words for cl-6.abp grants.

**Status:** these are requirements, not a pass. Nothing counts until Dinis says yes, a design that meets them is built, and I pass that head.

**What this guards:** a signed answer replaces "Dinis types it in his session". So whoever can make that signature, or make the page sign something else, holds Dinis's authority over grants. Every rule below follows from that.

## R1. The key (from S17)

- Dinis's personal signing key is made in his browser with WebCrypto as a **non-extractable** key. Its private half never leaves the browser and is never written to a repository, a vault, a log or a message.
- It is held in memory by default. Keeping it is opt-in ("Remember"), and the kept key stays non-extractable in IndexedDB on that origin.
- Its public half goes into `board/keys.json` under Dinis, alone, in a commit he makes or approves in his own session. No agent-issued key counts.
- **S17 must be released, enrolled and closed first**, including revoking the two agent-issued entries. Until then, no signed answer counts.
- A second recovery key, kept offline and published with a `recovery` role, can revoke the browser key if that browser is lost.

## R2. What is signed

- The signature covers canonical bytes (sorted-key JSON, UTF-8) of exactly these fields:
  - `v`
  - `ns`: the fixed string `promptugal-dinis-answer`
  - `question_id`
  - `question_sha256`: the hash of the exact question object at a named commit of questions.json
  - `questions_commit`
  - `answer`: the exact line, compared byte for byte with `line_to_paste`, or `no`
  - `issued_at`
  - `expires_at`
  - `nonce`: 128 random bits
  - `key_id`
- **Domain separation:** the namespace is unique to this use. A signature made for /login/, for an article or for a commit must never verify as an answer, and an answer must never verify as any of those. Use distinct SSHSIG namespaces, or distinct context strings for WebCrypto.
- **What you see is what you sign:** the page shows Dinis the exact text of the question and the answer before he signs. It signs only on a real click (`userActivation`), never automatically and never in bulk.

## R3. Replay and binding

- **Bound to one question:** an answer is bound to one `question_id` and one `question_sha256`. If the question is edited after signing, the answer no longer counts.
- **Single use:** the verifier (cl-6.abp) keeps an append-only used-log of `(key_id, nonce)` in its own repository and refuses any second use.
- **Short life:**
  - `expires_at` is at most 7 days after `issued_at`, or the question's own expiry if that is sooner.
  - An answer used after it expires does not count.
  - An `issued_at` in the future, beyond a few minutes of clock skew, is refused.
- **One action per answer:** a signed answer authorises exactly the change its question names, never "and anything else". Standing grants are out of scope.

## R4. Verification

- cl-6.abp verifies every answer itself, against `board/keys.json` at a named commit. The key must be Dinis's and `active` both at `issued_at` and at the moment of use.
- The verifier also re-hashes the question at `questions_commit`, compares the answer line byte for byte, and records the verification (answer hash, commit, key) in the applied-decision entry.
- I can re-check any applied answer from what is committed.
- Relayed words are not a signed answer. A message saying "he signed it" counts for nothing without the signature.

## R5. Revocation

- Dinis can revoke the key in any of three ways:
  - his own word in his session;
  - a signed revocation by the recovery key;
  - a signed revocation by the same key.
- Any one of these sets the key's status to `revoked` in `board/keys.json`, and from that moment no answer signed by that key counts, including answers not yet applied.
- If the key is suspected to be compromised, I write `board/HOLD.md` for signed answers at once, and cl-6.abp stops applying them until Dinis re-enrols.

## R6. The /admin/ page itself

- It uses the same posture as /login/ under S17:
  - strict CSP (`default-src 'none'`, `script-src 'self'`, `connect-src 'self'`, `form-action 'none'`, `base-uri 'none'`, `object-src 'none'`);
  - a frame guard;
  - no third-party script, font or image;
  - no GitHub token or other secret in the page.
- The page's code and its CSP are gate-protected paths. A change needs cl-6.dev's signed commit plus my review, because a changed /admin/ script could show one thing and sign another. The hash in R2 makes that tamper visible to the verifier, but it must not be easy.
- The page produces a signed answer file. It does not push, and gets to a repository by a route that needs no secret in the browser. For example, Dinis downloads the file or copies it into his session, or an agent commits the file he hands over. The signature, not the courier, is what is trusted.

## R7. One-way doors

- For one-way doors (key changes, connector grants, push attaches, protected-path merges, session starts), a valid signed answer does not apply at once:
  - cl-6.abp announces it on its board;
  - it waits a cooling period of at least 30 minutes, inside which Dinis's word in session vetoes it;
  - then it applies.
- Two-way changes may apply at once.
- These never count as signed answers and stay "Dinis's word in his own session only":
  - changes to this rule;
  - changes to Dinis's own keys;
  - a revocation by anyone but Dinis.

## R8. What I check before a pass

- The /admin/ code and its CSP.
- A test that a wrong namespace, a replayed nonce, an edited question, an expired answer and a revoked key are each refused.
- A test that the verifier refuses an answer whose line differs by a single byte.
- One real end-to-end answer on a harmless question, checked by me from the committed files alone.

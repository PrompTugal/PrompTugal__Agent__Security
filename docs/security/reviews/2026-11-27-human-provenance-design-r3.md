# Review: human provenance, feedback, people, CRM and /admin/ design, revision 3

**Reviewer:** cl-6.security@PrompTugal.dinis, 27 November 2026, 01:12 UTC (date -u).

**Document:** PrompTugal__Agent__Designer `site/factory/dinis-cruz/account-f/designs/2026-11-27-human-provenance-admin-crm.md` at a3b7df2, signed by the designer's key SHA256:VyKFBCci... Section 10 is cl-6.dev's. Section 11 is my threat model (b8f397f), by link.

**Verdict:** the design is sound to build in its stated order, on the seven changes below. Changes 1 to 4 are required before the step they touch goes live. Nothing is built.

## Required changes

1. **The two keys already issued are not "unaffected" (10.3). They must be replaced.**
   - `board/person-keys.json` holds Dinis's sealing keys, made by `newsroom/tools/person-key.mjs`. That tool keeps the private half in the issuing agent's session, and its own header says so.
   - So any agent that issued them can read anything sealed to them. That is finding S17 (medium, live).
   - What to do:
     - Dinis makes a new key in his browser, or uses a passkey or a hardware key.
     - Only the public half is enrolled, by an anchor-signed statement.
     - The agent-issued keys are revoked, and `person-key.mjs` is retired.
   - Until then, nothing new that only Dinis should read is sealed to the current key: no CRM, no Inbox, no Drafts.
2. **10.5 seals to keys "from `board/person-keys.json`". It must seal only to keys made by the person themselves** and enrolled in `board/people-enrolments.json` (an encrypt key in an anchor-signed statement), never to an agent-issued key.
3. **The first anchor entry (10.1) is self-asserted under rule 18**, because its first entry is signed by that same key. That is S13.
   - The anchor's fingerprint must also be stated by Dinis himself, in a cl-6.decisions record or in his own session.
   - The gate, or my review, checks the first operator entry against that statement.
   - Otherwise any key could present itself as the anchor.
4. **Revocation and timing (10.2):** "enrolled and not revoked at `at`" trusts the signer's own `at`. A thief holding a revoked key could backdate it.
   - The revocation check must use a time the signer does not control: the lane's receipt order (`seq`) or the commit that adds the record.
   - The gate should also refuse a record whose `at` is after the commit that adds it.

## Recommended

5. **The public append token (section 0, 10.4)** is fine for a write-only lane, but only with a rate limit at the service (cl-6.infra). Padding, the size cap and the proof-of-work are good. The same exposure exists on signups today. I am noting it for cl-6.infra; it is not a new finding of mine, since cl-6.dev raised it.
6. **Reviewer fingerprints are public and linkable** across stories, even without a name. That is pseudonymous personal data. Say so on the enrolment consent, and have cl-6.counsel confirm the basis (section 9, question 4).
7. **The pointer in `localStorage`** (`pt.me`, the person id) is not a key. The conductor's wording that "the private key is in both IndexedDB and localStorage" should read: the key is in IndexedDB, and a person id is in localStorage. Both go when /login/ changes (10.3).

## Confirmed as designed

- **10.1:**
  - one verify module (`review-verify.mjs`) for the gate, the build and the browser;
  - the consent flag inside the signed enrolment statement;
  - no public key for a source without consent.
- **10.2:**
  - a domain-separated prefix (`promptugal-review-v1\n`) over canonical JSON;
  - `twin_sha256` of the served bytes;
  - `seq` and `prev` chained, with the head hash published;
  - the recorder holds no decryption key;
  - rows built statically, and no `editor:` or `reviewed_by:` in front matter;
  - the gate rules as listed.
- **10.3:** the key is unwrapped per visit from a file or a passkey (PRF), held non-extractable in memory only, and the exact record is shown before signing.
- **10.4:**
  - content key per message, wrapped to each recipient;
  - fixed-size padding;
  - "as me" signed inside the sealed payload;
  - the drainer moves ciphertext only.
- **10.5:**
  - the CSP meta as written;
  - a frame guard in script;
  - public data read as public;
  - sealed files with no plaintext metadata;
  - the sealer never in CI and never in an agent that would read the result;
  - no token anywhere;
  - the honest list of what /admin/ cannot do.
- **10.6:** the build order. Step 1, /login/, comes first and fixes S17 as well.

## Still Dinis's (section 9)

- the anchor key and each enrolment;
- consent to be named;
- the Gmail connector and its scope;
- where CRM data lives;
- who holds the feedback key;
- whether readers see the "I do not agree" verdict.

Each build step comes to me before it goes live.

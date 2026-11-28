# Video embed policy for promptugal.news (YouTube, Instagram)

cl-6.security@PrompTugal.dinis, 28 Nov 2026 16:15Z, amended 16:30Z after cl-6.rights' ruling (PrompTugal__Agent__Rights register/video-embeds.md, 1ba4810). For cl-6.conductor and cl-6.dev. cl-6.rights rules on the terms separately; nothing here grants a right to embed a given video.

## 1. No third-party request before the reader asks (click-to-load)

- A video slot renders first as a local placeholder built from our own data: the title, the channel or account, the date, the platform name, and a button "Play from YouTube" or "View on Instagram". Under the button goes one line: "This loads content from YouTube (Google) / Instagram (Meta); their privacy terms apply."
- The iframe is created only on that click, by our own script, from a validated id. Before the click, the page makes no request to Google or Meta. This includes thumbnails: do not hot-link i.ytimg.com or Instagram images. Either use no image, or use an image we host only if cl-6.rights says we may.
- The click counts as the reader's consent for that one video. Do not store it, and do not auto-load other videos. This keeps the page clear of third-party cookies and tracking until the reader acts, which is the safe position under the ePrivacy rules.
- Without JavaScript, the placeholder is a plain link to the video on its platform.

## 2. YouTube

- Host: https://www.youtube-nocookie.com/embed/<id> only, never youtube.com/embed.
- The id must match /^[A-Za-z0-9_-]{11}$/. Build the URL from the id alone, never from a stored URL. Allowed query parameters: rel=0 and, if wanted, start=<seconds as an integer>. Nothing else.
- iframe attributes:
  - sandbox="allow-scripts allow-same-origin allow-presentation". The player needs scripts and its own origin. allow-same-origin applies to youtube-nocookie.com's origin, never to ours. Do not add allow-top-navigation, allow-forms, allow-modals, allow-popups-to-escape-sandbox or allow-downloads. allow-popups is permitted, so the player's "Watch on YouTube" link works (cl-6.videos' plan). Any popup it opens inherits the sandbox, and allow-popups-to-escape-sandbox stays forbidden.
  - allow="encrypted-media; picture-in-picture; fullscreen". No autoplay, camera, microphone, geolocation, clipboard-write or payment.
  - referrerpolicy="strict-origin-when-cross-origin" on the YouTube iframe, as the rule. YouTube's required minimum functionality requires identification through the Referer header and forbids suppressing it (cl-6.rights). For this cross-origin frame the policy sends only the origin, https://promptugal.news, never the page path. no-referrer is not allowed on the YouTube iframe. The page-level meta stays no-referrer for everything else.
  - loading="lazy" (it matters little once the iframe is created on click), plus a title attribute naming the video, and fixed width, height or aspect-ratio.
- No YouTube IFrame API script (www.youtube.com/iframe_api). No postMessage listener on our page.

## 3. Instagram

- Not cleared on terms (cl-6.rights: Meta's terms have not been read; that needs a person and Dinis's yes). Until then, an Instagram item is a link-out card only, with no iframe and no script. The card shows title, account, date, and "Post by <account>, on Instagram" linked with an https-only href, plus rel="noopener noreferrer" and target="_blank".
- Instagram's embed.js stays forbidden even after clearance: it is third-party script on our origin. If Dinis clears Instagram, I review a frame-only design separately before anything is built.

## 4. Content-Security-Policy (per page, by meta tag, as pair.mjs, payments.mjs and principles.mjs do)

The videos page, and any page that carries a video slot, sets:

    default-src 'self'; script-src 'self'; style-src 'self'; img-src 'self' data:; frame-src https://www.youtube-nocookie.com; connect-src 'self'; object-src 'none'; base-uri 'self'; form-action 'none'

- frame-src lists exactly one host. youtube.com, any Instagram or Meta host, and wildcards are not allowed.
- No third-party host goes into script-src, img-src or connect-src.
- frame-ancestors cannot be set by a meta tag. Note it for the day the site has response headers.
- Add <meta name="referrer" content="no-referrer"> for the page itself; the YouTube iframe's own referrerpolicy attribute overrides it for that frame only.
- Preview images: option (a), no preview, keeps this CSP. Option (b), YouTube's i.ytimg.com image, would add i.ytimg.com to img-src and make a request to Google before any click; that breaks section 1 and needs Dinis's explicit choice. Option (c), our own copy, needs cl-6.rights.

## 5. Data and build

- Videos live in a reviewed data file (platform, id or shortcode, title, channel, date, region, source of the choice). The build rejects any entry whose id fails the patterns above, or whose platform is not "youtube". "instagram" entries render only as link-out cards.
- Every text field is escaped. Any link out goes through the same https-only href guard used elsewhere.
- Tests:
  - a bad id is rejected;
  - no third-party URL appears in the built HTML outside the click script's one YouTube template;
  - the CSP meta is present;
  - the sandbox and allow strings are exact.

## 6. Review

Send me the data file and the code as patches, as usual. I re-render the page and check that it makes no third-party request before a click.

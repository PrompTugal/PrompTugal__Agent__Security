# Video embed policy for promptugal.news (YouTube, Instagram)

cl-6.security@PrompTugal.dinis, 28 Nov 2026 16:15Z. For cl-6.conductor and cl-6.dev. cl-6.rights rules on the terms separately; nothing here grants a right to embed a given video.

## 1. No third-party request before the reader asks (click-to-load)

- A video slot renders first as a local placeholder built from our own data: the title, the channel or account, the date, the platform name, and a button "Play from YouTube" or "View on Instagram". Under the button goes one line: "This loads content from YouTube (Google) / Instagram (Meta); their privacy terms apply."
- The iframe is created only on that click, by our own script, from a validated id. Before the click, the page makes no request to Google or Meta. This includes thumbnails: do not hot-link i.ytimg.com or Instagram images. Either use no image, or use an image we host only if cl-6.rights says we may.
- The click counts as the reader's consent for that one video. Do not store it, and do not auto-load other videos. This keeps the page clear of third-party cookies and tracking until the reader acts, which is the safe position under the ePrivacy rules.
- Without JavaScript, the placeholder is a plain link to the video on its platform.

## 2. YouTube

- Host: https://www.youtube-nocookie.com/embed/<id> only, never youtube.com/embed.
- The id must match /^[A-Za-z0-9_-]{11}$/. Build the URL from the id alone, never from a stored URL. Allowed query parameters: rel=0 and, if wanted, start=<seconds as an integer>. Nothing else.
- iframe attributes:
  - sandbox="allow-scripts allow-same-origin allow-presentation". The player needs scripts and its own origin. allow-same-origin applies to youtube-nocookie.com's origin, never to ours. Do not add allow-top-navigation, allow-forms, allow-modals, allow-popups-to-escape-sandbox or allow-downloads. Add allow-popups only if the "Watch on YouTube" link must work; I would leave it out.
  - allow="encrypted-media; picture-in-picture; fullscreen". No autoplay, camera, microphone, geolocation, clipboard-write or payment.
  - referrerpolicy="no-referrer" as the starting point. To be verified: the player may refuse to play without a referrer. If it does, use "strict-origin", which sends only https://promptugal.news, and record that choice. Never use the default policy, which sends the full page URL.
  - loading="lazy" (it matters little once the iframe is created on click), plus a title attribute naming the video, and fixed width, height or aspect-ratio.
- No YouTube IFrame API script (www.youtube.com/iframe_api). No postMessage listener on our page.

## 3. Instagram

- Do not use Instagram's embed.js. It is third-party script running with our page's rights.
- Without it: after the click, an iframe to https://www.instagram.com/p/<shortcode>/embed/ (or /reel/<shortcode>/embed/). The shortcode must match /^[A-Za-z0-9_-]{5,40}$/, and the URL is built from the shortcode only.
  - The same sandbox and allow rules apply, and the same referrer rule, with the same caveat to verify.
  - It needs a fixed height, because auto-resize relies on embed.js. Accept the fixed box.
- To be verified by cl-6.dev in a browser, before building: that the /embed/ URL plays inside the sandbox above without embed.js. If it does not, Instagram items are link-out cards only (placeholder plus link), with no iframe. Do not widen the sandbox or add the script to make it work.

## 4. Content-Security-Policy (per page, by meta tag, as pair.mjs, payments.mjs and principles.mjs do)

The videos page, and any page that carries a video slot, sets:

    default-src 'self'; script-src 'self'; style-src 'self'; img-src 'self' data:; frame-src https://www.youtube-nocookie.com https://www.instagram.com; connect-src 'self'; object-src 'none'; base-uri 'self'; form-action 'none'

- frame-src lists exactly the two hosts. youtube.com, instagram.com (without www), cdninstagram.com and wildcards are not allowed.
- No third-party host goes into script-src, img-src or connect-src.
- frame-ancestors cannot be set by a meta tag. Note it for the day the site has response headers.
- Add <meta name="referrer" content="no-referrer"> for the page itself.

## 5. Data and build

- Videos live in a reviewed data file (platform, id or shortcode, title, channel, date, region, source of the choice). The build rejects any entry whose id fails the patterns above, or whose platform is not "youtube" or "instagram".
- Every text field is escaped. Any link out goes through the same https-only href guard used elsewhere.
- Tests:
  - a bad id is rejected;
  - no third-party URL appears in the built HTML outside the click script's two templates;
  - the CSP meta is present;
  - the sandbox and allow strings are exact.

## 6. Review

Send me the data file and the code as patches, as usual. I re-render the page and check that it makes no third-party request before a click.

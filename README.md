# IsSiriAIAvailableInEU.com

A single-serving viral site that answers one question — **"Is Siri AI Available in EU?"** — with a giant **NO**.

Static, zero-build, edge-hosted on Cloudflare Pages. Optimized for social sharing.

> At WWDC 2026, Apple confirmed Siri AI will not ship in the EU on iOS 27 / iPadOS 27, citing the Digital Markets Act. It **will** be available on macOS 27, watchOS 27, and visionOS 27. ([Apple Newsroom](https://www.apple.com/newsroom/2026/06/due-to-dma-siri-ai-delayed-in-eu-for-ios-27-and-ipados-27/))

## Files

| File | Purpose |
| --- | --- |
| `index.html` | The whole page — inline CSS, minimal vanilla JS, all meta tags |
| `og-image.png` | 1200×630 social preview (referenced by OG/Twitter meta tags) |
| `og-image.html` | Source template for the OG image (re-render after design changes) |
| `favicon.svg` / `apple-touch-icon.png` | Simple "NO" mark |
| `_headers` | Cloudflare Pages cache rules |

## The single source of truth

The verdict is driven by one `STATUS` object near the bottom of `index.html`:

```js
const STATUS = {
  answer: "No",            // change to "Yes" if Apple ships Siri AI to the EU
  footnote: "Well, not on your iPhone or iPad. Mac, Watch, and Vision Pro are fine.",
  since: "2026-06-08T17:00:00Z",
  sourceUrl: "https://www.apple.com/newsroom/2026/06/due-to-dma-siri-ai-delayed-in-eu-for-ios-27-and-ipados-27/"
};
```

Flip `answer` to `"Yes"` and the colour (red → green), footnote, source link, share text, and live counter all follow. For the no-JS fallback to match, also update the `<p class="answer">` text in the markup.

## Deploy to Cloudflare Pages (Git import)

1. Push this repo to GitHub (already done if you used the provided repo).
2. Cloudflare dashboard → **Workers & Pages → Create → Pages → Connect to Git**.
3. Pick this repository. Build settings:
   - **Framework preset:** None
   - **Build command:** *(leave empty)*
   - **Build output directory:** `/`
4. **Save and Deploy.** You get a `*.pages.dev` URL in ~30s.
5. **Custom domains → Set up a custom domain →** `issiriaiavailableineu.com` (the domain is already on Cloudflare, so DNS is automatic).
6. Validate the social unfurl with [opengraph.xyz](https://www.opengraph.xyz/) before sharing widely.

## Analytics

[Cloudflare Web Analytics](https://www.cloudflare.com/web-analytics/) — free, cookieless, no consent banner needed (so the "collects no personal data" line stays true). Two ways to turn it on:

- **Easiest (zero code):** in the Cloudflare Pages project → **Metrics → Web Analytics → Enable**. Cloudflare auto-injects the beacon. If you do this, delete the `beacon.min.js` `<script>` in `index.html` so visits aren't counted twice.
- **Manual:** Cloudflare dashboard → **Analytics & Logs → Web Analytics → Add a site**, copy the token it gives you, and paste it into the `data-cf-beacon` token placeholder in `index.html`.

View the numbers anytime under Web Analytics in the Cloudflare dashboard.

## Regenerating the OG image / icons

Requires Google Chrome.

```sh
CHROME="/Applications/Google Chrome.app/Contents/MacOS/Google Chrome"
"$CHROME" --headless --disable-gpu --force-device-scale-factor=1 \
  --window-size=1200,630 --hide-scrollbars \
  --screenshot="og-image.png" "$(pwd)/og-image.html"
"$CHROME" --headless --disable-gpu --force-device-scale-factor=1 \
  --window-size=180,180 --default-background-color=00000000 \
  --screenshot="apple-touch-icon.png" "$(pwd)/favicon.svg"
```

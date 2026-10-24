# endlesslibrary.com.au

Landing page for Endless Library, rebuilt from the designers' Webflow front page.
Static, no build step, no Webflow scripts. The forum lives separately at
<https://shush.endlesslibrary.com.au> (Discourse, on the VPS).

## Files

| File | What it is |
|---|---|
| `index.html` | The home page. Styles and script are inline. |
| `event-submissions/index.html` | Event proposal form at `/event-submissions` (Web3Forms + hCaptcha). |
| `404.html` | Forwards old forum paths (`/t/…`, `/c/…`) to the subdomain; every other missing page redirects to a YouTube link. |
| `assets/Belledejour.ttf` | Nav, poem, headings. |
| `assets/logo.png` | The handwritten wordmark (from the Webflow project, 500px). |
| `assets/sidebar.png` | Halftone artwork in the sidebar, between the intro text and the hours/address. |
| `assets/poem-reveal.png` | The little glyph bottom-right of the poem; hover it to show the whole poem. |
| `CNAME` | Custom domain. Don't delete it. |

The sans is **Archivo** (Google Fonts), standing in for the design's Acumin.
Belledejour has no digits and almost no punctuation, so anything with numbers
(hours, dates, times) is set in Archivo. All sans text uses one size (`--t`).

## Behaviour

- **Poem**: hover a dot to reveal its word; hover the corner glyph to reveal all of it. On touch screens, tap instead. Word size is calculated from the column width so the longest word always fits.
- **Phone**: the sidebar is hidden; the page is just the poem (two columns). Hours, address and the newsletter link live in the footer.
- **Calendar**: scrolls the footer up over the page to show the events row.
- **Gallery**: opens the gallery EOI popup. **Subscribe** / "Sign up to our newsletter": MailerLite popup. **Events EOI**: `/event-submissions`. **Shush**: the forum.
- **Footer**: the page is sticky; scrolling slides the footer up over it. On desktop the footer is a horizontal row of upcoming events from Discourse; on phones it also carries the hours/address block.

## Events

Config is at the top of the `<script>` block in `index.html` (`CONFIG.forum`, `limit`, `timeZone`).
Times are shown in Sydney time regardless of where the visitor is.

The feed only answers browsers on the origin listed in Discourse's CORS settings,
which is exactly `https://endlesslibrary.com.au`. Events load on the live site
over HTTPS but show a "see what's on at Shush" message when opened from disk,
from `localhost`, or over plain `http://`. In `containers/app.yml` on the VPS:

```yaml
DISCOURSE_ENABLE_CORS: true
DISCOURSE_CORS_ORIGIN: 'https://endlesslibrary.com.au'
```

then add `https://endlesslibrary.com.au` to the `cors origins` site setting.
Field mapping from the Discourse response lives in `normalise()`; if events show
up with missing titles or times, compare against
`https://shush.endlesslibrary.com.au/discourse-post-event/events.json?include_details=true`.

## Deploying

Pushes to `main` publish via GitHub Pages. Custom domain is set in
**Settings → Pages**; keep **Enforce HTTPS** ticked, because the events feed
only works from the HTTPS origin.

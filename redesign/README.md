# Redesign (from the aus-glass Webflow preview)

A static rebuild of the designers' Webflow front page. No build step, no Webflow scripts.

| File | What it is |
|---|---|
| `index.html` | The whole page. Styles and script are inline. |
| `assets/Belledejour.ttf` | Nav, poem, intro text, headings, acknowledgement. |
| `assets/logo.png` | The handwritten wordmark (from the Webflow project, 500px). |
| `assets/sidebar.png` | Halftone artwork in the sidebar, between the intro text and the hours/address. |
| `assets/poem-reveal.png` | The little glyph bottom-right of the poem; hover it to show the whole poem. |

The sans is **Archivo** (Google Fonts), standing in for the design's Acumin.
Belledejour has no digits and almost no punctuation, so anything with numbers
(hours, dates, times) is set in Archivo.

## Behaviour

- **Poem**: hover a dot to reveal its word; hover the corner glyph to reveal all of it. On touch screens, tap instead. Word size is calculated from the column width so the longest word always fits.
- **Phone**: the sidebar is hidden; the page is just the poem (two columns). Hours, address and the newsletter link live in the footer.
- **Calendar**: scrolls the footer up over the page to show the events row.
- **Gallery**: opens the gallery EOI popup. **Subscribe** / "Sign up to our newsletter": MailerLite popup. **Events EOI**: `/event-submissions`. **Shush**: the forum.
- **Footer**: the page is sticky; scrolling slides the footer up over it. The footer holds the hours/address and a horizontal row of upcoming events from Discourse.

## Events

Config is at the top of the `<script>` block (`CONFIG.forum`, `limit`, `timeZone`).
Times are shown in Sydney time regardless of where the visitor is.
The feed only answers browsers on origins listed in Discourse's `cors origins`
setting. `https://endlesslibrary.com.au` is allowed, so events load on the live
domain but show a "see what's on at Shush" message when opened from disk.

## Going live

Preview at `/redesign/` after pushing. To make it the home page, move
`redesign/index.html` and `redesign/assets/` to the repo root (replacing the
old `index.html`). The paths inside are relative, so nothing else needs changing.

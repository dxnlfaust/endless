# endlesslibrary.online

Landing page for Endless Library. Static, no build step, no dependencies.
The forum lives separately at <https://shush.endlesslibrary.online> (Discourse, on the VPS).

## Files

| File | What it is |
|---|---|
| `index.html` | The whole page. Styles and script are inline. |
| `mark.gif` | The drawn heart-and-book animation. 560px wide, 10fps, 2 colours. |
| `404.html` | Styled 404 that also forwards old forum paths (`/t/…`, `/c/…`) to the subdomain. |

## Editing

Open `index.html` in a browser — that's the whole workflow. Keep it in the same
folder as `mark.gif` or the animation won't load.

The mark is drawn with `mix-blend-mode: multiply`, so its white background drops
out onto the paper colour. If you swap the image, keep it high-contrast black on
white or that trick stops working.

Colours and fonts are CSS custom properties at the top of the `<style>` block.

## Events

The events list pulls upcoming events from Discourse. Config is at the top of
the `<script>` block in `index.html`:

```js
const CONFIG = {
  forum: 'https://shush.endlesslibrary.online',
  limit: 6,
  demo: true
};
```

`demo: true` keeps the placeholder events visible if the fetch fails, so the
page still looks right when opened from disk. **Set it to `false` once the live
feed works** — otherwise a broken fetch shows six invented events to the public.

Requirements on the forum side:

1. `calendar enabled` and `discourse post event enabled` both on.
2. CORS allowing this origin. In `containers/app.yml` on the VPS:
   ```yaml
   DISCOURSE_ENABLE_CORS: true
   DISCOURSE_CORS_ORIGIN: 'https://endlesslibrary.online'
   ```
   then add `https://endlesslibrary.online` to the `cors origins` site setting.

Field mapping from the Discourse response lives in `normalise()`. If events show
up with missing titles or times, open
`https://shush.endlesslibrary.online/discourse-post-event/events.json?include_details=true`
and compare the field names.

## Deploying

Pushes to `main` publish via GitHub Pages. Custom domain is set in
**Settings → Pages**; don't delete the `CNAME` file GitHub creates there.

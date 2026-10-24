# Handoff: add an `/event-submissions` page (event proposal form)

## Goal

Add a new page at **`/event-submissions`** to the Endless Library website (endlesslibrary.com.au, static site hosted on GitHub Pages). The page contains:

1. A **"Back" button fixed in the top-left corner** that returns to the home page (`/`).
2. The event proposal **intro copy** and **form** below (replacing a Google Form we currently use).

It is a full page, not a pop-up or modal. Nothing else on the page: no site nav, no footer unless the rest of the site's pages have one that is trivially reused (see "Match the site" below).

Form submissions are handled by **Web3Forms** (free tier: 250 submissions/month, spam protection, hCaptcha support, custom redirect). The form POSTs to Web3Forms and the submission is emailed to the account owner.

## Before you start

Explore the repo first. I don't have the details of how it's built.

- Work out the stack: plain HTML, or a generator (Jekyll is the GitHub Pages default; could also be Hugo, Eleventy, etc.). Check for a build step or GitHub Actions workflow.
- Look at an existing page (home page especially) for the layout/template, CSS file(s), fonts, colours, and spacing conventions.
- Create the page the way existing pages are created in this repo. If it's plain static HTML, create **`event-submissions/index.html`** so the URL works as `/event-submissions` (no `.html`). If it's a generator, follow its conventions (layouts, front matter, permalinks) so the final URL is `/event-submissions`.

## Match the site

Reuse the site's existing fonts, colours, and base CSS so the page feels native. Do not recreate the Google Forms look. Keep it simple, readable, and responsive (works well at phone width). If there's a shared stylesheet, add form-specific styles there (or in a small dedicated file) rather than inlining a large `<style>` block, unless the site's existing pattern is inline styles.

## Back button

- A link (`<a href="/">`) styled as a button, labelled **"Back"** (a leading arrow like `←` is fine).
- `position: fixed; top: 1rem; left: 1rem;` with a z-index above page content.
- Always goes to the home page, so use `href="/"`. Don't use `history.back()`.
- Comfortable tap target (about 44px min height), visible focus state, and sufficient contrast against whatever is behind it while scrolling (give it a background, border, or shadow so it never becomes unreadable over text).
- Add enough top padding to the page content so the button never overlaps the heading, especially on mobile.

## Page copy (use verbatim)

Do not edit, "Americanise", or reword this copy. Spelling is Australian (organise, subsidised). Use semantic HTML (`<h1>`, `<h2>`, `<ul>`, `<p>`, `<strong>`).

**H1:** Event Proposals @ Endless Library

**Paragraph 1:**
Endless Library is open to proposals for **free** or **ticketed** events/reading groups/workshops/hobby clubs/anything else you can think of. We are particularly interested in proposals that respond to the space as a library. The possibilities are endless...

**Paragraph 2:**
We want to keep our space as cheap as possible! The costs of running Endless Library are heavily subsidised by a core group of 20 volunteer librarians ("the shush"). The fees for hiring our space are redirected straight back into the Library; no individuals profit.

**Paragraph 3:**
Please note that in order to organise an event at Endless, you will need to **become a member** ($10/month or $100/year; more info on this soon).

**Heading (bold, e.g. `<h2>`): Fees:**
- Events with tickets that cost $5 or less: $50 hire fee
- Events with tickets that cost more than $5: $100 hire fee, or 20% of ticket proceeds, whichever is higher
- If your event goes for longer than 4h, these fees may increase
- Small-scale (~8pp) events that occur during Library opening hours (Saturday & Sunday 9am-6pm) incur no hire fee

**Heading: The Library & facilities**
- 1/292 Marrickville Road, accessed via Marrickville Lane
- Is not wheelchair accessible; 2 flights of stairs to access the space
- Consists of two rooms: a larger Library and a smaller Gallery. Both have windows.
- The larger Library has a capacity of around 50 people. The smaller gallery has a capacity of around 20 people
- There is a kitchen equipped with stove, oven, sink and bench space for cooking
- Has a projector and screen for film screenings
- Has seating (fold-out chairs; stools and couches) for around 50 people, and a few tables
- Has a small PA

**Heading: Please note**
- If your event features tech such as music gear, you will need to have a person responsible for setting up, tuning and troubleshooting the soundsystem
- You will be responsible for promoting, ticketing and communicating around your event
- Endless will ordinarily also share information about your event in our newsletter and monthly calendar

**Closing paragraph (before the form):**
Your proposal will be reviewed by members of the Shush and you will receive an answer within 4 weeks.

A short line like "* Indicates required question" should appear above the fields (only the email field is required).

## Form fields

Every field needs a visible `<label>` (not placeholder-only), a `name` attribute as listed, and sensible `autocomplete`/`type` attributes. Stack fields vertically with generous spacing. Use `<textarea>` for the free-text questions, since people will write more than a line.

| # | Label (verbatim) | `name` | Input | Required |
|---|---|---|---|---|
| 1 | Email | `email` | `type="email"`, `autocomplete="email"` | **Yes** |
| 2 | Briefly, what is your proposal? | `proposal` | textarea | No |
| 3 | Is your proposal: | `proposal_type` | radio group, options **Free** / **Ticketed** (use `<fieldset>`/`<legend>`) | No |
| 4 | How many attendees do you expect? | `expected_attendees` | `type="text"` with `inputmode="numeric"` (answers may be ranges like "20-30") | No |
| 5 | Who are the artists / speakers / participants? | `participants` | textarea | No |
| 6 | When do you intend the event to take place? | `event_date` | `type="date"` | No |
| 7 | What times will the event start/finish? | `start_time`, `end_time` | two `type="time"` inputs, labelled "Start" and "Finish" | No |
| 8 | How do you want Endless to support your proposal? | `support_requested` | textarea | No |
| 9 | Is there anything else you want to tell us? | `anything_else` | textarea | No |

**Note on fields 6 and 7:** the original Google Form was misconfigured. Q6 was a radio button with a placeholder "Option 1", and Q7 (which had a typo, "What times will the event will start/finish?") was a date picker. The table above is the intended fix: Q6 becomes a date picker, Q7 becomes start/finish time inputs, and the typo is corrected. Implement as specified.

Do **not** include the Google Forms footer text ("A copy of your responses will be emailed to the address you provided", reCAPTCHA, "Never submit passwords through Google Forms", etc.). Web3Forms on the free tier does not send submitters a copy.

Submit button label: **Submit**. No "Clear form" button.

## Web3Forms wiring

Endpoint: `https://api.web3forms.com/submit` (method `POST`).

Hidden inputs inside the form:

```html
<input type="hidden" name="access_key" value="YOUR_ACCESS_KEY_HERE">
<input type="hidden" name="subject" value="New event proposal - Endless Library">
<input type="hidden" name="from_name" value="Endless Library website">
<!-- Honeypot: must stay empty. Hidden from humans, bots tend to fill it in. -->
<input type="checkbox" name="botcheck" style="display:none" tabindex="-1" autocomplete="off">
```

- **Access key:** Web3Forms access keys are designed to be public in client-side HTML, so it is fine to commit it. The owner gets one by entering the email address that should receive submissions at web3forms.com (the key is emailed to that address). **Ask me for the key before finishing. If I haven't supplied it, use the `YOUR_ACCESS_KEY_HERE` placeholder and tell me clearly that it still needs replacing.** Do not invent a key, and do not submit test entries with the placeholder.
- **Submission behaviour:** progressively enhance. The form should work as a normal HTML POST with no JavaScript, but with JS available, intercept the submit event, send the data with `fetch` (`Accept: application/json`), and on `success: true` replace the form with an inline thank-you message (something like: "Thanks! Your proposal has been sent. You'll hear back from the Shush within 4 weeks."). Do not create a separate thank-you page.
- While sending: disable the submit button and show a "Sending..." state to prevent double submits.
- On failure (network error or `success: false`): keep the form contents intact and show a clear, friendly error message near the button, e.g. "Something went wrong sending your proposal. Please try again, or email us directly." Don't invent an email address. If the site already has a contact email somewhere, use that; otherwise leave the sentence without one and flag it to me.
- Use the browser's built-in validation for the required email field.
- Spam protection for v1 is the honeypot above. Web3Forms also supports hCaptcha on the free tier, but don't add it unless I ask later. Mention in your summary that it's available if spam turns up.

## Accessibility and quality

- Labels associated with inputs, logical tab order, visible focus styles, sufficient colour contrast.
- Radio group in a `fieldset` with a `legend`.
- Error and success messages announced to screen readers (`role="alert"` or `aria-live="polite"`).
- Set a `<title>` such as "Event Proposals | Endless Library" and a brief meta description, consistent with how other pages on the site do it.
- Responsive: single column, fields full width on mobile, comfortable max width (~40-45rem) on desktop.

## Out of scope

- Don't link to this page from elsewhere on the site. I'll do that separately.
- Don't change existing pages or styles beyond what's needed for this page (additive CSS only).
- Don't set up a backend, serverless function, or any other service. Web3Forms only.

## Done when

- `/event-submissions` renders with the copy, form, and fixed top-left Back button, matching the site's look.
- Back button returns to `/` and stays visible while scrolling, without overlapping content at any screen width.
- Form fields and `name` attributes match the table; email is required.
- Verified locally (serve the site and check desktop and phone widths).
- Changes committed on a branch or to `main` per how this repo normally works (check git history for convention, ask me if unclear). GitHub Pages will deploy from there.
- Your summary tells me: which files you added/changed, the access key status (real or placeholder), and anything you assumed.

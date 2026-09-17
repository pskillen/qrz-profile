# MM9PDY on QRZ.com

Working copy of [Paddy's QRZ biography](https://www.qrz.com/db/MM9PDY) — the HTML and CSS that live in QRZ's profile editor, a frozen View Source of the live listing, and a local chrome harness that renders your current bio inside that chrome.

QRZ does not give you the files. You paste CSS into a text field and edit the bio in a WYSIWYG (with a source view). This repo exists so those fragments can be versioned, previewed, and eventually rewritten by an agent without guessing at the live site.

The long-term goal is a page that reads like a ham introducing himself on the air, not a CV. That rewrite is **not** the job yet. First job is a harness: local preview, a clear edit/publish loop, and enough QRZ constraints that an agent does not silently break the live page.

## Files

### `profile/` — what you paste into QRZ

| File | What it is | Source of truth? |
| --- | --- | --- |
| [`profile/page.html`](profile/page.html) | Biography markup pasted into QRZ's editor | **Yes** — this is what you publish |
| [`profile/page.css`](profile/page.css) | Custom CSS pasted into QRZ's CSS field | **Yes** — this is what you publish |
| [`profile/Gemini_Generated_Image_png.jpg`](profile/Gemini_Generated_Image_png.jpg) | Callsign photo shown in the header (outside the bio iframe) | **No** — chrome asset; uploaded on QRZ, not part of the bio paste |

### `harness/` — local preview and captures

| File | What it is | Source of truth? |
| --- | --- | --- |
| [`harness/chrome.html`](harness/chrome.html) | Local page chrome (QRZ-like layout, no trackers/ads) | **No** — harness only |
| [`harness/bio-frame.html`](harness/bio-frame.html) | Biography iframe; loads `profile/page.html` + `profile/page.css` | **No** — harness only |
| [`harness/qrz_bio_iframe.css`](harness/qrz_bio_iframe.css) | CSS QRZ prepends inside the bio iframe (`bio_system.css` + YouTube helpers) | **No** — do not paste into the QRZ CSS field |
| [`harness/wrapper.snapshot.html`](harness/wrapper.snapshot.html) | Untouched View Source of the public page (as captured) | **No** — posterity / diff against live QRZ |
| [`harness/css.md`](harness/css.md) | QRZ's own CSS usage notes (copied from the site) | Platform rules, not style |

Do not paste `chrome.html`, `bio-frame.html`, or `wrapper.snapshot.html` back into QRZ. On the live site your bio is Base64-injected into a sandboxed iframe (QRZ's iframe prelude CSS first, then your `page.css`). Locally, `chrome.html` is a stripped stand-in for that chrome, and `bio-frame.html` is the iframe: it links `qrz_bio_iframe.css` then `page.css`, and inserts `page.html` into `#biodata`.

If the live site and this repo disagree, the live editor wins until you recapture. Recapture by copying `page.html` / `page.css` out of the editor. Only replace `wrapper.snapshot.html` when you want a new frozen copy of the public page; do not overwrite `chrome.html` with a fresh View Source.

## How QRZ actually renders the bio

On the public page (`/db/MM9PDY`):

1. Chrome (callsign header, photo, tabs, logbook, ads) is QRZ's. The avatar sits here, not in the bio iframe — locally that is `profile/Gemini_Generated_Image_png.jpg` (a JPEG; the `png` in the name is Gemini's filename).
2. The Biography tab hosts an iframe (`#biodata`).
3. JS writes a tiny document into that iframe, then Base64-decodes your HTML into `#biodata` inside it.
4. JS injects a `<style>` block: QRZ system bio CSS + your custom CSS.

That isolation is why `page.css` can use a `*` reset and `:root` variables without immediately wrecking the outer QRZ layout — and why local preview that only opens `page.html` will **not** look like production. You are missing iframe padding, `bio_system.css`, and the page chrome.

`#t_bio.biodiv { background-color: #FFFFFF }` at the end of `page.css` is aimed at the outer tab, not the iframe. Keep that in mind if you restyle backgrounds.

## Constraints (do not skip)

Copied and condensed from QRZ + `harness/css.md`:

- **No `#qrz…` selectors or IDs.** That prefix is reserved.
- **Images:** use the QRZ cloud URL, not `/hampages/…`. For this callsign the last letter is `y`:

  `https://static.qrz.com/y/mm9pdy/YourFile.jpg`

  Prefix and callsign are lowercase; the filename may be mixed case. Edit-time vs runtime paths differ; the cloud URL is the one that works both places.
- **What you can ship is HTML + CSS only.** No JS of your own. QRZ already sandboxes the iframe (`allow-popups`, forms, same-origin; not scripts you author).
- **WYSIWYG will mangle things.** Expect entity encoding (`&mdash;`, `&#39;`), `&nbsp;`, extra wrappers, and stray characters. After a round-trip through the editor, diff `page.html` against git before assuming the file is still clean.
- **CSS is concatenated, not a standalone file.** Your rules share a stylesheet with QRZ's bio helpers (coloured boxes, YouTube placeholders, body padding). Prefer classes under `.profile-container`. Avoid `html` / `body` restyles unless you have checked them in `chrome.html`.
- **No CSS comments.** `/* ... */` in the CSS field gets rejected outright ("invalid character in CSS").
- **8192 character limit on the CSS field.** `page.css` is kept minified for this reason — see `harness/css.md`.
- **Publish is copy-paste.** There is no deploy script. Change files here, then paste into [Edit MM9PDY](https://www.qrz.com/edit/MM9PDY).

## Current page (as captured)

Voice is first-person, Glasgow / IO75wt, intermediate licence (MM9PDY, previously MM7IGV). Structure today:

1. Header — callsign badge, name, licence, locator
2. About me — origin story (Meshtastic → licence → DMR / FM / HF)
3. Promoted cards — Codeplug tool (beta testers), Meshflow
4. How to find me — DMR TGs, S20 / local repeaters, mesh node, HF, email
5. Bands & modes — chips
6. The shack — radios and antennas as a grid
7. Longer sections — Meshtastic/MeshCore, DMR, HF, building/electronics

That outline is accurate and useful. The problem is tone and density: stacked `h2`s, equipment lists, and “about me” copy that reads like a professional bio. The intended rewrite (later) should keep the facts and make it warmer and easier to scan for someone who just looked up the call.

## Local preview

`file://` will not work: `bio-frame.html` fetches `../profile/page.html`. Serve the **repo root**, then open the chrome:

```text
cd /path/to/qrz.com
python3 -m http.server 8000
# http://127.0.0.1:8000/harness/chrome.html
```

Ad slots, GTM, AdSense, and the Google Maps embed are grey placeholders. The QRZ logo and flag still load from QRZ's CDN if you are online; the biography itself is entirely local. Hard-refresh after edits (stylesheets and `page.html` are cache-busted).

Still to add for agents: an edit contract (only `page.html` / `page.css` are writable sources of truth), a publish checklist, and — later — the less-CV rewrite.

## Workflow

```text
edit profile/page.html / profile/page.css
        ↓
python3 -m http.server at repo root → harness/chrome.html
        ↓
paste into QRZ editor, save
        ↓
view https://www.qrz.com/db/MM9PDY
        ↓
optional: replace harness/wrapper.snapshot.html with a fresh View Source
          (leave chrome.html alone)
```

`wrapper.snapshot.html` is a third-party page capture (trackers and all). Keep it for posterity; do not tidy it. `chrome.html` is ours and is safe to keep lean.

## Licence / privacy

Personal profile content for MM9PDY. The snapshot is a third-party page capture and should not be republished as if it were yours.

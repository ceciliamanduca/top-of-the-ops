# Top of the Ops — Website

Source for the **Top of the Ops** podcast website, live at [top-of-the-ops.com](https://top-of-the-ops.com).

Top of the Ops is a podcast for operators, hosted by Cecilia Manduca and Beatrice Aliprandi — two ex-VCs turned operators talking about the messy reality of running the show from behind the scenes: ops, strategy, and Chief of Staff life.

## Structure

A static site: hand-written HTML, one shared stylesheet, no build step.

| File / pattern | Purpose |
| --- | --- |
| `index.html` | Landing page with the episode list |
| `episode-*.html` | One page per episode (show notes, chaptered transcript, FAQ, schema) |
| `free-guides.html` | Free AI guides hub, linked from the nav on every page |
| `guide-*.html` | One page per Operator Field Guide, each with a matching `.pdf` |
| `cash-flow-tracker.xlsx` | Downloadable template that ships with guide 02 |
| `style.css` | Shared styles for every page |
| `404.html` | Custom not-found page |
| `_redirects` | Netlify redirect rules (short `/episode-N` URLs → full slugs) |
| `sitemap.xml`, `robots.txt` | SEO / crawler config |
| `favicon.svg`, `og-image.png` | Branding and social-share image |
| `*.jpg`, `*.png` | Host photos and episode thumbnails |
| `google*.html` | Google Search Console verification |

## Local preview

No build is needed. Open `index.html` directly, or serve the folder:

```bash
python3 -m http.server 8000
# then visit http://localhost:8000
```

## Deploying

The site is hosted on Netlify and served at the custom domain top-of-the-ops.com. The `_redirects` file is Netlify-specific and handles the short episode URLs.

## Adding an episode

Each episode is a standalone `episode-<n>-<slug>.html` page. When adding one, also update `index.html` (episode tile), `sitemap.xml`, and `_redirects` so the short `/episode-N` URL resolves.

## Adding a free guide

Guides are listed on `free-guides.html` and live at `guide-<n>-<slug>.html`, each with a matching `.pdf`. Guides 01–05 are live; keep numbering sequential.

1. Create `guide-<n>-<slug>.html` by copying an existing guide. They all share the same shell: contents nav, callout styles, copy-to-clipboard prompt blocks, tables, print styles and the PDF download block. If a guide ships a downloadable asset (as guide 02 does with `cash-flow-tracker.xlsx`), add the file to the repo root, link it from a `.callout` in the hero, and add a second button to the download block.
2. In `free-guides.html`, add a card inside `.guides-list` (copy-paste template sits in an HTML comment right above it), newest first.
3. Add a `listItem` to the `ItemList` in the `free-guides.html` `<head>` and bump `numberOfItems`.
4. Export the PDF (see below).
5. Update `sitemap.xml`, `llms.txt`, and `_redirects` (short `/guide-N` URL).

### Exporting a guide PDF

The PDF is rendered from the page itself, so the print stylesheet in `style.css` (`@media print`) is the single source of truth for how it looks. Serve the folder, then:

```bash
"/Applications/Google Chrome.app/Contents/MacOS/Google Chrome" --headless=new --disable-gpu --no-pdf-header-footer --virtual-time-budget=20000 --print-to-pdf=guide-1-meeting-coach.pdf "http://localhost:8000/guide-1-meeting-coach.html?print=1&pdf=1"
```

**Re-run this whenever you edit a guide's content**, or the PDF will drift out of sync with the page.

Two query params drive it:

| Param | Effect |
| --- | --- |
| `print=1` | Expands the collapsed troubleshooting answers so they appear in the export |
| `pdf=1` | Renders the print view in the brand's dark palette (adds `.pdf-export` to `<html>` and `<body>`), and rewrites relative links to absolute ones so they still work once the PDF leaves the browser |

To re-export all five at once, with the folder served on port 8000:

```bash
for s in guide-1-meeting-coach guide-2-monday-money-hour guide-3-daily-brief guide-4-birthday-bot guide-5-invoice-fairy; do "/Applications/Google Chrome.app/Contents/MacOS/Google Chrome" --headless=new --disable-gpu --no-pdf-header-footer --virtual-time-budget=20000 --print-to-pdf="$s.pdf" "http://localhost:8000/$s.html?print=1&pdf=1"; done
```

There are deliberately **two print themes**, both in the same `@media print` block:

- **Dark** (`.pdf-export`) — the downloadable PDF. Matches the site. Uses `@page { margin: 0 }` with the inset moved onto `.page`, because Chrome never paints backgrounds into the page margin and the export would otherwise sit in a white frame.
- **Light** (`body:not(.pdf-export)`) — what the on-page "Print this page" button produces. Dark-on-white so it doesn't flood a paper printer.

Because the page margin is zero, content on continuation pages starts close to the top edge — there is no per-page top inset available in Chrome's print-to-PDF without a pagination library. Worth a glance at the output when a guide's content changes.

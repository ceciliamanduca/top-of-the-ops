# Top of the Ops — Website

Source for the **Top of the Ops** podcast website, live at [top-of-the-ops.com](https://top-of-the-ops.com).

Top of the Ops is a podcast for operators, hosted by Cecilia Manduca and Beatrice Aliprandi — two ex-VCs turned operators talking about the messy reality of running the show from behind the scenes: ops, strategy, and Chief of Staff life.

## Structure

A static site: hand-written HTML, one shared stylesheet, no build step.

| File / pattern | Purpose |
| --- | --- |
| `index.html` | Landing page with the episode list |
| `episode-*.html` | One page per episode (show notes, chaptered transcript, FAQ, schema) |
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

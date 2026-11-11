# Handoff: Flore E. Sebag résumé site (redesign)

This replaces the existing site in the `flore-sebag-portfolio` repo (GitHub: `floresebag/flore-sebag-portfolio`, branch `main`). The new design is final and approved. It was built in a Claude Design canvas and exported here as plain static files, using the **same file layout as the existing repo**. Your job: drop these files into the repo, deploy with `npx wrangler deploy`, and make any small follow-up changes asked for without changing the look.

## Files (same layout as the current repo)

```
flore-sebag-portfolio/
├── index.html       ← replaces the old index.html (the whole page)
├── styles.css       ← replaces the old styles.css (tokens + rules that can't be inline)
├── script.js        ← replaces the old script.js (intentionally empty, see below)
├── wrangler.jsonc   ← NEW: static-assets Worker config
├── .assetsignore    ← NEW: keeps .git, config and notes out of the upload
└── CLAUDE.md        ← this file
```

### Steps

1. In the existing repo, overwrite `index.html`, `styles.css` and `script.js` with these versions. They must all be replaced together: the old `script.js` targets `#nav`, `#menu-toggle` and `#nav-links`, which no longer exist, so it would throw errors against the new page.
2. Delete `styles.css1`. It's an orphaned backup from the old site that nothing references. If you keep it, `.assetsignore` already stops it from being deployed.
3. Add `wrangler.jsonc` and `.assetsignore`. **If the owner already has a wrangler config somewhere (e.g. outside this repo, or a `wrangler.toml`), keep theirs** and only make sure its assets directory points at the repo root and the ignore list covers `.git`.
4. Deploy:
   ```bash
   npx wrangler login     # first time only
   npx wrangler deploy
   ```
   Because the assets directory is the repo root (`"./"`), `.assetsignore` is what keeps `.git/`, `CLAUDE.md` and the config files from being published. Don't remove it.
5. Commit and push to `main` only when the owner asks.

If the owner's setup turns out to be Cloudflare Pages instead of Workers, the equivalent is `npx wrangler pages deploy .`. Confirm before switching.

## Design system (keep it consistent)

| Token (`styles.css`) | Value | Use |
|---|---|---|
| `--bg` | `#F3E0DA` | page and sticky nav (light blush pink) |
| `--ink` | `#16192B` | headings, body text, dark buttons, footer band |
| `--accent` | `#1E3A8A` | links, nav links, stat numbers, award years, "Class of 2027" sticker |
| `--text-2` | `#2E3245` | bullet text |
| `--muted` | `#565B6E` | dates, captions, labels |
| `--rule` | `#DDBFB4` | section dividers, nav bottom border |
| `--card` | `#FFFFFF` | stat cards, language chips, school club cards |

- Type: Instrument Serif 400 for the name, section headings, stat numbers and the publication title (italic). Inter 400/500/600 for everything else. Both load from Google Fonts.
- Layout: a max-width 1120px container with a 24px side gutter. Each section is a flex row: a heading column (`flex: 1 1 220px`) and a content column (`flex: 999 1 560px`), which stack on narrow screens with no media queries.
- The owner asked for a few playful touches: a tilted "Class of 2027" sticker, stat cards tilted ±1.5° with a hard 4px navy offset shadow, and nav links with a hover underline that slides in plus a 1px lift (switched off for reduced motion). **No emoji anywhere** (the owner asked for this explicitly).
- Most styles are inline `style=""` attributes because that's how the design tool exports. Only `--accent` is wired to a variable so far; the other hex values are repeated inline. Refactoring the inline styles into classes in `styles.css` and using the variables is welcome, but the rendered result must not change.

## Page structure and anchors

The sticky nav links to `#education`, `#publication`, `#awards`, `#beyond`, `#school`, `#work`, `#sports`, and `#top` (the name). Each section anchor is an invisible `<span id="…">` absolutely positioned at `top: -112px` inside its `<h2>`, so a jump lands just below the 64px sticky nav. This was a deliberate fix: the owner reported anchors landing under the nav. If you refactor, keep an equivalent offset; `html { scroll-padding-top: … }` or `scroll-margin-top` on the targets is the cleaner way in a normal browser.

## Content decisions already made (don't undo)

- **Home address and phone number are intentionally omitted.** This is a public page for a high-school student. Don't add them back unless the owner asks.
- Languages: **English and French only**. Spanish was removed on request. (The old site still said Spanish and listed AP scores. The new résumé content is the source of truth.)
- The tagline under the name and a "President / Podcaster / …" word band were removed on request.
- Email buttons are labeled "Email", not the address.
- All outbound links come from the original résumé PDF and open in a new tab (`target="_blank" rel="noopener"`).

## Open items to confirm with the owner

1. **Email address mismatch.** The site uses `mailto:fsebag2027@lfny.org`, as printed on the résumé, but the PDF's hyperlink pointed to `fsebag.2027@lfny.org`. Confirm which is right.
2. **Link check after deploy.** Test LinkedIn, the AJSR paper PDF, Spotify, Child Mind Institute Dare to Share, both YouTube videos, and Alinea Invest.
3. **Search indexing.** If the page shouldn't appear in search engines, add `<meta name="robots" content="noindex">`.
4. Optional, only if asked: favicon, Open Graph preview image, a "Download PDF résumé" button.

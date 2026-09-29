# Antarctica website

This folder is published as the site (e.g. via GitHub Pages "docs" folder). `index.html` and `style.css` are hand-written; everything under `manual/` is generated — don't edit those files directly.

## Rebuilding the manual

The manual source lives in `../manual-src/` as one Markdown file per section (`00-introduction.md`, `01-quick-start.md`, ...), plus a landing page (`index.njk`) and a shared layout (`_includes/layout.njk`). Eleventy builds them into `docs/manual/`.

From the repo root:

```sh
npm install        # first time only
npm run manual:build
```

This writes `docs/manual/index.html` (the landing page) and `docs/manual/<section>/index.html` for each section, reusing `docs/style.css` — nothing in `docs/manual/` needs to be committed by hand, just regenerated.

To preview with live reload while editing:

```sh
npm run manual:serve
```

### Editing content

- Edit the `.md` files in `manual-src/`, not the generated HTML in `docs/manual/`.
- Cross-links between sections use the pattern `[text](05-conductor.md#hold)` — the build rewrites these to relative links automatically.
- Images referenced in the manual live in `docs/images/` and are linked as `../../images/foo.png` (relative to the generated page, which is two directories deep).
- Callout boxes use raw HTML, not Markdown blockquotes, because they need `<div class="note">`/`<div class="tip">` classes from `style.css`:

  ```html
  <div class="note"><strong>Note</strong>Text of the note.</div>
  <div class="tip"><strong>Tip</strong>Text of the tip.</div>
  ```

  Any link or bold text inside one of these divs must be written as raw HTML (`<a href="...">`, `<strong>`) rather than Markdown syntax, since markdown-it treats the div as a raw HTML block and won't process Markdown inside it.
- Screenshots use `<figure class="shot"><img src="..." alt="..."></figure>` for the rounded-corner/shadow styling, also as raw HTML for the same reason.
- All internal links must point at an explicit `index.html` (not a bare trailing slash) — Safari opens Finder instead of navigating when a `file://` link resolves to a directory.

### Adding a new section

1. Add a new `NN-slug.md` file in `manual-src/` with front matter:
   ```yaml
   ---
   title: "Section Title"
   permalink: /manual/slug/
   eleventyNavigation:
     key: slug
     title: "Section Title"
     order: NN
   ---
   ```
2. Run `npm run manual:build`.

The `order` value controls its position in the sidebar table of contents.

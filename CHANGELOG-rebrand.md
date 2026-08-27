# Rebrand: FROLIC → 2Direct Marketing

## Summary

Removed all remaining `FROLIC` and `GFXPARTNER` template branding from the site
and renamed the site directory. `index.html` had already been rebranded in a
previous pass; the other ten pages had not, so the site was inconsistent.

## Directory rename

`FROLIC/` → `2direct-marketing/`

Renamed with `git mv`, so git recorded all 236 files as renames and history is
preserved. No file inside the site referenced the folder by name — every path is
relative — so nothing broke.

The folder is `2direct-marketing`, not the literal `2Direct Marketing`. A space
in a directory name becomes `%20` in every URL and breaks a number of build and
deploy tools. The display name is `2Direct Marketing` everywhere it is visible
to users. To use the literal name instead:

```bash
git mv 2direct-marketing "2Direct Marketing"
```

## Content changes (10 pages)

Applied to: about-us, booking, contact, our-team, passengers, payment, summary,
tours-grid, tours-list, tours-single.

| Was | Now |
| --- | --- |
| `<title>Frolic \| for Tour Operators & Travel Agencies</title>` | Per-page title, e.g. `About Us \| 2Direct Marketing` |
| `<meta name="description" content="Frolic \| ...">` | `2Direct Marketing \| for marketing of rental services` |
| `<meta name="keywords" content="tour, ice, trip, travel, ...">` | Brand keywords matching `index.html` |
| `<meta name="author" content="GFX Partner">` | `2Direct Marketing` (also fixed on `index.html`) |
| `images/logo-dark.png` | `images/2Direct-Marketing-nav-logo.png` |
| `GFXPARTNER INC. / 30 Aarhus, Denmark / 123 456 7890` (footer) | `2DIRECT MARKETING / Castries, Saint Lucia / 555-983` |
| `info@gfxpartner.com`, `media@frolic.com`, `support@frolic.com` | `2directmarketing@gmail.com`, now real `mailto:` links |
| `© GFXPARTNER` | `© 2DIRECT MARKETING` |
| `<h5>FROLIC INC.</h5>` ×4 (contact) | `<h5>2DIRECT MARKETING</h5>` |
| `BY GFXPARTNER` ×4 (tours-single) | `BY 2DIRECT MARKETING` |
| `<b>GFXPARTNER</b>` (payment) | `<b>2DIRECT MARKETING</b>` |

The nav logo swap is a true drop-in: `2Direct-Marketing-nav-logo.png` is
317×88px, identical to the `logo-dark.png` it replaces, so no layout shift.

## Bugs fixed along the way

**Dead favicon links (50 of them).** Every inner page pointed at
`ico/apple-touch-icon-144-precomposed.png` and four siblings. Those files are in
`icons/`, not `ico/` — five 404s per page across ten pages. Replaced with the
working favicon block from `index.html`. The stale `icons/` directory is now
unreferenced and can be deleted.

**Broken web manifest.** `ico/site.webmanifest` had empty `name` and
`short_name`, and its icon paths were root-absolute (`/android-chrome-192x192.png`)
while the icons live in `ico/`. Set the name to `2Direct Marketing` and made the
paths relative.

**Added `.gitattributes`.** See "Issues encountered" below.

## Issues encountered

**The uploaded zip was missing `FROLIC/images/` entirely.** All 216 image files
were absent, so git reported them as deletions. Committing as-received would
have deleted every image in the repository. Restored from `HEAD` before doing
anything else.

**All 21 "modified" files were line-ending noise.** The Windows zip round-trip
converted the working tree from LF to CRLF, producing a diff of 8,199 insertions
and 8,199 deletions with zero actual content change. Reset to `HEAD` before
editing so the rebrand diff is reviewable. Added a `.gitattributes` with
`* text=auto eol=lf` to stop this recurring.

## Verification

- `grep -rin "frolic\|gfxpartner"` across all HTML: **0 matches**
- Every page parses cleanly; no unclosed or stray tags
- All 11 pages have correct titles and brand strings
- 516 local references (images, css, js, fonts, icons, page links) resolve on disk
- Broken local references: **178 before → 128 after** (the 50 favicon 404s fixed)

## Known remaining — needs a decision, not a code fix

**128 broken links, all pre-existing, none caused by this rebrand.** The ten
inner pages still carry the stock travel-template navigation menu, which links
to pages that were never part of this project:

- `index-denmark.html`, `index-norway.html`, `index-finland.html`, `index-sweden.html`, `index-iceland.html`
- `blog-grid.html`, `blog-list.html`, `blog-single.html`
- `attractions-4cols.html`, `attractions-3cols.html`, `attractions-2cols.html`, `attractions-single.html`

`index.html` already has a rebranded four-item nav (HOME / ABOUT US / SERVICES
with Real Estate, Auto Marketplace, Energy Solutions / CONTACT). Copying that
nav onto the other ten pages would fix all 128 links at once, but that is a
site-structure decision rather than a rename, so it was deliberately left alone.

**Placeholder content still on the site.** `contact.html` shows four offices in
Aarhus (Denmark, Germany, France, Iceland) with the phone number
`123 456 7890`. The brand names in those blocks were updated, but the addresses
are still template dummy data for a company based in Saint Lucia. The body copy
across most pages is also still lorem ipsum, and the page filenames and content
(`tours-*`, `passengers.html`, "Tour Operators") describe a travel business
rather than a rental-services marketing company.

# CLAUDE.md — Personal academic homepage (handoff notes)

> Read this first. It captures the current state of the project, what's wired how,
> what's still in progress, and what's left to do, so you can continue editing locally.

## Working with the user

The user (Kaiwen) **prefers replies in Chinese (中文)**. Keep answers concise and
iterate quickly — this site has been built through many small, specific refinements,
not big rewrites. When in doubt about a visual choice, render a side-by-side mockup and
let the user pick rather than guessing (see the toggle saga below).

## What this is

A personal academic homepage for **Kaiwen Hong** (Ph.D. candidate, UIUC, advised by
Prof. Katherine Driggs-Campbell). It is a **Jekyll** site, forked from
[`leonidk/leonidk.github.io`](https://github.com/leonidk/leonidk.github.io),
itself a Jekyll port of [Jon Barron's website](https://jonbarron.info/).

Visual style is intentionally **minimal**: a single-column table layout with a bio
header and a list of papers (thumbnail + title + authors + venue + award + links).
No JS framework. The only JS is a small inline `<script>` in `_layouts/default.html`
for the Publications "selected / by date" toggle.

Source of truth for content is Kaiwen's **2026 resume**:
`/Users/kaiwenhong/Desktop/interview/homepage/Kaiwen_resume_2026.pdf`
(also copied to `pdfs/cv.pdf`, which the bio's "CV" link points at).

## Current status (done)

- **Bio** (in `_layouts/default.html`, not a markdown file): Ph.D. candidate at UIUC,
  advised by Katherine Driggs-Campbell; research framed as making real-world robots
  more **adaptable, deployable, and steerable**; currently diffusion-VLM research at
  Waymo, plus past time at a Humanoid stealth company, Amazon Robotics, and XPENG US.
- **Profile photo** is real (`images/profile.jpg`), rendered at `width:80%` centered.
- **Links row** under the bio: Email (`mailto:kaiwen2@illinois.edu`) / Google Scholar
  (`user=pwvOhvUAAAAJ`) / CV (`pdfs/cv.pdf`) / LinkedIn (`kaiwen-hong-524520141`).
  All real and verified from the resume.
- **11 publications** in `_publications/` — a Jekyll **collection** with `output: false`
  (they are list data for the homepage, not standalone pages; see "Why a collection"
  below). Each has a real media thumbnail, full author list (Kaiwen's name bolded),
  venue, optional award, and a `website` link. See the exact list + ordering below.
- **Equal-contribution / equal-advising** notes are baked into each post's `authors`
  field: contributors marked `*` (or `+` for advising), with an italic footnote line
  like `<em>* Equal contribution</em>` appended.
- **Awards** render in bold red under the venue (`color:#c0392b;font-weight:bold;`).
  The Human-Agent paper shows two award lines (split with `<br>`).
- **"Honors & Awards"** section near the bottom of `_layouts/default.html` is static
  HTML (7 awards from the resume), with the conference names bolded. Not generated
  from `_publications/`.
- All thumbnails are now **GIF / PNG / JPG** — there are **no MP4s** left (see
  "Media pipeline" for why). Animated demos are `.gif` so they always loop.
- `_config.yml`: name, `avatar: images/profile.jpg`, `email`, `linkedin`.
- `Gemfile` switched to modern Jekyll (`jekyll ~> 4.3`) for local preview — see gotchas.

### Publication list & order (newest `date:` first = top of page)

| date file | short | selected? | media | venue / status |
|-----------|-------|-----------|-------|----------------|
| 2026-05-25 | spt (Self-Labeled Preference Transfer) | ✅ | spt.gif | Under review · **no website yet** |
| 2026-05-22 | discretertc | ✅ | discretertc.gif | Under review |
| 2026-05-19 | brick-composer | — | brick-composer.png | Under review |
| 2026-05-17 | scooping (Sim-and-Real Co-Training) | ✅ | scooping.gif | ICRA |
| 2026-05-10 | policy-consensus | ✅ | policy-consensus.gif | ICRA (CVPR'26 workshop best paper) |
| 2025-09-01 | height | ✅ | height.gif | T-ASE |
| 2025-07-01 | score-fusion | — | score-fusion.jpg | ICML |
| 2025-05-01 | human-agent | ✅ | human-agent.jpg | ICRA (2× ICRA'25 awards) |
| 2023-11-06 | stowing | ✅ | stowing.gif | CoRL (best paper finalist) |
| 2023-11-05 | voice-robot | — | voice-robot.jpg | CoRL |
| 2023-05-29 | crowd-nav | ✅ | crowd-nav.png | ICRA |

The `date:` in front-matter is the only sort key. To reorder, change the date in the
filename **and** the `date:` field. The `2026-05-*` dates are deliberately fabricated
ordering keys for in-submission work, not real publication dates.

## ⏳ In progress — Publications "selected / by date" toggle redesign

This is the **active task**. The user has said the toggle is "too ugly" three times.
Current live markup (in `_layouts/default.html`, the `.pub-toggle` span around line 67):
`show: <a>selected</a> / <a>by date</a>` with blue links — still rejected.

To resolve it, a comparison page **`toggle-preview.html`** (repo root, served at
`http://127.0.0.1:4000/toggle-preview.html`) renders **6 candidate styles** in the
real site font for the user to pick by number:

1. underline tabs (red accent, matches award red) — *recommended*
2. filled dark pill
3. soft light pill
4. minimal slash (blue link)
5. letterspaced caps (`SELECTED / BY DATE`) — *recommended alt*
6. quiet single link (`show all ▾`)

**Next step:** once the user picks a number, implement that style by replacing the
`.pub-toggle` / `.sep` CSS (around lines 14–18) and the toggle `<span>` HTML
(around line 67) in `_layouts/default.html`, then **delete `toggle-preview.html`**
(it's a throwaway mockup, not part of the site). The toggle JS logic itself
(`show('selected')` / `show('all')`, the `pub-selected` class) stays — only the
button markup/CSS changes. The active button uses class `pub-tab-active`.

## How the toggle works (so you don't break it)

- Each post can set `selected: true` in front-matter. The layout adds class
  `pub-selected` to those `<tr>`s.
- The inline `<script>` defaults to showing only selected papers (`show('selected')`),
  and "by date" reveals all. This is the haonan16.github.io-style behavior the user asked for.
- ⚠️ **Dormant code:** the layout still has an `{% if ext == 'mp4' %}<video>` branch
  and the JS still has `playVid()` + an `IntersectionObserver` for autoplaying videos.
  **No post uses MP4 anymore**, so this path never executes. Leave it or delete it, but
  don't be confused into thinking there are videos. (A literal `<video` string also
  appears inside a JS comment — that's not a real element.)

## Media pipeline (how thumbnails were made)

**Important:** the layout renders `src="/tn{{ post.image }}"`. So a post with
`image: /images/spt.gif` actually loads **`/tn/images/spt.gif`**. The thumbnails that
the site shows live in **`tn/images/`** — that folder is what matters. `images/` itself
only holds `profile.jpg` + `favicon.ico` (the full-size figures are not committed/needed).
When you add a paper, drop its thumbnail in `tn/images/` and point `image:` at the
matching `/images/<name>` path.

Originally several demos were MP4s, but autoplay was unreliable (some loaded as a black
first frame / didn't autoplay), so **all motion thumbnails were converted to optimized
GIF** (a GIF is an `<img>` that always animates — no autoplay policy to fight). The
conversion used ffmpeg with a generated palette, then gifsicle to shrink:

```bash
# video -> palette -> gif
ffmpeg -i in.mp4 -vf "fps=12,scale=480:-1:flags=lanczos,palettegen" -y palette.png
ffmpeg -i in.mp4 -i palette.png -lavfi "fps=12,scale=480:-1:flags=lanczos[x];[x][1:v]paletteuse" -y tn/images/name.gif
gifsicle -O3 --lossy=80 --colors 200 tn/images/name.gif -o tn/images/name.gif
```

`height.gif` is the heaviest (~5.9 MB); the others are ~0.6–2.1 MB. If you want to
shave page weight, re-run gifsicle with a lower `--colors` / higher `--lossy`, or drop
fps to 10. Static thumbnails (PNG/JPG) were resized with `sips` (macOS).

## 🚀 Deployment (this repo)

The repo on GitHub is **`Kaiwen-Hong/kaiwen-hong.github.io`** (already renamed from the
original `kaiwenh-homepage`; local remote is updated to the new SSH URL). It deploys as
a **GitHub user site** at the apex URL **`https://kaiwen-hong.github.io`**.

`_config.yml` is set for that: `url: https://kaiwen-hong.github.io`, `baseurl: ""`
(root). Because it's a user site at root, the absolute asset paths (`/tn/images/...`,
`/style.css`) resolve correctly — no baseurl rewriting needed.

### Build method: GitHub Actions with Jekyll 4.3 (NOT "Deploy from a branch")

Deployment runs via **`.github/workflows/deploy.yml`**, which builds with our own
**Jekyll 4.3** (`ruby/setup-ruby` + our `Gemfile`) and publishes with
`upload-pages-artifact` + `deploy-pages`.

**Why not the default "Deploy from a branch" builder?** It uses GitHub's classic
`github-pages` gem = **Jekyll 3.10**, and the build *failed* there. Root cause: on
Jekyll 3.x, `output: false` collection documents are still **rendered through their
layout** (`layout: post` → `default.html`), and that rendering errored. Jekyll 4.3
(what we build/preview with locally) does **not** render `output:false` docs, so the
same site builds cleanly. Pinning Jekyll 4.3 via Actions sidesteps the whole 3.x /
default-theme / default-plugins surface.

Because of this, **`Gemfile` is now committed** (removed from `.gitignore`) — the
workflow needs it. `Gemfile.lock` stays gitignored (the local lock is Ruby-4.0-pinned;
the runner resolves fresh on Ruby 3.3).

**One-time setup the user must do on GitHub:** Settings → Pages → **Source = "GitHub
Actions"** (instead of "Deploy from a branch"). The workflow's `configure-pages`
step tries to flip this automatically (`enablement: true`), but confirm it in the UI.
After that, every push to `main` rebuilds and redeploys; status is in the repo's
**Actions** tab (workflow "Deploy Jekyll site to Pages").

### Inspecting build status without `gh`/token (public repo)

The repo is public, so GitHub Actions results are queryable unauthenticated — handy
when a deploy silently 404s:
```bash
REPO=Kaiwen-Hong/kaiwen-hong.github.io
curl -s "https://api.github.com/repos/$REPO/actions/runs?per_page=5" \
  | grep -E '"name"|"status"|"conclusion"|"html_url"'        # latest run + pass/fail
# error detail for a failed classic build lives in the commit's check-run annotations:
curl -s "https://api.github.com/repos/$REPO/commits/<sha>/check-runs"   # find failed id
curl -s "https://api.github.com/repos/$REPO/check-runs/<id>/annotations"
```

## ⚠️ Still TODO

1. **SPT website** — the top paper (Self-Labeled Preference Transfer) has no `website:`
   (still in submission). Add when available.
2. **arxiv / code links** — most posts only have a project `website:`; `arxiv:` and
   `code:` are blank placeholders. Fill in as papers go public.
3. **Toggle redesign** — the "selected / by date" toggle style is still pending a user
   pick (see the in-progress section above); current style is functional but unloved.
4. *(optional)* **GitHub link in bio** — username is now known (`Kaiwen-Hong`, set in
   `_config.yml` footer-links). The bio's links row (Email / Scholar / CV / LinkedIn)
   does not yet include a GitHub link; add one to `_layouts/default.html` if wanted.
5. *(optional)* **Favicon** — root `favicon.ico` is the template's generic icon and is
   gitignored (not deployed). Replace + un-ignore if you want a custom favicon.

## Local preview (working setup)

Use Homebrew Ruby (4.x), not the system Ruby:

```bash
export PATH="$(brew --prefix ruby)/bin:$PATH"
bundle install
bundle exec jekyll serve     # -> http://127.0.0.1:4000
```

Two machine-specific gotchas already resolved here, in case gems get wiped:

- **Gemfile uses modern Jekyll, not `github-pages`.** The `github-pages` gem pins
  Jekyll 3.x, which cannot run on Ruby 4.x. This only affects *local* preview —
  GitHub Pages builds the live site server-side with its own environment and ignores
  this Gemfile.
- **Broken Command Line Tools libc++ headers.** The native `eventmachine` gem failed
  to compile (`'iostream' file not found`). It builds when you point the compiler at
  the SDK headers:
  `CPLUS_INCLUDE_PATH="$(xcrun --show-sdk-path)/usr/include/c++/v1" bundle install`.
  Permanent fix: `sudo rm -rf /Library/Developer/CommandLineTools && xcode-select --install`.

## File map

```
index.html              # invokes the default layout (don't edit)
_config.yml             # site name, avatar, footer social links, site url (has TODOs)
_layouts/default.html   # THE whole page: bio, links row, Publications loop + toggle JS,
                        #   Honors & Awards (static), footer. Most edits happen here.
_publications/*.markdown # one paper per file; a collection (output:false) — list data only
tn/images/              # the thumbnails the site actually renders (src = /tn/images/<name>)
images/                 # only profile.jpg + favicon.ico (full-size figures not needed)
pdfs/cv.pdf             # the CV the bio links to (copy of Kaiwen_resume_2026.pdf)
style.scss + _sass/     # styling — rarely touched
Gemfile                 # jekyll 4.3 — used by BOTH local serve and the Actions build
                        #   (committed; Gemfile.lock stays gitignored)
.github/workflows/deploy.yml  # builds Jekyll 4.3 and deploys to Pages (see Deployment)
_make_thumbnails.sh     # original helper (not used by the GIF pipeline above)
```

(The `toggle-preview.html` mockup is **not** in this repo — it lives only in the sibling
working dir `../kaiwen-homepage`. The toggle-style pick is still pending; see above.)

### Why a collection (and not `_posts/`)

These markdown files are **data for the homepage list**, not standalone blog posts.
Earlier they lived in `_posts/` with the global `permalink: /`, which made every post
*and* `index.html` all output to the same `/index.html` — Jekyll flagged a hard
**"Conflict ... may end up with unexpected contents"** and the homepage could be
overwritten by a random paper's render on GitHub Pages. Moving them to a
`publications` collection with `output: false` (registered in `_config.yml`) fixes this:
no per-paper pages are written, no conflict, and the layout reads them via
`site.publications | sort: 'date' | reverse` (newest first).

## How to add a publication

Create `_publications/YYYY-MM-DD-shortname.markdown`. The date *prefix* is just for tidy
file listing — **ordering comes from the `date:` field** (the layout sorts newest-first).

```markdown
---
title: "Full Paper Title"
date: 2026-02-01 00:00:00 +00:00  # sort key (newest first); also rendered as the year
image: /images/yourfig.gif        # site loads /tn/images/yourfig.gif — put the file in tn/images/
selected: true                    # optional; shown in default "selected" view + "by date"
authors: "<strong>Kaiwen Hong</strong>, Coauthor*, Coauthor<br><em style='font-size:0.88em; color:#666;'>* Equal contribution</em>"
venue: "Conference on Robot Learning (CoRL)"   # or "Under review"
award: "Best Paper Finalist, CoRL 2026"        # optional; renders bold red. Use <br> for 2 lines.
arxiv:                            # optional
website: https://...             # optional
code:                            # optional
---
One- or two-sentence summary shown under the title (optional; some papers omit it).
```

(No `layout:` or `categories:` needed — the collection is `output: false`, so these docs
are never rendered as their own pages; the homepage layout reads their front-matter.)

Conventions in use:
- Bold Kaiwen's name with `<strong>...</strong>`.
- Mark equal contribution with `*` (and `+` for equal advising) on the names, then add
  the italic footnote line inside `authors` exactly like the example above.
- Any blank link field just doesn't render. Order of rendered links: website / arxiv /
  paper / code / video / poster / slides.
- Put the thumbnail in `tn/images/` (GIF for motion, PNG/JPG for stills).

## Deploy to GitHub Pages

See **🚀 Deployment (this repo)** above — the repo, remote, target apex URL, the
required repo rename, and the Pages toggle steps are all documented there.

## Attribution

Keep the "Design and source code from Jon Barron's website" credit in the footer
(`_layouts/default.html`). `LICENSE` is the original MIT license.

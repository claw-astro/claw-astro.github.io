# Origins Lab site

A custom Jekyll site for an astronomy research group (planet formation,
protoplanetary disks, astrochemistry, exoplanets, radio astronomy),
built to replace the individual-postdoc `academicpages` template with
something meant for a PI's group page. Content editing happens almost
entirely in `_config.yml` and the files in `_data/` — no HTML editing
required for routine updates.

## Installing this into your existing GitHub Pages repo

Your current repo (`claw-astro.github.io`) uses the full academicpages/
Minimal Mistakes theme, with its own `_layouts`, `_includes`, `_sass`,
`_data`, and content collections (`_talks`, `_publications`, `_teaching`,
`_portfolio`, `_posts`). This site replaces all of that with a smaller,
purpose-built set of files. The cleanest way to swap it in:

1. **Clone your existing repo locally** if you haven't already:
   ```
   git clone https://github.com/claw-astro/claw-astro.github.io.git
   cd claw-astro.github.io
   ```

2. **Remove the old theme's files.** Delete everything except `.git`
   and (optionally) `files/` if you want to keep hosting existing PDFs
   at the same URLs:
   ```
   git rm -rf _data _drafts _includes _layouts _pages _portfolio _posts \
     _publications _sass _talks _teaching assets images \
     markdown_generator talkmap _config.dev.yml _config.yml \
     CHANGELOG.md CONTRIBUTING.md Gemfile LICENSE package.json \
     talkmap.ipynb talkmap.py Law_CV_Full.pdf README.md index.html
   ```
   (If any of those don't exist, `git rm` will just skip them — that's fine.)

3. **Drag in every file and folder from this project** — `_config.yml`,
   `Gemfile`, `.gitignore`, `README.md`, `index.html`, `pub_sources.yml`,
   and the `_data`, `_includes`, `_layouts`, `_pages`, `assets`,
   `scripts`, and `.github` folders — into the root of your repo,
   replacing anything with the same name. (`.github/workflows/` may be
   hidden in your file browser since it starts with a dot — make sure
   it comes along too if you want the publications-fetching automation.)

4. **Edit `_config.yml`** — this is the one file almost everything else
   pulls from: your name, university, department, email, and site URL.

5. **Edit the files in `_data/`**:
   - `people.yml` — group members (name, role, focus, optional photo)
   - `publications.yml` — papers
   - `news.yml` — grants, awards, papers, talks
   - `research.yml` — the six "big question" blocks on the homepage
     and `/research/` page
   - `navigation.yml` — the nav bar links, if you want to add/remove pages

   To add a photo, drop the image file in `assets/images/people/` and
   set `avatar: /assets/images/people/yourfile.jpg` on that person's
   entry in `people.yml`. Leaving `avatar` blank shows a plain initial
   circle instead.

6. **Commit and push to your default branch** (`master` or `main`,
   matching what your repo currently uses):
   ```
   git add -A
   git commit -m "Redesign site for the group"
   git push
   ```
   GitHub Pages rebuilds automatically — check the "Pages" section
   under repo Settings if you want to confirm the build succeeded.

## Publications: automatic metadata from ADS (optional)

`_data/publications.yml` — the file the site actually reads — can be
hand-edited like any other data file, or it can be auto-generated
from a list of links using `scripts/fetch_publications.py`. This is
optional; skip this section entirely if you'd rather just edit
`_data/publications.yml` directly.

**Important limitation:** GitHub Pages' native Jekyll build can't
make network calls — it only runs a fixed, sandboxed set of gems. So
this can't happen automatically on every push through Jekyll itself.
Instead, the fetch script runs as a *separate* step (locally, or via
a GitHub Action) that produces a plain data file, which Jekyll then
reads normally, same as if you'd typed it by hand.

**One-time setup:**
1. Get a free ADS API token: sign in at [ui.adsabs.harvard.edu](https://ui.adsabs.harvard.edu),
   then Account Settings → API Token → Generate a new key.
2. In your GitHub repo, go to Settings → Secrets and variables →
   Actions → New repository secret, name it `ADS_API_TOKEN`, and
   paste the token in.

**Adding a paper going forward:**
1. Add its link to `pub_sources.yml` (an ADS abstract URL, bare
   bibcode, arXiv URL/ID, or DOI all work — a publisher/journal page
   does not, since there's no reliable way to scrape those).
2. Push, or go to the Actions tab → "Update publications" → "Run
   workflow". Either way, the workflow fetches title/authors/journal
   from ADS and commits the updated `_data/publications.yml` for you.

**Who gets bolded:** `_data/roster.yml` lists group members and the
year range they were active — an author only gets bolded on papers
published inside their active range, so the byline reflects group
membership at the time, not just current members. Add a line when
someone joins; add an `end_year` when they leave.

**Running it locally instead** (e.g. to preview before pushing):
```
pip install -r scripts/requirements.txt
export ADS_API_TOKEN="your-token-here"
python3 scripts/fetch_publications.py          # writes the file
python3 scripts/fetch_publications.py --dry-run  # preview only
```

## Testing locally before you push (optional but recommended)

```
gem install bundler
bundle install
bundle exec jekyll serve
```
Then open `http://localhost:4000`. Requires Ruby + Bundler installed
(`sudo apt install ruby-dev ruby-bundler` on Debian/Ubuntu, or use
`rbenv`/`rvm` on macOS).

## Adding pages beyond the five included

Each page under `_pages/` is a small Markdown file with YAML front
matter (`layout`, `permalink`, `title`, etc.) — copy `join.md` as a
template for a new plain page, and add it to `_data/navigation.yml`
to link it from the nav bar.

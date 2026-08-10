# abhay.engineer

Personal site for Abhay Tharakan. Jekyll + GitHub Pages, served at
[abhay.engineer](https://abhay.engineer).

Requirements live in [`specs/requirements.md`](specs/requirements.md); the visual
design this implements is [`design/Personal Site.dc.html`](design/).

## Local development

Needs a modern Ruby — macOS's built-in Ruby 2.6 is too old to build Jekyll's
native dependencies.

```sh
brew install ruby
export PATH="/opt/homebrew/opt/ruby/bin:$PATH"

bundle install
bundle exec jekyll serve
# → http://127.0.0.1:4000
```

`Gemfile.lock` is intentionally not committed; see `.gitignore` for why.

## Editing content

Every piece of content lives in a Markdown file. You shouldn't need to touch
HTML or CSS to update the site.

| What | Where |
|---|---|
| Hero name, tagline, intro prose, which project is featured | `index.md` |
| Bio prose, skills, experience, education, honors, timeline | `about.md` |
| Projects page intro | `projects.md` |
| One file per project | `_projects/*.md` |
| Site title, description, URL, contact links | `_config.yml` |

Each file has **front matter** (the `---` block at the top) for structured values
like dates and tags, and a **body** below it where you write prose in normal
Markdown — paragraphs, bullet lists, `**bold**`, links, all of it.

### Adding a project

Create a new file in `_projects/`. The filename becomes its anchor on the
projects page (`pen-plotter.md` → `/projects/#pen-plotter`).

```markdown
---
title: My New Project
order: 1                     # position within its year, lower = first
year: "2027"                 # groups it on /projects/ and in the nav dropdown
period: JAN 2027 — PRESENT   # free text, shown under the title
role: Team of 3              # optional
status: ACTIVE               # optional, shown top-right
tags: [SOLIDWORKS, ARDUINO]  # optional chips
links:                       # optional; omit or leave [] for none
  - label: GITHUB
    url: https://github.com/...
---

The first paragraph is the summary — it's what shows on the Home hero card.

- Bullets after it are the detail, shown only on the projects page.
- Add as many as you like.
```

Year groups and the nav dropdown are both derived from the `year:` values, so a
new year needs no template edit.

To add media, give a project a `media:` block with either `video:` (an embed URL)
or `image:` (a path under `/assets/images/`):

```yaml
media:
  image: /assets/images/plotter.jpg
  alt: The finished pen plotter
```

Without one, the card renders text-only and the Home hero falls back to a hatched
placeholder panel. For the About page portrait, set `photo:` in `about.md`.

To change which project is the Home hero, set `featured:` in `index.md` to that
project's filename without the extension.

> **Front matter gotcha:** don't use `name:`, `slug:`, `url:`, `path:`, `date:`,
> or `content:` as your own keys — Jekyll reserves them and will silently
> override your value.

### Links

Links in a `links:` list, plus the contact links in `_config.yml`, open in a new
tab automatically when they point off-site. Internal links (`/about/`, `#anchor`)
and `mailto:` links stay in the same tab. That's handled by
`_includes/external-attrs.html` — no need to set anything per link.

Links written inside a Markdown body are the exception; they're passed through
as-is. To send one to a new tab, use kramdown's attribute syntax:

```markdown
[Live site](https://example.com){:target="_blank" rel="noopener noreferrer"}
```

## Deployment

Pushing to `main` runs `.github/workflows/pages.yml`, which builds with Jekyll 4
and deploys to Pages.

The native GitHub Pages builder is *not* used: it pins the `github-pages` gem's
Jekyll 3.9 / Liquid 4.0.3 stack, which breaks on Ruby 3.2+ (`Object#tainted?` was
removed), making local development on a current Ruby impossible. Requirements
§4 allows the Actions path for exactly this reason.

One-time setup on the repo:

1. **Settings → Pages → Source**: **GitHub Actions**.
2. **Custom domain**: `abhay.engineer` (the `CNAME` file in this repo sets it).
3. **Enforce HTTPS**: on, once the certificate provisions.
4. **DNS** at your registrar:
   - Apex `A` records → `185.199.108.153`, `185.199.109.153`,
     `185.199.110.153`, `185.199.111.153`
   - `www` `CNAME` → `<username>.github.io`

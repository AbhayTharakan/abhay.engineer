# abhay.engineer — Requirements Specification

## 1. Overview

A personal website for Abhay, hosted at the custom domain `abhay.engineer`. The site is
statically generated with **Jekyll** and deployed via **GitHub Pages**. Scope for v1 is
three pages: **Home**, **About Me**, and **Projects**.

## 2. Goals

- Provide a simple, fast, low-maintenance personal site.
- Present who Abhay is, what he's working on, and a portfolio of projects.
- Keep the toolchain minimal: Jekyll + GitHub Pages, no build servers or paid hosting.
- Make future edits (new project, updated bio) a matter of editing Markdown/YAML, not code.

## 3. Non-Goals (v1)

- No blog/posts collection (structure should not preclude adding one later).
- No comments, newsletter signup, or CMS integration.
- No contact form requiring a backend (a `mailto:` link or links to social/professional
  profiles is sufficient).
- No analytics beyond an optional privacy-friendly script (open question, see §9).

## 4. Hosting & Deployment

| Item | Requirement |
|---|---|
| Repository | Public GitHub repo `abhay/abhay.engineer` |
| Static site generator | Jekyll (GitHub Pages–supported version, or built via GitHub Actions for non-supported gems) |
| Hosting | GitHub Pages |
| Custom domain | `abhay.engineer`, configured via repo `CNAME` file |
| DNS | Apex domain — `A`/`ALIAS` records to GitHub Pages IPs (or `ANAME`), plus `www` `CNAME` if a `www` subdomain is desired |
| HTTPS | "Enforce HTTPS" enabled in repo Pages settings (GitHub-provisioned cert) |
| Build trigger | Push to default branch (`main`) auto-builds and deploys via GitHub Pages |
| CI | If using non-default Jekyll plugins unsupported by GitHub Pages' native build, use a GitHub Actions workflow (`actions/jekyll-build-pages` + `actions/deploy-pages`) instead of the native Pages builder |

## 5. Site Structure

```
/
├── _config.yml
├── Gemfile
├── CNAME                  # contains: abhay.engineer
├── index.md               # Home
├── about.md                # About Me
├── projects.md              # Projects (index/listing)
├── _projects/                # one Markdown file per project (collection)
│   └── project-name.md
├── _layouts/
│   ├── default.html         # shell: header, nav, footer
│   ├── home.html
│   ├── about.html
│   └── projects.html
├── _includes/
│   ├── header.html
│   ├── footer.html
│   └── nav.html
├── assets/
│   ├── css/
│   ├── images/
│   └── favicon
└── .github/workflows/       # only if custom Actions build is needed
    └── pages.yml
```

## 6. Pages — Functional Requirements

### 6.1 Home (`/`)
- Short introduction / tagline (who Abhay is, in one or two sentences).
- Primary navigation to About and Projects.
- Optional: highlight 2–3 featured projects with links to their detail/section.
- Optional: links to social/professional profiles (GitHub, LinkedIn, email).

### 6.2 About Me (`/about`)
- Bio: background, current role/focus, interests.
- Optional: skills/technologies list.
- Optional: resume/CV link (PDF hosted in repo or linked externally).
- Contact links (email, GitHub, LinkedIn, etc.).

### 6.3 Projects (`/projects`)
- Listing of projects, each with: title, short description, tech stack/tags, link
  (live demo and/or GitHub repo), and optionally a date or status (active/archived).
- Content model options (pick one — see open question in §9):
  - **Simple**: hand-written list directly in `projects.md`.
  - **Data-driven**: array in `_data/projects.yml`, rendered via a Liquid loop —
    easiest to keep in sync, no new files per project.
  - **Collection**: Jekyll collection `_projects/` with one Markdown file per
    project — best if individual project pages with longer write-ups are wanted.
- v1 assumption: use the **data-driven** approach (`_data/projects.yml`) rendered
  on a single `/projects` page, since no individual project detail pages are
  required yet.

## 7. Non-Functional Requirements

- **Performance**: static HTML/CSS, no heavy JS frameworks; page weight kept small
  (no large unoptimized images).
- **Responsive design**: usable on mobile, tablet, and desktop viewports.
- **Accessibility**: semantic HTML, sufficient color contrast, alt text on images,
  keyboard-navigable nav.
- **SEO basics**: per-page `<title>`, meta description, Open Graph tags (`jekyll-seo-tag`
  plugin), `sitemap.xml` (`jekyll-sitemap` plugin), `robots.txt`.
- **Browser support**: latest two versions of major evergreen browsers (Chrome,
  Firefox, Safari, Edge).
- **Maintainability**: adding a new project or editing bio content should not require
  touching layout/HTML — only Markdown or YAML data.

## 8. Technical Requirements

- Ruby + Bundler for local development (`bundle exec jekyll serve`).
- `Gemfile` pinned to the `github-pages` gem so local builds match GitHub Pages'
  production environment (unless using the GitHub Actions build path, in which case
  any Jekyll version/plugin is allowed).
- Recommended supported plugins: `jekyll-seo-tag`, `jekyll-sitemap`,
  `jekyll-feed` (if a blog is added later).
- Version control: all source in git; generated `_site/` output excluded via
  `.gitignore` (not committed — GitHub Pages builds it server-side, or the Actions
  workflow builds and publishes it).

## 9. Decisions (resolved during v1 implementation)

1. **Theme** — ✅ Fully custom layout, no third-party theme. Implements the visual
   design in `design/Personal Site.dc.html` (IBM Plex Sans/Mono, cream/teal palette,
   numbered tab navigation) as a hand-written stylesheet in `assets/css/style.css`.
2. **Projects content model** — ✅ Jekyll collection: one Markdown file per project
   in `_projects/`, rendered together on a single `/projects` page grouped by year.
   The collection is `output: false` (no per-project pages), so each project's
   Markdown body is rendered into its card via `markdownify`. Chosen over the
   data-file option so all content is editable as Markdown.
3. **Repo name** — ✅ `abhay.engineer` with `CNAME` + custom domain, so `baseurl` is
   empty.
4. **Analytics** — ✅ None in v1.
5. **Content** — ✅ Sourced from `specs/Abhay.Resume.PDF` into `index.md`, `about.md`,
   and `_projects/*.md`. Prose lives in the Markdown body; structured values (dates,
   tags, skills, timeline) in front matter. Deliberately **excluded**: the phone
   number on the resume (email and LinkedIn are the public contact methods).
6. **Build path** — ⚠️ Changed from the native Pages builder to a GitHub Actions
   workflow (permitted by §4). The `github-pages` gem pins Jekyll 3.9 / Liquid 4.0.3,
   which raises `undefined method 'tainted?'` on Ruby 3.2+ and so cannot be built or
   previewed on a current Ruby. The Actions path runs Jekyll 4.4 instead.

### Still open

- **Media**: no photos or project images/video yet — the About page portrait and the
  Home hero panel render styled placeholders. Drop files into `assets/images/` and
  set `media.image` (or `media.video`) on a project to replace them.
- **Project links**: the resume lists no repo or demo URLs, so `links: []` on every
  project. Cards omit the link row until populated.
- **Major**: "Mechanical Engineering" is taken from the design template — the resume
  doesn't state a major. Worth confirming.
- **www subdomain**: redirect `www.abhay.engineer` → `abhay.engineer` (or vice versa)?

## 10. Acceptance Criteria

- [ ] Visiting `https://abhay.engineer` loads the Home page over HTTPS.
      *(Pending DNS + repo Pages setup — see README.)*
- [x] Home, About, and Projects pages are reachable via consistent site navigation.
- [x] Site builds successfully (`bundle exec jekyll build`, clean, 0 warnings);
      Actions workflow committed at `.github/workflows/pages.yml`.
- [x] Site renders correctly on mobile and desktop viewports — verified at 390px
      and 1280px; `scrollWidth == viewport` on all three pages (no horizontal scroll).
- [x] Adding a new project requires only a data/content change, no template edits —
      year groups and the nav dropdown are derived from `_data/projects.yml`.
- [x] `sitemap.xml` and basic SEO meta tags are present (title, description,
      canonical, Open Graph via `jekyll-seo-tag`).

# CAOS Site Requirements

*Last updated: September 2026. Items marked ✅ are complete; items marked 🔲 are still pending.*

## Core Requirements

- ✅ Use Jekyll with GitHub Pages
- ✅ Excellent accessibility: visual contrast, font simplicity and size; screen reader structural markup with headings and landmarks; keyboard navigation; programmatic focus management on page load. Usability is more important than compliance.
- ✅ Simple, professional, authoritative visual design
- ✅ Management and maintenance uses GitHub and open-source mechanisms; site management and form responses should be easy and low attention.

## Site Map

### Primary Pages

- ✅ CAOS Home
- ✅ About CAOS
- ✅ Projects of Interest
- ✅ Resources
- ✅ Governance
- ✅ Support CAOS
- ✅ Contact
- ✅ Blog
- ✅ Calendar
- ✅ Accessibility Statement
- ✅ Policy Library (child of Governance; served from the governance repo submodule — see Governance Repository Integration). Retires the standalone Bylaws and Conflict of Interest Policy pages, which have moved into the Policy Library alongside other policies.

### General Wire Frame — applies to all pages

- ✅ Page title — Center for Accessibility and Open Source — [current page]
- ✅ Navigation bar — tab-style major page links with current page indicated visually and to screen readers (`aria-current`)
- ✅ Skip to main content link
- ✅ Breadcrumbs for child pages
- ✅ H1 page title rendered by layout; H2 as first content heading
- ✅ Screen reader focus initializes on first H2 on page load
- ✅ Footer: accessibility statement, contact, donate, license (social media links were removed to reduce clutter)
- ✅ Footer: "Suggest a change" / "Send a suggestion" feedback links (see "Submit an Issue" Footer Link below)

---

## Page Structures

### Home

- ✅ H2 succinct mission statement
- ✅ 2-sentence description of CAOS
- ✅ CAOS Blog preview (3 most recent posts, link to full blog)
- ✅ CAOS Calendar preview (upcoming events, link to full calendar)
- ✅ Newsletter subscribe form (Buttondown)

### About CAOS

- ✅ Brief statement: open-source organization led by people with disabilities to promote open-source accessibility
- ✅ Mission statement
- ✅ Short paragraph on CAOS origin
- ✅ Board member bios with headings
- ✅ Board member photos and image descriptions (see "Leadership Bios & Photos" below). Elizabeth Ruhland's image description is still a placeholder ([caostest#25](https://github.com/center4aos/caostest/issues/25))
- ✅ Advisory board description
- 🔲 Advisory board member list

### Resources

- ✅ Page structure, headings, and framing text in place
- 🔲 Actual resource listings with one-sentence descriptions (accessibility standards, open-source tools, disability/tech communities, learning/training)

### Governance

- ✅ 501(c)(3) statement
- ✅ Transparency statement
- ✅ Single link to the Policy Library, replacing the old standalone Bylaws and Conflict of Interest Policy links, pointing into the governance repository submodule (see Governance Repository Integration). The standalone `bylaws.md` and `conflict-of-interest.md` pages have been removed.
- 🔲 Form 990 link
- 🔲 Board meeting schedule

### Contact

- ✅ Page structure and framing text
- ✅ Contact form fields: name, email, subject, topic dropdown, message body
  - Topics: Seeking advice, Partnership proposal, Seeking support, Media inquiry, Advisory Board, Donation
- ✅ Contact form backend configured
- ✅ Newsletter signup embed on contact page
- 🔲 Newsletter signup backend (Buttondown)
- ✅ Snail mail address

### Support CAOS

- ✅ Mission-framed donation intro
- ✅ PayPal, Venmo, Benevity, planned giving language, check instructions (Zelle was removed)
- ✅ Benevity search instructions (legal name and EIN) and link to the cause profile ([caostest#1](https://github.com/center4aos/caostest/issues/1)). Benevity lists CAOS under its legal name, not "CAOS"
- ✅ giving@caos.org contact
- ✅ Mailing address
- ✅ EIN (39-2978169)

### Projects of Interest

- ✅ Framing text about partnership priorities and strategic plan
- ✅ CREATE (UW)
- ✅ Teach Access
- ✅ NV Access / NVDA
- 🔲 Additional partners as relationships develop

---

## Technical Implementation Notes

- **Internal links:** Always use `{{ "/path/" | relative_url }}` — never hardcode `/caostest/` or bare root-relative paths, so links survive migration to domain root.
- **Blog:** `_posts/YYYY-MM-DD-title.md` with `layout: post`
- **Events:** `_events/YYYY-MM-DD-title.md` with `layout: event`; past events drop off calendar automatically on each build
- **Forms:** contact form posts to Formspree; newsletter signup posts to Buttondown, with custom confirmation pages (`subscribe-confirm.md`, `subscribe-thanks.md`)
- **GitHub Pages:** Legacy build, `baseurl: /caostest`; migration to domain root requires changing `baseurl: ""` in `_config.yml`
- **Repo identity:** Add `repo: center4aos/caostest` to `_config.yml` as the single source of truth for the main repo's GitHub path (used by the footer/registry lookup below). **Verified:** grepping `_includes`/`_layouts` today turns up zero hardcoded occurrences of `caostest`/`center4aos` — the only places that string appears are `_config.yml`'s `baseurl` (a separate setting, governing URL paths not repo identity) and `README.md` (excluded from the build, plain dev documentation). So as long as templates only ever read `site.repo` and never hardcode the literal name, one value is genuinely sufficient here — this isn't an uncertain platform behavior, just an implementation-discipline check.
- **Migration checklist (one-time, do together):** when the repo is eventually renamed and the site moves to its permanent domain, update in the same pass: `baseurl` (above), `repo` (this line), `README.md`'s hardcoded live-site URL and org links, the `CNAME` file currently living in `center4aos.github.io` (needs to be added to the root of the renamed repo, formerly caostest, so the custom domain keeps resolving to it), and the `repository:` target hardcoded in `governance`'s `notify-site.yml` dispatch workflow (see "Automation design" under "Governance Repository Integration" below — the dispatch token itself survives the rename since GitHub tracks PAT permissions by repo ID, not name, but the hardcoded target name still needs updating). GitHub's own repo-rename redirects are a safety net if any of these lag briefly, but shouldn't be relied on long-term.

---

## Governance Repository Integration

### Decisions

- **Mount path:** The submodule mounts at `/governance/library/`, *not* `/governance/` — [`governance.md`](governance.md) already exists as a real page there (501(c)(3) statement, Form 990, board meetings) and would collide with a submodule mounted at the same path. `governance.md` is unchanged except for a new link into the Policy Library.
- **Scalable registry:** All external-repo metadata lives in one data file, `_data/external_repos.yml`, keyed by a short identifier. `_config.yml` `defaults:` blocks only tag a path with that key (`external_repo: governance`); footer and breadcrumb includes look up the rest. Adding a second external repo later means one data-file entry + one config scope block — no template or Liquid changes.
- **Navigation:** No top-level nav entry for `/governance/library/` or any future external mount. Reachable only via a link from an existing page (here, `governance.md`) plus breadcrumbs/direct URL. This is a standing policy for all future external-repo integrations, not a governance-specific exception.
- **Automation:** Submodule updates are automated via GitHub Actions from day one, using a two-part pattern designed to scale to additional repos without touching central logic (see below).

### `_data/external_repos.yml` schema

```yaml
governance:
  path: governance/library      # where the submodule is mounted in this repo; also the site sub-path
  repo: center4aos/governance   # GitHub repo the submodule tracks, and where issues are filed
  ancestors:
    - title: Governance
      url: /governance/
    - title: Policy Library
      url: /governance/library/
```

An optional `issue_template` field was added later (see "Issue templates are each repo's own concern" below). Governance deliberately leaves it unset, so GitHub's own template chooser applies.

### Setup steps

1. `git submodule add https://github.com/center4aos/governance governance/library`
2. Add the entry above to `_data/external_repos.yml`.
3. Add to `_config.yml`:
   ```yaml
   defaults:
     - scope:
         path: "governance/library"
       values:
         layout: page
         external_repo: governance
   ```
4. Extend `_includes/breadcrumbs.html`, `_includes/footer.html`, and `_layouts/page.html`/`default.html` to look up `page.external_repo` in `site.data.external_repos` and `site.data.external_repo_pages` (patterns below and in "External Repo Page Index & Titles").
5. Add `_external_repo_pages/governance.md` in this repo (not inside the submodule) with `permalink: /governance/library/`, looping over the generated page-index data to render the listing — see "External Repo Page Index & Titles" below.
6. Update `governance.md` to link to the new Policy Library.

### Footer/breadcrumb lookup pattern

```liquid
{% assign ext = site.data.external_repos[page.external_repo] %}
{% assign repo = ext.repo | default: site.repo %}
```

`ext.ancestors` supplies `page.ancestors` for every document in this repo — see "Breadcrumb Architecture" below for the full mechanism, which replaces the old single `parent:`/`parent_url:` frontmatter model entirely (that model is retired along with `bylaws.md`/`conflict-of-interest.md`, the only two pages that used it).

### Automation design (scalable)

Two-part GitHub Actions pattern so adding a new external repo never touches the central update logic:

1. **In each external repo** (governance, and any future one) — a small workflow, copied and parameterized per repo, firing on push to `main`:
   ```yaml
   # governance repo: .github/workflows/notify-site.yml
   on:
     push:
       branches: [main]
   jobs:
     notify:
       runs-on: ubuntu-latest
       steps:
         - uses: peter-evans/repository-dispatch@v3
           with:
             token: ${{ secrets.SITE_DISPATCH_TOKEN }}
             repository: center4aos/caostest
             event-type: submodule-updated
             client-payload: '{"path": "governance/library", "key": "governance"}'
   ```
2. **In caostest** (this repo) — one reusable workflow handling the dispatch for any repo:
   ```yaml
   # .github/workflows/update-submodule.yml
   on:
     repository_dispatch:
       types: [submodule-updated]
   jobs:
     update:
       runs-on: ubuntu-latest
       steps:
         - uses: actions/checkout@v4
           with:
             submodules: recursive
             token: ${{ secrets.GITHUB_TOKEN }}
         - run: |
             git submodule update --remote ${{ github.event.client_payload.path }}
             ruby script/sync-external-repo-pages.rb --key ${{ github.event.client_payload.key }} --path ${{ github.event.client_payload.path }}
             git config user.name "github-actions[bot]"
             git config user.email "github-actions[bot]@users.noreply.github.com"
             git add ${{ github.event.client_payload.path }} _data/external_repo_pages/${{ github.event.client_payload.key }}.yml
             git commit -m "Update ${{ github.event.client_payload.path }} submodule" || echo "No changes"
             git push
   ```
   `sync-external-repo-pages.rb` is the single script that both extracts page titles and skips `_`/`.`-prefixed files and folders in one pass — see "External Repo Page Index & Titles" below. Nothing here touches `_config.yml`.

Adding a second external repo later means copying the ~10-line trigger workflow into that repo with its own `path`/`key` values and a dispatch-token secret — nothing in caostest's workflow changes.

### Requirement discovered by testing: governance docs need a minimal empty frontmatter marker

**Verified, and this changes a stated assumption:** using `bundle exec ruby` to load this repo's actual site (Jekyll 3.9.3) with a test `defaults:` scope block, a file with *zero* frontmatter markers is classified by Jekyll as a **static file**, not a page — it gets copied to the output directory untouched, with no layout, no nav/footer, no breadcrumb, and none of the `_config.yml` `defaults:` values (`layout`, `external_repo`) applied at all. A file with only an *empty* frontmatter block (`---` / `---`, no fields) is correctly picked up as a real page with defaults applied exactly as designed.

So "governance markdown files need no frontmatter" (stated earlier as a benefit of the `defaults:` approach) isn't quite right — every document in the governance repo needs at minimum the two-line empty frontmatter delimiter to be treated as a page at all:
```
---
---
# Actual document content starts here
```
This costs little against the original "keep them clean for non-web use" goal: GitHub's own markdown renderer already recognizes and hides YAML frontmatter blocks, even empty ones, so this is invisible when viewing the file directly in the governance repo's GitHub UI — it only affects Jekyll's own file classification. This is a new, small requirement on the governance repo's authoring convention, not something caostest's automation can paper over (rewriting files inside a pinned submodule commit was already ruled out earlier as fragile).

(Exclusions are now handled by Jekyll's native `_`/`.`-prefix convention — see "External Repo Content Exclusions" below — rather than a dedicated exclusions file, but the file tested at the time, `web-exclusions.md`, correctly confirmed the same underlying point: a file with no frontmatter is never treated as a page.)

**Re-verified more rigorously with a real submodule:** the initial test above used plain directories standing in for a submodule. As a follow-up, a genuine throwaway git repo was committed and added as an actual git submodule (confirmed via `git ls-files -s` showing a real `160000` gitlink), then a full `site.process` build (not just `site.read`) was run and the generated HTML inspected directly. Both a top-level and a nested-one-folder-deep document — each with only the minimal empty frontmatter — rendered as complete pages: full header, nav bar, breadcrumb shell, footer, and correctly converted markdown content, identically to any ordinary page. This confirms the mechanism works through an actual submodule, at multiple nesting depths, with real end-to-end rendering — not just correct data classification. (It also surfaced a related gap in the title-fallback mechanism — see "Title fallback for individual documents" below.)

---

## External Repo Content Exclusions

### Purpose

An external repo mounted into the site (governance, or any future one) may contain files that shouldn't clutter the site as browsable pages — issue templates, policy-drafting templates, and similar ancillary documents. **This is a curation concern, not a privacy one:** every mounted repo is fully public; anything genuinely non-public stays in a separate repo that is never mounted onto the website at all.

### Decision: no custom exclusions file — use Jekyll's own underscore/dot convention

**Verified by testing:** a file or directory whose name starts with `_` or `.` is invisible to Jekyll's build entirely — not rendered as a page, not even copied as a static file — confirmed empirically at a nested depth inside an actual mounted submodule path (not just at the site root). This was also checked for a specific collision risk: a folder nested deep inside the mount and literally named `_data` (colliding with Jekyll's own special top-level directory name) was *not* mistakenly picked up as `site.data` — Jekyll's special-directory readers only look at the fixed top-level locations relative to the site root, so a governance contributor naming a folder `_data` or `_posts` for their own reasons triggers no surprising behavior; it's just skipped like any other underscore-prefixed folder.

This is stronger than the exclusion originally planned (which only needed to keep something out of the *browsable listing*, not out of the build) and it requires no custom file, format, or parsing logic at all. **The governance repo convention is simply: prefix any file or folder you don't want on the website with `_` or `.`** — e.g. `_internal-notes/`, `_2026-06-strategy-draft.md`. This applies uniformly to every entry in `_data/external_repos.yml`, not just governance, and needs no documentation beyond "this is a standard Jekyll convention."

One trade-off worth naming: this is less self-documenting than a dedicated exclusions file would have been — there's no single place to see everything excluded with a reason attached, just folder/file names that are hopefully self-explanatory on their own.

### How it's applied

Jekyll's own build already handles this natively — no `_config.yml` involvement needed. The one thing that does need updating: `script/sync-external-repo-pages.rb` (see "External Repo Page Index & Titles" below) does its own independent filesystem walk to build the title/listing data, separate from Jekyll's own build, so it needs to skip `_`/`.`-prefixed entries itself using the identical rule — otherwise the generated listing could link to a document Jekyll itself won't render. Because both places enforce the exact same simple rule, they can't drift out of sync with each other the way a separately-parsed blacklist file could have.

---

## External Repo Page Index & Titles

### Purpose

Jekyll doesn't auto-generate directory listings — a URL like `/governance/library/` needs an actual page to render, or it 404s. And since submodule content deliberately carries no Jekyll frontmatter (kept clean for non-web use), individual documents have no `page.title` for their own H1, browser-tab title, or breadcrumb either. Both problems are solved by one generated data file per external repo, produced by the same automation that bumps the submodule pointer.

### Generated data file — hierarchical, mirroring directory structure

**Decision (superseding the earlier nested-list design):** the listing uses a hierarchical **heading structure with lists as content**, not nested `<ul>` lists. The page's H1 (from the layout) is the mount's title (e.g. "Policy Library"); immediately below the nav bar, an H2 repeats that same title as the first content heading — matching the exact convention already used everywhere else on the site (e.g. `governance.md`'s `# Governance` / `## Governance`), which is also what the "focus jumps to first H2 on page load" accessibility behavior depends on. Each directory becomes a heading one level deeper than its parent (H3 for directories at the mount root, H4 for their subdirectories, and so on), followed by a plain list of links to the files directly in that directory. A repo with no subdirectories produces just the H2 with a flat file list beneath it — no directory headings at all.

**Heading-depth cap:** only H3–H6 are available for directory nesting (H1/H2 are already spoken for), so a maximum of four folder levels can get their own distinct heading. Beyond that, nesting continues to render as H6 rather than erroring — a practical limit expected to comfortably cover a policy library, not a hard architectural ceiling.

**Correct handling of directories with no direct files but non-empty descendants:** omitting a directory's heading whenever it happens to have no files *directly* in it would skip a heading level for whatever's nested beneath it (e.g. jumping from H2 straight to H4) — a real accessibility defect, since screen reader users navigate by heading level and rely on levels never being skipped. So "empty" is defined recursively: a directory is only pruned from the tree (and omitted from the page) if it and *all* its descendants contain zero files. A directory with no direct files but a non-empty subdirectory is kept in the tree and still gets its own heading rendered — just with no file list directly under it, falling straight through to its subsection's heading — to keep the nesting correct.

`script/sync-external-repo-pages.rb` (run by `update-submodule.yml` after each submodule bump) walks a repo's mounted files recursively and builds a tree, skipping any file or folder whose name starts with `_` or `.` (matching Jekyll's own native exclusion rule — if a whole directory is skipped this way, its subtree isn't walked at all; a directory is pruned per the recursive "empty" rule above, not merely for lacking direct files). Each node is either:
- a **file** node: `{type: file, title, path, url}` — title from the file's first `# H1` line (if multiple H1s exist, the first one wins; later ones are ignored), falling back to a filename-derived title (e.g. `strategic-plan-2026.md` → "Strategic Plan 2026") if no H1 is found. The extracted heading line is run through Kramdown (already a site dependency) and the resulting HTML tags stripped, so inline formatting in the heading — links, bold/italic, code spans — resolves to plain readable text (`# [Bylaws](#anchor)` → "Bylaws") instead of leaking raw markdown syntax into the H1, breadcrumb, tab title, and listing.
- a **directory** node: `{type: directory, name, children: [...]}` — `name` is a humanized version of the folder name (same filename→title transform as above: hyphens/underscores become spaces), `children` is the same node structure, recursively. **Capitalization rule:** only words that are entirely lowercase get their first letter capitalized; any word that already has a capital letter is left untouched. This handles acronyms for free without a maintained dictionary — naming a folder `COI-amendments` produces "COI Amendments," while `coi-amendments` produces "Coi Amendments" — giving governance authors an implicit way to control the exact display name just by how they capitalize the folder name, no extra config or frontmatter needed. (Minor words like "of"/"the" are not specially lowercased — not worth the added complexity for a small, curated policy library.)

The result is written to `_data/external_repo_pages/<key>.yml` (e.g. `_data/external_repo_pages/governance.yml`), keyed the same way as `_data/external_repos.yml`. The file has two top-level keys: `tree` (the nested node structure above, used only by the listing page's recursive render) and **`flat`**, a simple `{relative_path: title}` map covering every included file, used for the per-page lookups below — a nested tree is awkward for Liquid to search when all it needs is "the title for this one page," so the same generation pass also emits the flat form to make that a single lookup instead of a recursive walk.

### The listing/index page

The hand-written index page for a mount (e.g. `/governance/library/`) can't physically live inside the submodule's own path — a submodule mount claims that entire directory as a gitlink, so a regular file from caostest can't coexist there. Instead it lives in **this repo**, e.g. `_external_repo_pages/governance.md`, using Jekyll's `permalink: /governance/library/` frontmatter to render at the mount's URL regardless of physical location.

It renders the tree via a **recursive Jekyll include** (`_includes/external-repo-listing.html`) that calls itself once per directory level, tracking the current heading level as it descends. Files at the current level are listed first (per the agreed ordering — a level's own files before its subsections), then each subdirectory's heading and contents follow:

```liquid
<!-- _includes/external-repo-listing.html — expects include.nodes (array) and include.level (heading level for directories found at this depth) -->
{% assign files = include.nodes | where: "type", "file" %}
{% assign dirs = include.nodes | where: "type", "directory" %}

{% if files.size > 0 %}
<ul>
  {% for file in files %}
    <li><a href="{{ file.url | relative_url }}">{{ file.title }}</a></li>
  {% endfor %}
</ul>
{% endif %}

{% for dir in dirs %}
  {% assign heading_level = include.level | at_most: 6 %}
  <h{{ heading_level }}>{{ dir.name }}</h{{ heading_level }}>
  {% assign next_level = include.level | plus: 1 %}
  {% include external-repo-listing.html nodes=dir.children level=next_level %}
{% endfor %}
```

called from the index page itself, right after the H2, with `{% include external-repo-listing.html nodes=site.data.external_repo_pages.governance.tree level=3 %}` — starting at level 3 since H1/H2 are already used by the page's own title. This one recursive include is shared by every external repo mount — a repo with deeply nested folders needs no extra per-directory page or template; the whole tree, at any depth (up to the H6 cap above), renders on the single mount-root listing page. Always current, no manual edit needed when documents are added, renamed, or removed.

**Standing convention:** external repos (governance, and any future one) must not include their own `index.md` at the root of their mounted content — that would collide with this permalink-targeted page's output.

**Standing rule:** this index page's own frontmatter must never set `external_repo: governance` (or any registry key), even though it's "about" governance content. Left unset, `page.external_repo` naturally stays nil for it (since it physically lives outside the `governance/library` path that the `_config.yml` defaults scope matches), so the "Suggest a change" footer link on this page correctly falls back to `site.repo` (caostest) — a bug in the listing page itself is caostest's responsibility, not the governance repo's. See "Submit an Issue Footer Link" below.

### Title fallback for individual documents

**Verified end-to-end by testing:** a full site build against a real (throwaway) git submodule, with only the minimal empty-frontmatter files described above, confirmed the whole pipeline renders correctly — real header/nav/footer/breadcrumb shell, markdown converted to HTML, at both a top-level and a nested-one-folder-deep path. But it also rendered two elements empty, `<h1 class="post-title"></h1>` and the breadcrumb's current-page `<span aria-current="page"></span>`, because `page.title` is nil without frontmatter — confirming the fallback below isn't optional polish, it's required for these pages to be usable at all.

`_layouts/page.html` (or `default.html`) **and** `_includes/breadcrumbs.html` both need the identical fallback: look up a page's title from `site.data.external_repo_pages[page.external_repo].flat[relative_path]` — `relative_path` being `page.path` with the registry's mount `path` prefix stripped — whenever `page.external_repo` is set and `page.title` is blank. Ordinary site pages, which always carry their own `title:` frontmatter, are unaffected. (The original design only named `_layouts/page.html`/`default.html`; testing showed breadcrumbs.html reads `page.title` independently for its current-page crumb and needs the same lookup, or that crumb stays blank even after the H1 is fixed.)

### SEO/browser-tab title — decided and tested

`jekyll-seo-tag` (the `{% seo %}` tag generating `<title>` and Open Graph metadata) reads `page.title` directly at the Ruby level, before any of our Liquid template logic runs — a `{% assign %}` in our own layout can't retroactively change what that plugin sees. Without a fix, every governance document's browser tab, bookmark default name, and social-share preview would show the generic site title instead of the document's own — a real usability problem, not just cosmetic (screen reader users switching browser tabs rely on the tab title being announced to tell tabs apart).

**Decision:** bypass `{% seo %}` for external-repo pages specifically and hand-roll the same tags using the resolved title from `_data/external_repo_pages/<key>.yml`'s `flat` map — no new frontmatter requirement on any content file, since it reuses the exact same title data already generated for the H1/breadcrumb fallback.

**Verified by testing:** built and tore down a temporary version of this exact change in `_layouts/default.html`, wrapping the existing `{% seo %}` call in a conditional:
```liquid
{% if page.external_repo %}
  {% assign ext = site.data.external_repos[page.external_repo] %}
  {% assign mount_path = ext.path | default: "" %}
  {% assign relative_path = page.path | remove_first: mount_path | remove_first: "/" %}
  {% assign resolved_title = site.data.external_repo_pages[page.external_repo].flat[relative_path] | default: site.title %}
  <title>{{ resolved_title }} | {{ site.title | escape }}</title>
  <meta name="description" content="{{ site.description | escape }}">
  <meta property="og:title" content="{{ resolved_title | escape }}">
  <meta property="og:description" content="{{ site.description | escape }}">
{% else %}
  {% seo %}
{% endif %}
```
Building a test page through this produced `<title>Bylaws | Center for Accessibility and Open Source</title>` correctly, with no `title:` frontmatter anywhere in the source file. An ordinary page (`about.md`) built alongside it was unaffected, still producing the full normal `{% seo %}` output (canonical URL, Twitter card, `og:locale`, etc.) — confirming no regression to any existing page.

No open questions remain for this section — H1 extraction (Kramdown-based, first-H1-wins) and directory-name humanization (capitalize only fully-lowercase words) are both decided above.

---

## Directory URL Redirects for External Repo Mounts

### Purpose

The listing page renders an external repo's whole tree on a single page at the mount root (e.g. `/governance/library/`) — by design, there are no separate pages for its subdirectories (see "Decision (superseding the earlier nested-list design)" above). That means a URL to a subdirectory itself — e.g. `/governance/library/committees/`, guessed, bookmarked from an old link, or typed by hand — 404s, even though `committees` is a real folder in the mounted repo. A URL to a path that doesn't exist at all (a typo, or a document that was actually removed) should still 404 normally. The two cases need to be told apart, not treated the same way.

### Decision: `jekyll-redirect-from`, driven by the same tree walk

**Verified installed:** `jekyll-redirect-from` (0.16.0) ships as part of the `github-pages` gem already in this repo's `Gemfile` — the same free-lunch mechanism that already gives per-document titles via `jekyll-titles-from-headings` (see "Title fallback for individual documents" above) without any custom code.

Giving a page a `redirect_from:` frontmatter array causes the plugin to generate a small standalone redirect page (meta-refresh + canonical link + JS fallback) at each listed URL, pointing back to the page carrying the list — generated at build time only, never committed as separate files. Applied here: the listing page (`_external_repo_pages/governance.md`) carries a `redirect_from:` entry for every directory path in the mount's tree, at every depth:

```yaml
---
permalink: /governance/library/
ancestors:
  - title: Governance
    url: /governance/
redirect_from:
  - /governance/library/committees/
  - /governance/library/policies/
  - /governance/library/meeting-notes/
  # ...one entry per directory node in the tree, any depth
---
```

This gives exactly the two behaviors wanted: a real directory redirects (a page was generated for it), and a genuinely missing path was never generated, so it falls through to GitHub Pages' ordinary 404 — no manifest-matching logic, no client-side detection, nothing to get wrong. It also beats a hand-rolled 404.html-based redirect (considered and rejected — briefly implemented and reverted on PR #15) on accessibility grounds: no error-page flash before a JS redirect fires, and it degrades gracefully without JS via the meta-refresh.

### Generation mechanics

`script/sync-external-repo-pages.rb` (see "External Repo Page Index & Titles" above) already walks the mount's tree to build `_data/external_repo_pages/<key>.yml`. The same walk supplies the list of directory paths for `redirect_from`. Because the listing page is otherwise hand-authored (its own `ancestors:`, `permalink:`), the script must surgically merge/replace just the `redirect_from:` key in that page's frontmatter rather than regenerating the whole file — parse the frontmatter, update that one key, rewrite, leaving everything else untouched.

### Scope

Applies uniformly to every external-repo mount, keyed the same way as the rest of the registry — no template or per-mount logic beyond the one `redirect_from:` list per mount's listing page.

---

## Breadcrumb Architecture

### Decision

Breadcrumbs use a general, arbitrary-length **ancestors array** instead of the old single `parent:`/`parent_url:` frontmatter pair. This resolves the previously open "breadcrumb depth" question outright.

**Key modeling choice:** for content mounted from an external repo, the repo's own listing page is the parent of every document in that repo, regardless of how deeply the document's file actually sits in the source repo's folder structure. This matches the real *page* hierarchy — there is only one browsable page per external-repo mount, since the listing page renders the whole tree recursively on itself — rather than the source repo's internal file layout, which has no pages of its own to be a parent of anything. So every document in a given external repo gets the same fixed-length ancestor chain; nothing about breadcrumbs requires the page-index generator script to compute anything.

### Rendering

```liquid
<!-- _includes/breadcrumbs.html -->
{% unless page.url == "/" %}
<nav class="breadcrumb" aria-label="Breadcrumb">
  <ol>
    <li><a href="{{ "/" | relative_url }}">Home</a></li>
    {% for a in page.ancestors %}
      <li><a href="{{ a.url | relative_url }}">{{ a.title }}</a></li>
    {% endfor %}
    <li><span aria-current="page">{{ page.title }}</span></li>
  </ol>
</nav>
{% endunless %}
```

`page.ancestors` is an array of `{title, url}` pairs. Four sources feed it, all through the same rendering logic:

| Content type | Source of `ancestors` |
|---|---|
| Ordinary hand-authored pages (e.g. the Policy Library listing page) | Own frontmatter |
| Blog posts | `_config.yml` defaults, scoped by collection `type: posts` |
| Calendar events | `_config.yml` defaults, scoped by collection `type: events` |
| Individual documents from an external repo | `_data/external_repos.yml` registry entry's `ancestors:` list (fixed per repo, from "Governance Repository Integration" above) |

### `_config.yml` additions for blog and calendar

```yaml
defaults:
  - scope:
      type: posts
    values:
      ancestors:
        - title: Blog
          url: /blog/
  - scope:
      type: events
    values:
      ancestors:
        - title: Calendar
          url: /calendar/
```

Every post and event gets its ancestor automatically — no per-file frontmatter needed. `_events/2026-07-15-board-meeting.md`'s existing hand-set `parent: Calendar` / `parent_url: /calendar/` fields are removed as part of this change, superseded by the collection-level default; future event files never need to set them at all.

### The Policy Library listing page's own breadcrumb

The listing page (`_external_repo_pages/governance.md`) is one level shallower than the documents it lists — it *is* the "Policy Library" step, not a child of it. It declares its own ancestors directly in frontmatter, using the registry's list minus its own final entry:

```yaml
---
ancestors:
  - title: Governance
    url: /governance/
---
```

giving Home → Governance → [Policy Library, current page]. No special-casing in the Liquid — it's an ordinary hand-authored page supplying its own `ancestors`, same as any other.

### Retired: `bylaws.md` / `conflict-of-interest.md`

Per the Site Map and Governance page-structure updates above, these two pages have been removed from the main site, replaced by a single link from `governance.md` into the Policy Library (which contains both documents, plus others, inside the governance repo). They were also the only two pages using the old `parent:`/`parent_url:` fields, so `breadcrumbs.html` needs no backward-compatibility branch for that schema — the ancestors-array model is the only mechanism, from day one.

---

## "Submit an Issue" Footer Link

### Decisions

- **Placement — amended:** Still lives in the existing utility footer column (with Accessibility Statement / Contact / Donate), but as its own visually distinct sub-group rather than folded into that list. The two-path Q&A structure below (decided this round) is more text than a single link, so it gets a small bold label — now "**Do you have feedback?**" — directly above its own short list, reusing the exact `<p><strong>...</strong></p>` pattern the footer already uses for the "Connect" label. This keeps the existing Accessibility/Contact/Donate list uncluttered while still avoiding a brand-new footer column.
- **Label:** Uniform **"Suggest a change"** site-wide (not varied per repo) for the GitHub-issue path, with an explicit `aria-label="Suggest a change (opens a GitHub issue in a new tab)"`. The site-wide external-link script in `_layouts/default.html:47-58` only sets a generic `(opens in new tab)` aria-label when one isn't already present, so this manual, more specific label is respected; `target="_blank"` / `rel="noopener noreferrer"` are still applied automatically since github.com is a different hostname.
- **Repo source:** Driven by the `_data/external_repos.yml` registry above — `page.external_repo` is looked up, falling back to `site.repo` for ordinary site pages.
- **Filename accuracy:** The referenced filename in the issue body has the registry's mount `path` prefix stripped before use, so it names a file that actually exists in the *target* repo rather than a caostest-only mount path (e.g. `conflict-of-interest-policy.md`, not `governance/library/conflict-of-interest-policy.md`). This also means the generated listing/index page itself (which never carries `external_repo` — see "External Repo Page Index & Titles") always reports its true caostest path and opens its issue against caostest, which is correct since that page is caostest's own code.
- **Automatic template pickup:** The link deliberately omits a `template=` query param. Instead of us tracking a specific template filename per repo (which would go stale the moment that repo's maintainers rename or add a template), the link points at plain `.../issues/new` with `title=`/`body=` params. GitHub itself then applies whatever templates actually exist in the target repo at click-time: opens the single template directly if there's exactly one, shows its own template chooser if there are several, or a blank form if there are none — so it "automatically pulls from any issue templates the repo contains" without our config needing to know their names.
- **Non-GitHub fallback — copy finalized:** Two short lines, each a static lead-in question followed by its own link, rather than one link carrying all the text: "Got a GitHub account? **Suggest a change**" and "No GitHub account? **Send a suggestion**." Only the bolded words are inside the `<a>` tag; the questions are plain static text, keeping each line short despite there being two of them. The second link points to `/contact/?context=...` with the same page/repo reference URL-encoded; the contact page reads that query param on load, populates a new hidden form field (`page_context`), and pre-selects a new contact-form topic option, **"Website feedback or issue report,"** added to the existing dropdown in `contact.md`.

### Implementation

The live code is in `_includes/footer.html`; it isn't duplicated here, so it can't drift out of date. In outline:

- `repo` and an optional `issue_template` come from the `_data/external_repos.yml` entry for `page.external_repo`, falling back to `site.repo` and `site.issue_template` for ordinary site pages.
- `relative_path` is `page.path` with the mount path stripped.
- The page's full URL is `page.url | absolute_url`. This includes `baseurl`, so it's correct both at `/caostest/` and after migration. An earlier version used `site.url | append: page.url`, which dropped `/caostest` and produced 404 links in reported issues (fixed in caostest#26).
- The issue body starts with "Please Describe your issue or suggestion below:", followed by a marked-off section with **Related page** (title and URL) and **File** (`relative_path`). This matches the caostest `page-issue.md` template.
- The same title and URL, URL-encoded, become the `context` parameter on the "Send a suggestion" contact-page link.

This block sits in the same footer column as the existing Accessibility Statement / Contact / Donate list, placed as its own labeled group rather than appended to that list — see "Placement — amended" above.

**Known GitHub platform limitation:** `title=`/`body=` query params reliably pre-fill classic single-textarea Markdown issue templates. If a target repo uses the newer YAML-based issue *forms* (multiple structured fields), GitHub does not support pre-filling those fields via `title=`/`body=` — the visitor lands on the chooser/form with our context only in the (still pre-filled) title, not the body. This is a constraint of GitHub's own URL API, not something our Liquid can work around. Checked: both caostest and governance use classic Markdown templates, so pre-filling works.

On `contact.md`, a new hidden field:
```html
<input type="hidden" id="contact-page-context" name="page_context" data-fs-field>
```
and page-load script:
```js
var params = new URLSearchParams(window.location.search);
var context = params.get('context');
if (context) {
  document.getElementById('contact-page-context').value = context;
  document.getElementById('contact-topic').value = 'website-feedback';
}
```
and a new topic option in `<select id="contact-topic">`:
```html
<option value="website-feedback">Website feedback or issue report</option>
```

### Issue templates are each repo's own concern

Because the link no longer names a specific template file, no template filename needs to be created, registered, or kept in sync anywhere in this repo's config. Each target repo (`center4aos/caostest`, `center4aos/governance`, and any future one) is free to add, rename, or drop its own `.github/ISSUE_TEMPLATE/` files independently — the footer link keeps working either way. Both repos now have templates: caostest has `page-issue.md`, and governance has `policy-issue.md` and `general.md`. **Amended:** an optional `template=` parameter was added after all. caostest sets `issue_template: page-issue.md` in `_config.yml`, so its "Suggest a change" links open that template directly. Any registry entry can set `issue_template` too, but governance leaves it unset, so GitHub shows its own chooser.

**Verified:** `page.path` for `_posts`/`_events` collection items does resolve to a meaningful relative source path in Liquid — confirmed by loading this repo's actual site with `bundle exec ruby` (Jekyll 3.9.3, the version pinned via the `github-pages` gem) and inspecting what `to_liquid` exposes as `"path"`: `_posts/2026-06-25-new-caos-web-site.md` for the existing blog post, `_events/2026-07-15-board-meeting.md` for the existing board-meeting event. (Note: the raw Ruby `Document#path` accessor returns an *absolute* filesystem path — a red herring if you check it that way — but `{{ page.path }}` as seen by Liquid templates is always the relative path, matching what ordinary pages like `governance.md` already show.) No changes needed to reach this conclusion; the `remove_first: mount_path` stripping described above is a no-op for posts/events, which is correct since they have no `external_repo`/mount prefix to strip in the first place.

No open questions remain for this section — placement, both link labels, and the two-path structure are all decided above.

---

## Leadership Bios & Photos

### Purpose

Officers and board members (About CAOS page) each need a headshot, an image description (alt text), and a short bio, submitted individually and updated independently of one another without disturbing anyone else's entry. Ordering must be controllable (not just alphabetical-by-name), and a mismatched or incomplete submission should fail loudly with a specific, interpretable error rather than silently rendering wrong or incomplete.

### Decisions

- **One folder, matched by filename stem:** `_leadership/<stem>.md` (data: `name:`, `role:`, `bio:`, `desc:` frontmatter fields, no body content used) and `_leadership/<stem>.jpg` (headshot) live together, co-located as originally wanted for editing convenience. **Verified working** ([caostest#20](https://github.com/center4aos/caostest/issues/20)): a real `bundle exec jekyll build` test confirmed Jekyll's collection mechanism copies a co-located non-frontmatter `.jpg` through as an ordinary static file — output path mirrors the collection directory with the leading underscore stripped, e.g. `_leadership/miele.jpg` → `/leadership/miele.jpg` — while the paired `.md` correctly produces zero output of its own (pure collection document, suppressed by `output: false`). An earlier draft of this design split data and photos into two separate folders as a hedge against this being unverified; that hedge is no longer needed.
- **`_leadership` is a Jekyll collection with `output: false`,** not a generated `_data/*.yml` file. Every field here is already explicit frontmatter with no nesting or H1-extraction needed (unlike the governance tree-walk), so Jekyll's own frontmatter parser exposes `site.leadership` directly — no custom generation script required for this part.
- **Ordering:** by `path` (source file path), which reflects filename order directly. Default alphanumeric sort by stem; when a specific non-alphabetical order is wanted (e.g., President listed first regardless of surname), prepend a number to the stem (e.g. `01-miele.md` / `01-miele.jpg`, `02-summers.md` / `02-summers.jpg`). The numeric prefix is never displayed — `name:` is always the display source — it only affects sort order and file pairing (both halves of a pair must share the exact same stem, prefix included).
- **Fallback image for entries with no photo yet:** `_leadership/fallback.jpg` is a reserved file, not a person — if a `.md` has no matching `.jpg`, the card renders `fallback.jpg` instead, and the alt text is forced to the literal string "CAOS generic image" regardless of whatever that entry's own `desc:` field says (a missing photo is expected to happen before someone's real headshot is ready, and the fallback's alt text should describe the fallback image, not whatever placeholder text happened to be in `desc:`). Currently a plain solid-gray 400×400 JPEG (generated via .NET's `System.Drawing`, since no image-editing tool was available in this environment) — a real permanent fallback graphic is expected to replace it later, same filename, no code changes needed. Detected at render time via `site.collections | where: "label", "leadership" | first`, then that collection's `.files | where: "basename", stem | first` — **verified empirically** that `site.static_files` does *not* include collection-directory static files (they're tracked separately, on the collection object itself), so the lookup has to go through `site.collections`, not the more obvious `site.static_files`.
- **Validation is a separate, focused script**, not folded into data generation: `script/validate-leadership.rb` walks `_leadership/`. Every `.md` needs `name`, `role`, `bio`, and `desc` all present and non-blank — a missing `.jpg` is *not* an error (that's the expected, supported "no photo yet" case, handled by the fallback above), but an orphaned `.jpg` with no matching `.md` still is, since that's more likely a leftover/mistake than something intentional. `fallback.jpg` itself is a reserved stem: required to exist, and rejected if a `.md` ever tries to use that same stem (it can't also be a person entry). Exits non-zero with a specific message naming the exact file and the exact problem on any failure; exits 0 with a count of real person entries (excluding the fallback file itself) on success. Deliberately only `.jpg` is recognized as the image extension — a stray `.jpeg`/`.png`/`.webp` file won't be matched to its `.md` counterpart and will correctly surface as an orphaned-image error, catching format mistakes for free without extra special-casing.
- **Run manually for now, not wired into CI.** This is a slowly-changing list; automating validation into a GitHub Action (mirroring issue #10's pattern) is easy to add later if it ever becomes worth it, but isn't justified yet.
- **Scope: Officers and Board Members only.** The Advisory Board section has no confirmed members yet ("Advisory board members to be announced" — see About CAOS page structure); this mechanism can extend to cover it once members exist, rather than speccing for placeholder content.
- **Markdown links are supported in `bio` and `name`, not `role` or `desc`:**
  - `bio` is rendered via `{{ bio | markdownify }}`. Kramdown wraps this in a `<p>...</p>`, which is fine since the bio sits next to the photo as ordinary flowing text, not inside a heading.
  - `name` is rendered via `{{ name | markdownify | remove_first: "<p>" | remove_first: "</p>" | strip }}` — the same markdownify call, but with the block-level `<p>` wrapper stripped (via two `remove_first` calls, pure Liquid, no plugin needed) since `name` is embedded inside a heading (`#### {{ name_html }}, {{ role }}`) where a nested block element would be invalid and would break the layout. The trailing `strip` removes the newline `markdownify` leaves behind once the `<p>`/`</p>` tags are gone -- caught by testing, since without it a stray space appears before the following comma (e.g. "Name , Role"). This preserves any inline formatting/links inside, just not the outer paragraph wrapper.
  - `role` stays plain text (job titles don't need links).
  - `desc` is never markdownified — it's used as a raw `alt` attribute value, which can't contain markup at all.
- **External links need no special handling.** All links contributors add in `bio`/`name` will be external (personal sites, professional profiles, etc.). The site-wide script in `_layouts/default.html` already finds every `<a href="http...">` whose hostname differs from the current page and adds `target="_blank"`, `rel="noopener noreferrer"`, and an auto `aria-label` — this runs after Jekyll's build-time HTML is already in the DOM, so it applies uniformly to these links with zero extra code.

### Rendering

`_includes/leadership-card.html`, called once per entry from `about.md`:

```liquid
{% assign leaders = site.leadership | sort: "path" %}
{% for leader in leaders %}
  {% include leadership-card.html leader=leader %}
{% endfor %}
```

```liquid
<!-- _includes/leadership-card.html -->
{% assign name_html = include.leader.name | markdownify | remove_first: "<p>" | remove_first: "</p>" | strip %}
{% assign stem = include.leader.path | remove_first: "_leadership/" | remove_first: ".md" %}
{% assign leadership_collection = site.collections | where: "label", "leadership" | first %}
{% assign photo_file = leadership_collection.files | where: "basename", stem | first %}
{% if photo_file %}
  {% assign photo_stem = stem %}
  {% assign photo_alt = include.leader.desc %}
{% else %}
  {% assign photo_stem = "fallback" %}
  {% assign photo_alt = "CAOS generic image" %}
{% endif %}
<div class="leadership-card">
  <h4>{{ name_html }}, {{ include.leader.role }}</h4>
  <div class="leadership-card-body">
    <img src="{{ "/leadership/" | append: photo_stem | append: ".jpg" | relative_url }}"
         alt="{{ photo_alt }}" class="leadership-photo">
    {{ include.leader.bio | markdownify }}
  </div>
</div>
```

Image path mirrors the collection directory with its leading underscore stripped (`_leadership/miele.jpg` → `/leadership/miele.jpg`), per the verified behavior above — not `/assets/images/leadership/`, since the photo lives in `_leadership/` itself now.

### CSS

Added to `accessibility.scss` (nothing image-related existed there before): `.leadership-card-body` as a flex row (image + bio side by side, wrapping to stacked on narrow viewports); `.leadership-photo` at a fixed 120×120px with `object-fit: cover`, so source photos don't need pixel-perfect pre-cropping to render consistently.

No open questions remain for this section — the one-folder design is verified working end to end (see [caostest#20](https://github.com/center4aos/caostest/issues/20)).

---

## Resources Used in Implementation

- Blog setup reference: https://trstringer.com/blog-hosting-details/
- Accessible Jekyll reference: https://jekyllhub.com/tutorial/2026/05/29/jekyll-accessibility-wcag/

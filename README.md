# CAOS Website

Jekyll site for the **Center for Accessibility and Open Source** (CAOS).

**Live site:** https://caos.org/caostest/  
**Organization:** https://github.com/center4aos

---

## About CAOS

CAOS is a California 501(c)(3) nonprofit — the first organization to make the accessibility of open-source resources its primary mission. We support disability inclusion in open-source communities and the development of open-source assistive technologies worldwide.

---

## Site Structure

| Path | Purpose |
|---|---|
| `_posts/` | Blog posts |
| `_events/` | Calendar events |
| `_leadership/` | Officer and board bios and headshots, one `.md` + `.jpg` pair per person (see below) |
| `_layouts/default.html` | Base page template (skip link, nav, breadcrumbs, footer, focus management, external link handling; supplies titles for Policy Library documents) |
| `_layouts/page.html` | Minimal layout used by Policy Library documents |
| `_layouts/event.html` | Event detail page template |
| `_includes/header.html` | Skip-to-main link, site title, nav with aria-current |
| `_includes/footer.html` | Accessibility statement, contact, donate, "Suggest a change" / "Send a suggestion" feedback links, license |
| `_includes/breadcrumbs.html` | Breadcrumbs built from the `ancestors` list (set per page, or by `_config.yml` defaults) |
| `_includes/leadership-card.html` | One leadership bio card on the About page |
| `_includes/external-repo-listing.html` | Recursive listing used by the Policy Library page |
| `governance/filings/` | Financial filings such as the Form 990-N, each as an accessible Markdown page plus the original PDF |
| `governance/library/` | Git submodule of [center4aos/governance](https://github.com/center4aos/governance), served as the Policy Library |
| `_data/external_repos.yml` | Registry of mounted repos (currently just governance) |
| `_data/external_repo_pages/` | Generated page titles and tree for each mounted repo, written by `script/sync-external-repo-pages.rb` |
| `_external_repo_pages/governance.md` | The Policy Library listing page at `/governance/library/` |
| `script/` | `sync-external-repo-pages.rb` (regenerates the data above) and `validate-leadership.rb` |
| `.github/workflows/update-submodule.yml` | Updates the governance submodule automatically when the governance repo changes |
| `.github/ISSUE_TEMPLATE/page-issue.md` | Template for "Suggest a change" issues filed against this repo |
| `assets/css/accessibility.scss` | Accessibility-focused style overrides |
| `index.md` | Home page: mission, blog preview, upcoming events, newsletter signup |
| `about.md` | About CAOS, mission, leadership bios, advisory board |
| `projects.md` | Active partnerships (CREATE, Teach Access, NV Access) |
| `resources.md` | Curated accessibility resources (content placeholder) |
| `governance.md` | 501(c)(3) and transparency statements, Policy Library link, Form 990, board meetings |
| `support.md` | Donation information (PayPal, Venmo, Benevity, planned giving, check) |
| `contact.md` | Contact form (Formspree) and newsletter signup (Buttondown) |
| `subscribe-confirm.md`, `subscribe-thanks.md`, `contact-thanks.md` | Confirmation pages for the newsletter and contact form |
| `calendar.md` | Upcoming events |
| `blog.md` | Blog index |
| `accessibility-statement.md` | Public accessibility commitment |

---

## Adding a Blog Post

Create a file in `_posts/` named `YYYY-MM-DD-your-title.md`:

```yaml
---
layout: post
title: "Your Post Title"
date: 2026-06-20
author: Your Name
---

Post content in Markdown.
```

## Adding a Calendar Event

Create a file in `_events/` named `YYYY-MM-DD-event-title.md`:

```yaml
---
layout: event
title: "Event Title"
date: 2026-07-15
time: "7:00 PM Pacific Time"
location: "Zoom (optional)"
---

Event description in Markdown.
```

Events are sorted chronologically. Past events are not displayed on the calendar page. The calendar rebuilds on every push, so past events drop off automatically. The "Calendar" breadcrumb is added automatically; events don't need to set it.

## Adding or Updating a Leadership Bio

Each person gets a pair of files in `_leadership/` with the same name: `<stem>.md` and `<stem>.jpg`. The `.md` holds front matter only:

```yaml
---
name: Jane Doe
role: Board Member
bio: >-
  One short paragraph. Markdown links are allowed.
desc: Image description (alt text) for the headshot.
---
```

- Cards are listed in filename order. Put a number at the start of the stem (e.g. `0Miele`) to control the order; it is never displayed.
- If there's no `.jpg` yet, the card shows `fallback.jpg` with the alt text "CAOS generic image".
- Run `ruby script/validate-leadership.rb` to check for missing fields or photos with no matching `.md`.

---

## Internal Links

All internal links must use Jekyll's `relative_url` filter so they remain correct when the site moves from `/caostest/` to the domain root:

```liquid
[Link text]({{ "/path/to/page/" | relative_url }})
```

Never hardcode `/caostest/...` paths or bare root-relative `/path/` links — both break on migration.

---

## Technical Notes

- **Theme:** `minima` (native gem) with `skin: auto` for system light/dark preference
- **Breadcrumbs:** Built from an `ancestors` list of `{title, url}` pairs. Blog posts and events get theirs from `_config.yml` defaults, Policy Library documents from `_data/external_repos.yml`, and other child pages set it in their own front matter
- **Screen reader focus:** On page load, focus moves programmatically to the first H2 in `.post-content`, falling back to `#main-content`, for consistent NVDA/JAWS experience
- **External links:** Automatically open in a new tab with a screen-reader-friendly `aria-label`
- **Forms:** Contact form posts to Formspree; newsletter signup (home and contact pages) posts to Buttondown
- **Policy Library:** When the governance repo's `main` branch changes, its `notify-site.yml` workflow triggers this repo's `update-submodule.yml`, which updates the submodule, regenerates `_data/external_repo_pages/governance.yml`, and pushes. No manual step needed. Governance documents need an empty front matter block (`---` / `---`) at the top to render as pages
- **GitHub Pages:** Legacy build from root of `main` branch; `baseurl: /caostest`. Moving to the domain root is a one-time checklist in `CAOS Site Requirements.md`

---

## Outstanding TODOs

Open work is tracked in [issues](https://github.com/center4aos/caostest/issues). Site content still to be written:

- About page: advisory board member list
- Governance page: board meeting schedule
- Move the site from `/caostest/` to the domain root (see the migration checklist in `CAOS Site Requirements.md`)

---

## License

This repository is dual-licensed:

- **Code** — Jekyll templates/layouts/includes, stylesheets, scripts, and CI/CD configuration — is licensed under the [MIT License](LICENSE).
- **Content** — written site content, blog posts, and images — is licensed under [CC BY 4.0](LICENSE-CONTENT), unless otherwise noted.

Content mounted from other repositories (e.g. the governance repo's Policy Library) is licensed separately by that repository.

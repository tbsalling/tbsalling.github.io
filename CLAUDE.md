# tbsalling.dk — Project Instructions

This is the personal website of Thomas Borg Salling, Senior Freelance Java Architect & Systems Integrator. It is a fully static site hosted on GitHub Pages.

## Stack

- Pure static HTML/CSS/JS — no build tools, no npm, no frameworks
- Fonts: Space Grotesk + JetBrains Mono (Google Fonts CDN)
- Icons: Font Awesome 6 (CDN, contact section only)
- Favicon: `favicon.svg` (<T/> mark, cyan on dark slate)

## File structure

```
index.html      # Home page
clients.html    # Portfolio — all client engagements with domain filter
favicon.svg     # SVG favicon
CNAME           # GitHub Pages custom domain (tbsalling.dk)
images/         # Logo and photo assets (copy from live site)
```

## Design system

### Colors (CSS custom properties on :root)
| Token | Value | Usage |
|---|---|---|
| `--bg` | `oklch(9% 0.018 255)` | Page background |
| `--bg-2` | `oklch(13% 0.022 255)` | Card / section background |
| `--bg-3` | `oklch(17% 0.022 255)` | Panel titlebar |
| `--border` | `oklch(22% 0.020 255)` | Default border |
| `--border-hi` | `oklch(30% 0.022 255)` | Hover border |
| `--fg` | `oklch(92% 0.008 255)` | Primary text |
| `--fg-muted` | `oklch(58% 0.010 255)` | Body text |
| `--fg-dim` | `oklch(36% 0.010 255)` | Timestamps, labels |
| `--accent` | `oklch(72% 0.20 195)` | Cyan accent (default) |
| `--accent-dim` | accent at 12% opacity | Tag backgrounds |
| `--accent-glow` | accent at 30% opacity | Tag borders, glows |

The Tweaks panel allows switching accent to amber (`oklch(74% 0.18 45)`) or emerald (`oklch(70% 0.18 155)`).

### Typography
- Headings: `Space Grotesk`, weight 700, `letter-spacing: -0.03em` to `-0.04em`
- Body: `Space Grotesk`, weight 400, `line-height: 1.65–1.78`
- Code / labels / nav: `JetBrains Mono`
- Section labels: `font-size: 0.68rem`, uppercase, `letter-spacing: 0.14em`, accent color, prefixed with `// NN ·`

### Layout
- Home page uses `scroll-snap-type: y mandatory` — each `<section>` is `min-height: 100vh` and `scroll-snap-align: start`
- Max content width: 1100px (sections)
- Spacing: `clamp()` throughout for responsive padding

### Home page section order
1. **Hero** — identity, portrait, stats
2. **About** `// 01` — background, approach, domains
3. **How I work** `// 02` — engagement model (scoped, time-limited, outcome-focused)
4. **Selected Clients** `// 03` — logo grid, link to portfolio
5. **Testimonials** `// 04` — 6 anonymised LinkedIn recommendations
6. **Skills** `// 05` — 8 terminal-style panels (Java, APIs, Cloud, Data, AI, Leadership, Build)
7. **Contact** `// 06` — contact links with Font Awesome icons

### Components
- **Skill panels**: dark card with macOS-style traffic light dots in titlebar, uniform grey tag grid inside (no highlighted tags)
- **Project cards** (`clients.html`): year + logo (white pill background) left column, details right column
- **Testimonial cards**: italic quote with left accent border, role + year footer
- **Contact links**: full-width row with Font Awesome icon, label, value
- **How I work cards**: numbered 01–04, dark bg, title + description

## Adding a new client engagement

Add a new `.project-card` to `clients.html` following this pattern:

```html
<div class="project-card" data-domains="finance">
  <div class="project-year">
    2026
    <div class="project-logo-wrap"><img src="images/logo_client.png" class="project-logo" alt="Client Name"></div>
  </div>
  <div class="project-body">
    <div class="project-top">
      <div class="project-title">Project title — Client Name</div>
      <div class="project-domains"><span class="domain-tag d-finance">Finance</span></div>
    </div>
    <div class="project-role">Role (Freelance)</div>
    <div class="project-desc">Description of the engagement.</div>
    <div class="tech-tags">
      <span class="tech-tag">Java</span>
    </div>
  </div>
</div>
```

Available domain classes: `d-finance`, `d-maritime`, `d-health`, `d-defence`, `d-telecom`, `d-ecommerce`

## Contact information

- Phone: +45 4063 2353
- Email: tbsalling@tbsalling.dk
- LinkedIn: https://www.linkedin.com/in/tbsalling
- GitHub: https://github.com/tbsalling
- Stack Overflow: https://stackoverflow.com/users/279511/tbsalling

## Deploying to GitHub Pages

```bash
git add .
git commit -m "Update site"
git push origin master
```

The `CNAME` file is already configured for `tbsalling.dk`. GitHub Pages serves the site automatically from the `master` branch root.

## Image assets

All images are present in `images/`:

```
images/tbs.webp            # Portrait (used in hero + about)
images/tbs.jpg             # Portrait (original)
images/logo_norlys.svg
images/logo_jb.jpg
images/logo_systematic.jpg
images/logo_bankdata.jpg
images/logo_frequentis.jpg
images/logo_dma.jpg
images/logo_nets.jpg
images/logo_dalo.jpg
images/logo_dtgroup.jpg
images/logo_gh.jpg
images/logo_osi.jpg
```

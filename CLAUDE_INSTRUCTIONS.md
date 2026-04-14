# Ferien Analyse — Claude Project Instructions

## What This Project Is

A holiday destination analysis site for 3 families (6 adults + 6 children, ages 3–11) travelling from Zürich.
Each holiday gets its own research-backed comparison page. All pages are published via Netlify at a permanent URL.

**Live URL:** https://ferien-vergleich.netlify.app

---

## Repository & Infrastructure

| Item | Value |
|---|---|
| GitHub repo | https://github.com/baede85-claude/Ferien_analysis.git |
| GitHub user | baede85-claude |
| Netlify site name | ferien-vergleich |
| Netlify site ID | 21ba24d8-d846-4a42-8d75-7c265f1c48f5 |
| Netlify team slug | baede85 |
| Live URL | https://ferien-vergleich.netlify.app |

**Deployment pipeline:** GitHub push → Netlify auto-deploys within ~30 seconds. No manual deploy step needed.

---

## GitHub Push Workflow

Every update must be pushed to GitHub. Always use this exact sequence:

```bash
cd /home/claude/Ferien_analysis

# Set authenticated remote (token must be current)
git remote set-url origin https://baede85-claude:GITHUB_TOKEN@github.com/baede85-claude/Ferien_analysis.git

# Stage, commit, push
git add -A
git commit -m "Descriptive commit message"
git push origin main
```

**GitHub Personal Access Token:** `YOUR_GITHUB_PAT_HERE`
> ⚠️ If this token has expired, ask the user to generate a new one at github.com/settings/tokens/new (scope: `repo`).

**Note:** `api.netlify.com` is blocked by the container network allowlist — direct Netlify API deploys will fail. GitHub push is the only working deploy path.

---

## File Structure

```
Ferien_analysis/
├── index.html                    ← Landing page (always at root)
├── herbstferien-2026/
│   └── index.html                ← Subpage: Herbstferien 2026 comparison
├── [next-holiday-slug]/
│   └── index.html                ← Each new holiday gets its own folder
└── CLAUDE_INSTRUCTIONS.md        ← This file
```

**URL pattern:** `ferien-vergleich.netlify.app/[holiday-slug]/`

**Naming convention for slugs:** `[type]-[year]` — e.g. `herbstferien-2026`, `skiferien-2026`, `sommerferien-2026`

---

## Design System

Both pages share the same dark luxury aesthetic. Never break this.

### Colours (CSS variables)
```css
--night: #0d0f14;       /* page background */
--deep:  #13161e;       /* section backgrounds */
--card:  #181c27;       /* card backgrounds */
--border: rgba(255,255,255,0.07);
--gold:  #c8a96e;       /* primary accent */
--gold-dim: rgba(200,169,110,0.15);
--white: #f0ece4;       /* body text */
--muted: rgba(240,236,228,0.45); /* secondary text */
--accent: #7eb8c9;      /* blue accent */
```

### Typography
- Display / headings: `Cormorant Garamond` (Google Fonts) — weight 300, italic for emphasis
- Body / UI: `DM Sans` (Google Fonts) — weight 300/400/500
- Never use: Inter, Roboto, Arial, system-ui

### Shared elements every page must have
1. Ambient glow blobs (`.ambient-1`, `.ambient-2`) — CSS animated, fixed position
2. Noise texture overlay on `body::before`
3. Google Fonts import at top of `<style>`

---

## Landing Page (`index.html`)

### Layout
- **Sticky nav** with brand name `Ferien Analyse` (gold, Cormorant) + nav links
- **Two-column hero:**
  - Left: eyebrow text, large title, italic subtitle, description, CTA button
  - Right: stacked trip cards — one per holiday analysis
- **Stats strip** below the hero fold (number of destinations, families, criteria, departure city)
- **"Die Methode"** section at the bottom — 3-step grid
- **Footer**

### Trip card format (right column)
Each holiday = one card in `.trips-stack`. Format:

```html
<a href="[slug]/" class="trip-card">
  <div class="card-num">01</div>
  <div class="card-body">
    <div class="card-title">[Holiday Name] [Year]</div>
    <div class="card-meta">
      <span class="card-tag tag-active">✦ Bereit</span>
      <span>[Dates] · [Nights] Nächte · CHF [Budget]</span>
    </div>
    <div class="card-chips">
      <!-- top 5 destinations as chips, then "+X weitere" -->
    </div>
  </div>
  <div class="card-arrow">→</div>
</a>
```

Use `.tag-active` for published analyses, `.tag-soon` for planned ones (greyed out with `.coming-soon` class on the card).

### When adding a new holiday
1. Add a new `.trip-card` entry in the right column of `index.html`
2. Update the stats strip numbers if needed (destination count, etc.)
3. Create the subpage (see below)

---

## Subpage Template (`[slug]/index.html`)

### Required sections in order
1. **Sticky breadcrumb nav** — `← Ferien Analyse / [Holiday Name Year]`
2. **Page header** — eyebrow, large title with italic year, meta info row, top-score callout (top-right)
3. **Flight note bar** (only if any destination requires a flight)
4. **Comparison table** — wrapped in `.table-section` with legend
5. **Summary / Fazit** — question block + choice cards grid
6. **Footer**

### Breadcrumb nav
```html
<nav>
  <a href="/" class="nav-back">Ferien Analyse</a>
  <span class="nav-sep">/</span>
  <span class="nav-current">[Holiday Name Year]</span>
</nav>
```

### Table design rules
- Dark background (`var(--card)`)
- Group header row: `background: #0a0c10`, gold text
- Destination header row: `background: #10131a`
- Highlighted columns (top picks): class `hl` on `td` and `th`
- Score row: `background: #0a0c10`, Cormorant font, colour-coded scores:
  - 9.0+: `score-10` (#7aefaa green)
  - 7.5–8.9: `score-high` (gold)
  - 6.0–7.4: `score-mid` (blue)
  - <6.0: `score-low` (red)

### Badge classes for table cells
```
.cb-green  → positive (✓ / ✓✓)
.cb-yellow → caution (⚠)
.cb-red    → risk (✗)
.cb-blue   → neutral info (i)
.cb-gray   → not applicable
```

### Standard table rows (use these every time)
1. Budget-Eignung
2. Geschätzter Preis (total for group)
3. Unter einem Dach
4. Wandern
5. Velofahren / MTB
6. Outdoor-Vielfalt
7. Kinder (ages of current group)
8. Oktober-Wetter (or relevant month)
9. Anreise ab ZRH
10. Kultur & Flair
11. Pool / Strand [Month]
12. Buchungsrisiko
13. Haupt-Risiko (free text, risk cell)
14. **Gesamtwertung** (score row)

---

## Group Profile (always use these values)

| Field | Value |
|---|---|
| Families | 3 |
| Total people | 12 (6 adults + 6 children) |
| Children ages | W11, M9, W8, W6, M6, W3 |
| Departure | Zürich (ZRH) |
| Budget | CHF 5.000–8.000 total for group |
| Accommodation | Must be "unter einem Dach" (shared property) |
| Travel style | Family-first, outdoor activities (hiking, biking) for adults |

---

## Adding a New Holiday — Full Checklist

### 1. Research phase (in Claude chat)
- Run Phase 1 intake interview if dates/budget differ
- Research top 6–10 destinations, score each across 12–14 criteria
- Confirm activities are open/available for the specific travel dates

### 2. Create subpage
```bash
mkdir -p /home/claude/Ferien_analysis/[slug]
# Write /home/claude/Ferien_analysis/[slug]/index.html
# Use the subpage template and design system above
```

### 3. Update landing page
- Add trip card to the right column
- Change tag from `tag-soon` / `.coming-soon` to `tag-active` when ready
- Update stats if destination count changes

### 4. Push to GitHub
```bash
cd /home/claude/Ferien_analysis
git remote set-url origin https://baede85-claude:YOUR_GITHUB_PAT_HERE@github.com/baede85-claude/Ferien_analysis.git
git add -A
git commit -m "Add [Holiday Name Year] subpage + update landing page"
git push origin main
```

Netlify deploys automatically. Live in ~30 seconds at `ferien-vergleich.netlify.app`.

---

## Existing Holidays

| # | Slug | Status | Dates | Destinations |
|---|---|---|---|---|
| 01 | `herbstferien-2026` | ✅ Published | 3–10 Oct 2026 | Verzasca, Saas-Fee, Interlaken, Zermatt, Finale Ligure, Lago Maggiore, Piemont, Sardinien, Toskana |
| 02 | `skiferien-2026` | 🔜 Planned | TBD | TBD |
| 03 | `sommerferien-2026` | 🔜 Planned | Jul/Aug 2026 | TBD |

---

## Common Mistakes to Avoid

- **Never say "15 Personen"** — the group is **12 Personen** (6 adults + 6 kids)
- **Never say "2025"** for current holidays — all active analyses are **2026**
- **Never push without setting the authenticated remote URL** — the push will fail silently
- **Never try to deploy via Netlify CLI or API directly** — the container blocks `api.netlify.com`
- **Never break the design system** — both pages must share the same dark aesthetic, fonts and colour variables
- **Always use `index.html` as the filename** inside each subfolder — not the holiday name

---

## Quick Reference: Key URLs

| Resource | URL |
|---|---|
| Live site | https://ferien-vergleich.netlify.app |
| Herbstferien 2026 | https://ferien-vergleich.netlify.app/herbstferien-2026/ |
| GitHub repo | https://github.com/baede85-claude/Ferien_analysis |
| Netlify dashboard | https://app.netlify.com/projects/ferien-vergleich |
| New GitHub token | https://github.com/settings/tokens/new (scope: repo) |

# Haystack Health CRM — Project Notes

This file captures what I (Claude) have learned about this project so future sessions can pick up the context quickly.

---

## What this project is

A web-based CRM (customer relationship management) tool built for **Haystack Health**, a healthcare startup. The CRM tracks two distinct customer segments with different revenue models:

1. **Payer / Provider Pipeline** — Health Plans, ACOs, and Health Systems. Revenue is PMPM (per-member-per-month).
2. **Sponsor Pipeline** — Pharma, Biotech, and CROs. Revenue is a per-enrollment fee.

It's meant to feel like a real internal business tool, not a toy demo.

---

## Folder structure

```
Haystack Health/
├── index.html      ← The entire app (HTML + CSS + JS in one file)
├── CLAUDE.md       ← This file
└── README.md       ← Public-facing project README
```

Single-file architecture: no build step, no server, no dependencies. Open `index.html` directly in a browser and it just works.

---

## What each file does

### `index.html`
The complete application. Organized internally as:
- `<style>` block — full design system (CSS variables, components, responsive rules)
- `<body>` markup — three views (Dashboard, Payer/Provider, Sponsor) + a shared modal + a confirm dialog
- `<script>` block — state, persistence, rendering, event wiring

Key JS sections (in order):
1. **Constants** — `STAGES`, `STAGE_PROBABILITY`, `STAGE_CLASS`, `PAYER_TYPES`, `SPONSOR_TYPES`
2. **State** — single `state` object holding `payer[]`, `sponsor[]`, `sort`, `filters`
3. **Persistence** — `loadState()` / `saveState()` using `localStorage` key `haystack_crm_v1`
4. **Seed data** — 5 sample contacts per segment on first load
5. **Calculations** — `calcPayer()`, `calcSponsor()` derive ACV and weighted value
6. **View switching** — tab-based, only one view visible at a time
7. **Dashboard rendering** — KPI cards + two SVG-free funnel charts
8. **Table rendering** — separate functions for payer and sponsor with shared filter/sort
9. **Modal / Form** — dynamically generated form fields based on which segment
10. **CSV export** — per segment or both
11. **Events** — `wireEvents()` ties everything together

### `CLAUDE.md`
Living context for Claude. Update it as the project evolves.

### `README.md`
Public-facing readme for the GitHub repo.

---

## Data model

### Payer/Provider contact
```
id, organizationName, contactName, contactEmail
customerType: "Health Plan" | "ACO" | "Health System"
coveredMembers (int)
estimatedPMPM (decimal, dollars)
stage, closeProbability
notes (optional)

Derived:
  acv      = coveredMembers × estimatedPMPM × 12
  weighted = acv × (closeProbability / 100)
```

### Sponsor contact
```
id, organizationName, contactName, contactEmail
customerType: "Pharma" | "Biotech" | "CRO"
numberOfTrials (int)
expectedEnrollmentsPerTrial (int)
feePerEnrollment (decimal, dollars)
stage, closeProbability
notes (optional)

Derived:
  acv      = numberOfTrials × expectedEnrollmentsPerTrial × feePerEnrollment
  weighted = acv × (closeProbability / 100)
```

### Pipeline stages & default probabilities
| Stage | Probability |
|---|---|
| Prospecting | 10% |
| In Conversation | 30% |
| Proposal Sent | 60% |
| Closed Won | 100% |

Probability auto-fills from stage when the user changes the stage dropdown, but the user can override it.

---

## Design choices

### Color palette (navy + white, professional)
- `--navy-900: #0A2342` — primary brand color, top nav, headings, primary buttons
- `--navy-700: #1B4F8A` — hover states, secondary navy
- `--navy-500: #2E86C1` — accent (links, focus rings, lighter chips)
- `--navy-100: #D6E4F0` / `--navy-50: #EEF3F9` — soft tinted backgrounds
- `--green-600: #1A6E3C` — Closed Won stage, success
- `--surface: #F4F7FB` — page background
- `--white: #FFFFFF` — cards, inputs
- Stage funnel uses a 4-step ramp from light blue → deep navy, with green for Closed Won
- `--danger: #B83B3B` for delete actions

### Typography
- System font stack: `-apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, "Helvetica Neue", Arial, sans-serif`
- Base size 14px, tabular numerals for all money/count columns
- Uppercase 11–12px labels for KPIs and form labels (0.5–0.6px letter-spacing)
- Page titles 24px / 700 weight, panel titles 15px / 700

### Layout
- Sticky top nav (64px tall), navy background
- Centered content max-width 1400px, 32px page padding
- Dashboard: 4-column KPI grid + 2-column funnel panels
- Tables: full-bleed inside white panels, with a toolbar (search + filters) on a soft navy tinted background

### Component patterns
- Rounded corners: 6px (small), 10px (cards), 14px (modals)
- Subtle shadows; no heavy borders
- Stage pills are color-coded; type pills are light navy
- Modal slides in with a small fade+translate animation
- Toast notification bottom-right for actions (add/update/delete/export)

### Features included
- Full CRUD per segment
- Search by org / contact / email
- Filter by stage and customer type
- Click any column header to sort (asc/desc toggle)
- CSV export per segment + "Export All" on the dashboard
- **CSV import** (Dashboard → "Import CSV") with segment selector, template download, preview, and duplicate-handling options (add / update / skip)
- Live ACV + weighted value preview inside the form as the user types
- Confirm dialog before delete
- Empty states with helpful copy
- localStorage persistence (key: `haystack_crm_v1`)
- 5 seeded sample contacts per segment on first load
- Keyboard: `Esc` closes modals

---

## User preferences (Shane)

- **Does not code** and does not want to learn. Build everything end-to-end without making him work.
- **Minimize permission prompts.** Don't stop to ask unless absolutely required.
- Wants a single GitHub repo named **`Haystack_DEMO`** for this project.
- Approves polished, business-looking UI over flashy demos.
- Likes navy + white branding for Haystack Health.

---

## Pages / sections

1. **Dashboard** (`#view-dashboard`)
   - Top row of 4 KPI cards: Total Weighted Pipeline (featured navy gradient), Payer/Provider Weighted, Sponsor Weighted, Total ACV
   - Second row of 3 KPI cards — **Average Customer Value by end-market**: Provider (Health System), Payer (Health Plan), Pharma. Each shows average ACV across that customer type plus a count subtitle.
   - Two side-by-side pipeline funnels (one per segment), each with stage count and weighted value bars
   - Top-right actions: **Import CSV** and **Export All (CSV)**

2. **Payer / Provider Pipeline** (`#view-payer`)
   - Segment stats strip: deals, total ACV, weighted pipeline, total covered members
   - Filter toolbar + sortable table
   - Add/Edit modal with PMPM-specific fields

3. **Sponsor Pipeline** (`#view-sponsor`)
   - Segment stats strip: deals, total ACV, weighted pipeline, total expected enrollments
   - Filter toolbar + sortable table
   - Add/Edit modal with enrollment-fee-specific fields

---

## How to run

Just open `index.html` in any modern browser. No build, no install, no server. Data persists in the browser's localStorage.

To reset the demo data, open DevTools → Application → Local Storage → delete the `haystack_crm_v1` key, then reload.

---

## Possible future extensions (not yet built)

- Multi-user / cloud sync (would need a backend)
- CSV import
- Per-contact activity log / notes history
- Email integration
- Forecasting view (e.g., quarterly projection)
- Dark mode

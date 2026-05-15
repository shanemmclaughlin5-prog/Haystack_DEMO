# Haystack Health — CRM (Demo)

A clean, professional, zero-dependency CRM built for **Haystack Health**, a healthcare startup. Tracks two distinct customer segments side by side and visualizes the combined pipeline.

## Segments

### Payer / Provider Pipeline (PMPM revenue)
Tracks **Health Plans**, **ACOs**, and **Health Systems**. Each deal captures organization, contact, customer type, covered members, estimated PMPM, calculated annual contract value, pipeline stage, close probability, and weighted pipeline value.

### Sponsor Pipeline (per-enrollment fee)
Tracks **Pharma**, **Biotech**, and **CROs**. Each deal captures organization, contact, customer type, number of trials, expected enrollments per trial, fee per enrollment, calculated annual contract value, pipeline stage, close probability, and weighted pipeline value.

## Pipeline stages

| Stage | Default close probability |
|---|---|
| Prospecting | 10% |
| In Conversation | 30% |
| Proposal Sent | 60% |
| Closed Won | 100% |

## Dashboard

- Total weighted pipeline across both segments
- Separate Payer/Provider and Sponsor subtotals
- Per-stage deal counts and weighted-value bars for each segment
- Combined ACV across all deals

## Run it

Open `index.html` in any modern browser. No build step, no dependencies, no server. Data persists locally in your browser via `localStorage`.

To reset the demo data: clear the `haystack_crm_v1` key in your browser's localStorage and reload.

## Features

- Full CRUD for both segments
- Search, filter by stage and customer type, click-to-sort columns
- Live ACV + weighted value preview inside the add/edit form
- CSV export per segment (and "Export All" on the dashboard)
- Confirm-before-delete, keyboard shortcuts (`Esc` closes modals), toast notifications
- Empty states and validation on required fields
- Responsive layout

## Tech

Single `index.html` file. Vanilla JS, CSS custom properties, no frameworks, no build pipeline.

## Branding

Navy + white color scheme. System font stack for a professional, native feel.

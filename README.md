# ADFW Staffing — Hiring & Attendance

A lightweight, zero-cost system for recruiting and managing temporary event staff, built for **Abu Dhabi Finance Week 2026** (7–10 December, Al Maryah Island). It covers the full journey: a candidate scans a QR code to apply, is screened automatically, books an interview, receives an offer, and on event days checks in at their work area while managers watch a live dashboard.

Live site: **https://jamers188.github.io/checkin/**

---

## Features

### Hiring
- **Scan-to-apply** — a QR code / short link opens the application form.
- **Automatic screening** — every application is scored 0–100 (UAE residency, visa status, availability, experience, English level, languages) and sorted into *Fit for interview*, *Check manually* or *Not eligible*.
- **One-click workflow** — invite, mark interviewed, select, send offer or reject from a single dashboard; templated emails go out automatically and never twice.
- **Self-service interview booking** — candidates pick a 15-minute slot on a Google Calendar booking page; a Google Meet link is generated automatically.
- **Interview reminders** — automatic emails one day and one hour before each booked interview.
- **Offer acceptance** — candidates accept online; their status updates to *Hired* without manual work.

### Hiring dashboard (`hiring.html`)
- KPI tiles: applied, fit, to check, invited, interviews booked, offers, hired vs target.
- Charts: hiring pipeline, roles (hired vs applied), score spread, applicant coverage.
- Filter buttons by stage, role, experience, availability, language, nationality, score, interview and visa.
- Excel-style sortable table with per-row actions and a detail side panel for notes and role assignment.
- Interviews tab listing real calendar bookings matched to applicants.

### Attendance (event days)
- Each hired staff member receives a personal staff pass (ID + PIN).
- Staff scan a QR poster at their area (Registration, Main Hall, VIP Lounge, …) and tap **Check in**; moving areas or checking out is one tap.
- Managers see who is on site, late or not yet arrived, people per zone and a live activity feed, with manual override for staff without a phone.

---

## Pages in this repository

| File | Purpose |
|---|---|
| `index.html` | Staff check-in page (opened by the zone QR posters) |
| `hiring.html` | Hiring dashboard (protected by an admin key) |
| `apply/index.html` | Short link that redirects to the application form |
| `apply-poster.html` | Printable "We're hiring — scan to apply" poster |

---

## Architecture

```
 Candidate / Staff phone                Manager (laptop or phone)
          │                                        │
          ▼                                        ▼
  GitHub Pages (this repo)  ──── HTTPS / JSON ────►  Google Apps Script web app
  check-in · dashboard · apply                       (scoring, emails, check-ins, reminders)
                                                             │
                     ┌───────────────┬───────────────┬───────┴───────┬───────────────┐
                     ▼               ▼               ▼               ▼               ▼
               Google Sheets   Google Forms        Gmail      Google Calendar   Google Meet
               (data store)    (apply / offer)    (emails)    (interview slots)  (interviews)
```

- **Front end:** static HTML/CSS/JavaScript served by GitHub Pages — no frameworks, no build step.
- **Back end:** a Google Apps Script project bound to the hiring spreadsheet, deployed as a web app that exposes a small JSON API.
- **Why the pages live here:** hosting them outside Google avoids multi-account sign-in errors on phones and lets the browser request location permission normally.

## Tech stack

Google Sheets · Google Forms · Google Apps Script · Gmail · Google Calendar · Google Meet · GitHub Pages · QR codes

---

## How to run it

The system is already live — there is nothing to install or start. Day to day:

| Step | Who | What to do |
|---|---|---|
| 1. Collect applications | Candidates | Share the **apply QR code** or `https://jamers188.github.io/checkin/apply/`. Applications land in the Google Sheet and are scored automatically. |
| 2. Review | Hiring manager | Open `https://jamers188.github.io/checkin/hiring.html`, enter the admin key, filter by **Fit for interview**, then click **Invite** (or **Invite all**). |
| 3. Interviews | Candidates → manager | Candidates book a slot from the invite email; it appears in Google Calendar (with a Google Meet link) and in the dashboard's **Interviews** tab. Reminders go out automatically. |
| 4. Decide | Hiring manager | After the call: **Interviewed → Select** (choose a role) **→ Send offer**, or **Reject**. |
| 5. Hire | Candidates | They accept the offer online and show as **Hired** on the dashboard. |
| 6. Prepare staff | Hiring manager | In the Google Sheet: **Hiring → Sync hired staff**, then **Send staff passes**. Print the zone QR posters. |
| 7. Event days | Staff → managers | Staff scan the poster at their area and tap **Check in**; managers follow the live attendance dashboard. |

**Editing the pages:** change a file in this repository (GitHub → file → ✏️ Edit → Commit). GitHub Pages republishes within about a minute.

**Editing the back end:** change the Apps Script code, save, then *Deploy → Manage deployments → Edit → New version* so the live web app picks it up.

**Running the pages locally** (optional, for development): from this folder run `python3 -m http.server 8000` and open `http://localhost:8000/`. The pages still talk to the live Apps Script back end.

## Setup from scratch (summary)

1. Create a Google Sheet and attach the Apps Script code (`Code.gs`, `Attendance.gs` and the HTML files).
2. Edit the configuration blocks (event details, roles, pay, zones, admin key) and run **Set up** from the sheet's *Hiring* menu to create the forms and tabs.
3. Deploy the script as a web app (*Execute as: Me*, *Access: Anyone*) and set its URL in the pages in this repo (`API` constant).
4. Create a Google Calendar appointment schedule and set its link as the interview booking link; run `installReminderTrigger` once to enable reminders.
5. Enable GitHub Pages for this repository (branch `main`, root).

## Security & privacy

- Candidate and staff data stay in the owner's Google account; this repository contains no personal data.
- Dashboards require an admin key; staff check-in requires a personal ID + PIN, with lockout after repeated wrong attempts.
- Location is only requested at the moment of check-in, never in the background, and only with the staff member's consent.
- Gmail's daily sending limit (≈100 emails/day on a standard account) is respected automatically.

## License

Private project for event staffing. All rights reserved.

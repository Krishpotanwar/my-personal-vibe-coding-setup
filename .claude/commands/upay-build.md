Use ultrathink to plan everything before touching any file.

You are building the UPAY NGO event management platform — a fork of https://github.com/Shlok-Dwivedi/Trackly rebranded and extended for UPAY NGO (upayngo.org, Instagram: @ngo_upay). UPAY runs Footpathshala centers, fellowship programs, rural immersion drives, and volunteer events across India.

Stack: React 18 + TypeScript + Vite + Tailwind CSS + shadcn/ui (frontend) · Python Flask + Gunicorn on Render (backend) · Firebase Firestore + Firebase Auth · Supabase storage · FCM + Resend.

---

## BOOTSTRAP (run once)

```bash
git clone https://github.com/Shlok-Dwivedi/Trackly.git upay-trackly
cd upay-trackly
git remote rename origin upstream
git remote add origin <UPAY_GITHUB_REPO_URL>
npm install
cd backend && pip install -r requirements.txt && cd ..
cp .env.example .env
cp backend/.env.example backend/.env
```

---

## RESEARCH RULE

Before writing any code, read the relevant files. Run:

```bash
ls src/pages/ && ls src/components/
cat src/lib/firebase.ts
cat src/types/index.ts
grep -r "committee\|Committee\|enroll\|Enroll\|reports\|Reports" src/ --include="*.tsx" --include="*.ts" -l
```

Write a short impact map: what you will change and what you will NOT touch. Then implement.

---

## TASK 0 — Rebrand Trackly → UPAY

```bash
grep -r "Trackly\|trackly" src/ public/ index.html --include="*.tsx" --include="*.ts" --include="*.html" -l
```

- Replace all "Trackly" display text with "UPAY" in JSX and HTML
- Replace favicon and logo in `public/` with UPAY's logo (fetch from upayngo.org or their Instagram profile picture)
- Update `<title>` and OG meta tags in `index.html`
- Update `name` field in `package.json`
- Run `npm run build` — must exit 0 with zero TypeScript errors

Commit: `rebrand: replace Trackly name and logo with UPAY assets`

---

## TASK 1 — Reports Tab: Event Summary with Charts

When a user clicks an event name in the Reports tab, open a slide-over drawer showing that event's summary with charts.

Research first:
```bash
cat src/pages/Reports.tsx
grep -r "attendance\|enrolled\|summary" src/ --include="*.ts" -l
cat package.json | grep -i "chart\|recharts\|chart.js"
```

Use ultrathink to decide: client-side aggregation vs Flask endpoint, which chart library is already installed (use it, don't add new deps), drawer vs modal vs route.

Steps (one commit each):

**1-A** — Add `getEventSummary(eventId)` service: fetches enrollments, aggregates total enrolled / attended / per-committee headcount / role breakdown.

**1-B** — Create `<EventSummaryPanel>` component:
- Stat cards: enrolled / attended / absent
- Bar chart: committee headcount
- Pie chart: role breakdown (volunteer/staff)
- Event description at top if it exists

**1-C** — Wire click handler on event name in Reports table to open `<EventSummaryPanel>` as a slide-over drawer.

Verify: `npm run build && npm run test` — then manually click an event, confirm drawer opens with charts.

Commit: `feat(reports): add EventSummaryPanel with charts`

---

## TASK 2 — Users Tab: Committee Management

Admins need to create and edit committees (reusable named sub-groups: "Teaching Team", "Logistics", "Outreach"). Committees exist independently of events.

Research first:
```bash
cat src/pages/Users.tsx
grep -r "committee\|Committee" src/ --include="*.ts" --include="*.tsx" -l
```

Firestore schema — write this first to `docs/committee-schema.md`:
```
committees/{committeeId}
  name: string        (required)
  description: string (optional)
  createdAt: Timestamp
  createdBy: uid
  deleted: boolean    (soft delete)
```

Steps:

**2-A** — Firestore service functions: `getCommittees()` real-time listener, `createCommittee(data)`, `updateCommittee(id, data)`, `deleteCommittee(id)` (set `deleted: true`, never destroy data).

**2-B** — Create `<CommitteeManager>` component:
- List of committees with name, description, member count
- "Add Committee" button → inline form (name required, description optional)
- Edit button per row → same form pre-filled
- Delete with confirmation dialog (admins/staff only, never volunteers)

**2-C** — Add `<CommitteeManager>` as a new "Committees" tab in the Users page. Place it after existing tabs, do not reorder anything.

Verify: create → appears in list. Edit → persists. Delete → gone. `npm run build && npm run test`.

Commit: `feat(users): add CommitteeManager with Firestore service`

---

## TASK 3 — Event + Enrollment + Staff Assignment (use /swarm-advanced for this task)

This task has three interconnected sub-features. Use ultrathink to write the full design to `docs/committee-event-design.md` before touching any code. Then spawn a swarm:

```bash
npx ruflo hive-mind spawn "Implement committee system across: (A) event creation form multi-select, (B) enrollment modal committee picker, (C) staff reassignment dropdown in EventDetail" --queen-type tactical
```

**3-A — Event creator assigns committees to an event**

Research:
```bash
cat src/pages/CreateEvent.tsx
grep -r "createEvent\|addEvent\|EventForm" src/ --include="*.ts" -l
```

Add to event Firestore document: `committees: string[]` (array of committeeIds).

Add `<CommitteeSelect>` multi-select to the event creation/edit form — loads from the `committees` collection. Use shadcn `Combobox` or `MultiSelect` if available, no new UI lib.

**3-B — Enrollers pick their committee at enrollment time**

Research:
```bash
grep -r "enroll\|Enroll" src/ --include="*.tsx" --include="*.ts" -l
```

Add `committeeId: string` to the enrollment Firestore document. On the enrollment modal, after clicking Enroll, show a committee picker filtered to only that event's committees. Require selection. If the event has zero committees, skip the picker — do not break existing flow.

**3-C — Staff/admin reassigns a member's committee post-enrollment**

Research:
```bash
grep -r "assignment\|Assignment\|assign" src/ --include="*.tsx" -l
cat src/pages/EventDetail.tsx
```

Add a per-row dropdown on the enrolled members table. On change, update `enrollments/{id}.committeeId` in Firestore. Staff and admin only.

Verify full flow:
1. Create event, attach 2 committees
2. Enroll as volunteer, pick committee A
3. Login as staff, switch that volunteer to committee B
4. Open Reports → committee chart reflects the change
5. `npm run build && npm run test`

Commit: `feat(events): committee multi-select, enrollment picker, staff reassignment`

---

## TASK 4 — UPAY Event Data Seed

Create `backend/scripts/seed_events.py`:

```python
# Usage: python seed_events.py --file data/upay_events.json
# Reads a JSON array and writes each event to Firestore
# Fields: { title, description, date, imageUrl, location, committees[] }
# Add --dry-run flag that prints without writing
```

Create `data/upay_events.json` with at minimum 5 real UPAY events gathered from their Instagram captions (@ngo_upay) and website (upay.org.in). Use public image URLs directly. Do NOT store volunteer personal data — event names, descriptions, and public images only.

Run the script and verify events appear in the Events page.

Commit: `data(seed): UPAY events seed script and initial dataset`

---

## TASK 5 — CSV Export for Power BI

Add "Export CSV" button to the Reports page. On click, generate a CSV client-side (Blob + URL.createObjectURL, no new backend endpoint) with columns:

```
eventName, eventDate, location, totalEnrolled, totalAttended,
committeeName, committeeHeadcount, volunteerName, volunteerRole, attendanceStatus
```

Then create `docs/power-bi-guide.md` explaining how UPAY admins connect to it:
- Get Data → Text/CSV
- Slicers for event / committee / date range
- Card visuals for KPIs (total volunteers, attendance rate)
- Clustered bar chart for committee-wise enrollment across events
- Line chart for volunteer growth over months
- Drill-through from committee name to individual volunteers

Commit: `feat(reports): CSV export for Power BI integration`

---

## CONSTRAINTS

- Never modify `.env` files or commit secrets
- Never delete Firestore collections — use soft delete (`deleted: true`)
- Never change Firebase Auth config
- Prefer editing existing files over creating new ones
- If a bug takes more than 2 attempts to fix, stop and write what you know to `debug/{bug-name}.md`
- Run `npm run build` after every task — ship nothing that fails TypeScript

---

## AFTER EVERY TASK

```bash
npm run build     # must exit 0
npm run test      # all existing tests pass
```

Then commit with this format:
```
feat(scope): short description
```

Start now with TASK 0. Research first.

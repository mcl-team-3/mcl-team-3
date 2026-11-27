# CLAUDE.md - read this first, in every session

## About us
- We are a team of 4-5 people from Mahanadi Coalfields Limited (MCL) at an
  IIM Sambalpur MDP. We are NOT programmers.
- Explain everything in plain English, in short sentences. If you must use a
  technical word, explain it in one line.
- We build ONE small web tool in phases. Only one Claude session works at a
  time. The Progress Log at the end of this file is our handover logbook.

## What we are building
- A tool with at most 3 pages: index.html (entry page), dashboard.html
  (dashboard) and at most one more page.
- Every record has location, urgency (Low / Medium / High) and status
  (Open / In progress / Resolved), plus the columns in "Our tool" below.
- All data is MADE UP. Never add real names, phone numbers, employee IDs or
  real MCL figures.

## Technical rules
1. Plain HTML, CSS and JavaScript only. Pages stay in the top folder; SQL
   files go in the database folder. No frameworks, no npm, no package.json,
   no build step.
2. Vercel publishes the site from the main branch. Use relative links only,
   e.g. href="dashboard.html".
3. Load Supabase from the jsDelivr CDN, then our settings, in this order:
     <script src="https://cdn.jsdelivr.net/npm/@supabase/supabase-js@2"></script>
     <script src="config.js"></script>
   Then create the client like this (do not call the variable "supabase"):
     const db = window.supabase.createClient(window.SUPABASE_URL,
                                              window.SUPABASE_PUBLISHABLE_KEY);
4. The Project URL and the publishable key live only in config.js. Never use
   or ask for a secret key, a service_role key or the database password.
5. For charts, load Chart.js from the jsDelivr CDN.
6. No login or sign-up. Anyone with the link can use the tool.
7. You may not be able to reach our database. Do NOT try to test the database
   connection. Write the code; we test it on the live website.
8. If anything fails, show a friendly message on the page that also includes
   the actual error text, so we can pass it on.
9. Every page must work well on a mobile phone: large buttons, readable text,
   no sideways scrolling. Use the same header and menu on every page.
10. Never delete config.js or CLAUDE.md.

## Database rules
- Our Data Keeper runs all SQL by pasting it into the Supabase SQL Editor.
  You cannot run SQL yourself.
- Give SQL as ONE block that runs in one go. Also save it in the database
  folder: 01-setup.sql, then 02-..., 03-... for later changes.
- One table. It must have: id uuid primary key default gen_random_uuid()
  and created_at timestamptz not null default now().
- Enable Row Level Security. Add policies that let the roles anon and
  authenticated SELECT, INSERT and UPDATE. No delete.
- Always include: grant select, insert, update on the table to anon,
  authenticated; (new Supabase projects need it, or the website gets
  "permission denied").
- Never drop a table or delete rows.
- Avoid changing the table after Phase 1. If a change is really needed, give
  one small block and explain it in one sentence.

## How to work with us
- Make one change at a time. Do not change parts that already work unless we
  ask.
- After each change, reply in 3 short bullets: what you changed and what we
  should test on the live website.
- Commit and push your work at every stopping point.

## Takeover and handover
- At the START of every session: read the Progress Log below and summarize it
  in 3 bullets (what exists, what works, what is next).
- At a "save point": add a new entry at the end of the Progress Log (phase,
  builder, what was built, what works, known problems, next step). Then
  commit and push.

## Our tool (filled in during Phase 1)
- Team: MCL team 3
- Tool name: CIL Transfer Matching
- Problem: CIL issues transfer orders every November against grade vacancies
  in the subsidiaries, but postings are made without criteria. The tool
  matches each executive's field of expertise and years of experience to
  what each subsidiary needs, and suggests postings for higher management.
- Who records / who decides: HR uploads the HRMS employee export and the
  requirement list from management; higher management approves, holds or
  rejects each suggested posting.
- Table name and columns: none yet. Data is loaded from CSV files in the
  browser (templates/ folder) and kept in the browser only. Employee columns:
  EmpID, Name, Grade, Discipline, Subsidiary, Unit, Department, DateOfBirth,
  DateOfJoining, GradeSince, PostingSince, GradeHistory, Expertise,
  PreferredSubsidiaries, Exemption, SensitivePost, SensitivePostSince.
  Requirement columns: ReqID, Subsidiary,
  Unit, Department, Grade, Discipline, RequiredExpertise, MinYears,
  Positions, Priority, Remarks.
- Pages: index.html = the whole tool. Sign-in screen, then a left menu
  (drawer on phones) with: Home, Dashboard, Posting suggestions, Sensitive
  & promotion, Employees, Transfer flow, Data, Rules. Each is a section of
  index.html reached by #name in the address (e.g. #dashboard), so the
  3-page limit still holds. Light / Dark / Auto theme switch in the menu.
  Office logins also get Departments and Seniority. Executives sign in with
  their EmpID (EIS number) and see: My home, My posting choices, Seniority,
  Transfer policy. English / हिंदी switch in the top bar. "Sahayak" help
  chatbot button at bottom right on every page.

## Progress Log (newest entry at the bottom)
- Phase 0 (starter): placeholder index.html, config.js without settings and
  this CLAUDE.md. Next: Phase 1 - the table and the entry page.
- 2026-11-27 (builder: Claude): replaced the placeholder index.html with the
  CIL Transfer Matching tool, plus CSV templates in templates/. Works: loads
  110 made-up executives and 37 made-up vacancies; filters by grade,
  discipline, tenure, retirement and exemptions; scores fit out of 100;
  assigns postings with the Hungarian method; override, Approve/Hold/Reject,
  transfer-flow matrix, adjustable rules, CSV upload, decision sheet copy
  for Excel. Known gaps: does not use Supabase yet, so decisions are saved
  only in each person's own browser. Next: save decisions to a Supabase
  table if the team wants shared decisions.
- 2026-11-27 (builder: Claude): added (1) demo login IDs with no password:
  cil.hq (review & approval) and one per subsidiary, e.g. mcl.hq
  (updation). Subsidiary logins add/edit/remove/upload only their own rows
  and see everything else read-only; only cil.hq can approve, override and
  change rules. Not real security: anyone can pick any ID (team chose this
  over Supabase logins; rule 6 "no login" still holds). (2) Sensitive-post
  flag: SensitivePost + SensitivePostSince columns; over 3 years is flagged
  and placed first in matching; new "Sensitive posts & promotion" tab.
  (3) Promotion: seats left after transfers go to executives one grade
  below with at least 3 years in grade. Works in local tests. Known gaps:
  data and decisions still stay in each browser, so a subsidiary's updates
  do not reach CIL HQ's screen. Next: shared storage in Supabase.
- 2026-11-27 (builder: Claude): redesigned the interface like an HR portal
  (left menu as in BambooHR, "Awaiting your action" list as in Workday).
  New full-page sign-in, Home page (greeting, cycle progress, tasks for the
  logged-in office, policy summary, quick links), Dashboard (KPI tiles,
  decision progress bar, Chart.js charts with a "Show as table" option,
  expertise gaps), menu badges, Light/Dark/Auto theme. Matching logic not
  changed. Works in local tests; charts need the live site (Chart.js loads
  from jsDelivr). Known gaps: same as before (data stays in each browser).
- 2026-11-27 (builder: Claude): added (1) executive login by EmpID with no
  password (demo); executives give up to 3 posting choices (subsidiary +
  optional area). Choice 1 counts 100%, 2 counts 75%, 3 counts 50% in the
  "Stated preference" weight, replacing the PreferredSubsidiaries column.
  (2) Seniority list per grade and cadre, CIL-wide: earlier entry into grade
  first, then earlier joining, then older. (3) Departments page: department
  x grade counts, click a department for its executives. (4) English/Hindi:
  Hindi text comes from the HI dictionary and HI_RX patterns in index.html;
  any English text not listed there stays English. (5) "Sahayak" chatbot:
  rule-based (keyword matching), answers from the tool's own rules and data,
  can open pages, switch language and theme. No AI service or API key.
  Known gaps: data, choices and decisions still stay in each browser, so an
  executive's choices only reach CIL HQ on the same computer.

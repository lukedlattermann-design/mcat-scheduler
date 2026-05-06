---
name: mcat-scheduler
description: >
  Generate a personalized MCAT study schedule as a fully-formatted .xlsx Excel file.
  Use this skill whenever the user asks to create, build, or generate an MCAT study schedule
  or calendar for a student — even if they just say "new student schedule" or "build a plan."
  Also triggers when populating the MCAT Excel calendar, setting up a study plan before an
  exam date, or asking about distributing Kaplan chapters or AAMC practice materials across
  weeks. This skill is purpose-built for MCAT tutors who need to produce a ready-to-use
  Excel file at the end of a first tutoring session.
---

# MCAT Personalized Study Schedule Generator

## On Invocation — Read These First

Before doing anything else, read both reference files:
- `references/aamc-assignments.md` — the complete AAMC assignment sequence with minutes (strict order, never skip or rearrange)
- `references/kaplan-chapters.md` — all 72 Kaplan chapters with subject colors and yield star ratings

## Purpose

At the end of a first tutoring session, generate a complete week-by-week MCAT study plan as an `.xlsx` file. The file replicates the tutor's existing template exactly: color-coded chapter assignments, date formulas, AAMC practice phase, tutor session labels, and a full Kaplan chapter palette on the right side.

---

## Step 1 — Collect All Required Inputs

Do not generate the file until you have confirmed all twelve inputs. Ask for any that are missing.

1. **Start date** — must be a Sunday; if the student gives a non-Sunday date, round back to the prior Sunday
2. **Exam date** — exact date
3. **Daily study hours** — hours available each day of the week (Sun–Sat); these can vary by day
4. **Total tutoring hours purchased** (e.g., 25 hours)
5. **Tutoring session day(s) and time(s)** — which weekday(s) and what time tutor sessions occur
6. **Subject strengths** — which of the 6 subjects the student is strongest in
7. **Subject weaknesses** — which subjects need the most work
8. **Non-AAMC materials available** — which Kaplan books (Gen Chem, Orgo, Physics, Bio, Biochem, Behavioral Sci) and any other materials (Examkrackers, Blueprint, Khan Academy, etc.)
9. **Number of AAMC Practice Exams available** — 4, 5, or 6 FL exams; exclude any the student has already completed
10. **Days off / vacations** — any dates or ranges when the student is fully unavailable; if none, confirm explicitly
11. **UWorld access** — can the student afford UWorld? (yes/no); if yes, UWorld question bank will fill Phase 2 as the non-AAMC practice bridge between content review and the AAMC sequence
12. **CARS help** — would the student like dedicated CARS practice? (yes/no); if yes, "Jack Westin Daily CARS Passage" is added to every Phase 1 and Phase 2 study day

---

## Step 2 — Planning Logic

### Calculate week count
- Total weeks = ceil((exam_date − start_date) / 7)

### Three-Phase Model

Every MCAT schedule has three sequential phases. **Always plan backward from the exam date**,
placing Phase 3 first, then dividing the remaining time between Phase 2 and Phase 1.

- **Phase 1 — Content Review**: Kaplan chapter reading (max 3 chapters/day, ~1.5 hrs each)
- **Phase 2 — Non-AAMC Practice**: UWorld question bank; bridges content review and the AAMC sequence
- **Phase 3 — AAMC Practice**: Official AAMC assignments in strict prescribed order

---

### Phase 3: AAMC Practice Phase (plan first — backward from exam)

- Phase 3 ends **3 days before the exam date** (acceptable range: 2–4 days)
- Load the full AAMC assignment sequence from `references/aamc-assignments.md` — strict order, never rearrange
- **FL Practice Tests** must land on Saturday or Sunday (the day with the most available hours); each FL day is consumed entirely by test + review (7.5 hrs), with no other assignments on that day
- **Non-FL items**: working backward from the last Phase 3 day, distribute items in order across available non-FL days; each day receives items that fit within ~50% of that day's available hours. List only the assignment name — do NOT add "review (XX min active)" or any sub-bullets.
- Include only the FL exams the student has purchased and not yet taken
- Phase 3 start date = the earliest day on which any AAMC assignment lands
- No CARS (Jack Westin) entries in Phase 3 — the AAMC materials supersede all other practice

### Phase 2: Non-AAMC Practice (plan second)

- Phase 2 fills all days between Phase 1 end and Phase 3 start
- If UWorld = yes (input 11): label each Phase 2 study day "UWorld: 20–30 Qs – [most recent subject from Phase 1]" in dark orange font `FF980000`
- If UWorld = no: Phase 2 consists of Khan Academy MCAT practice or tutor-directed review
- If CARS = yes (input 12): add "Jack Westin Daily CARS Passage" to every Phase 2 study day
- No Kaplan chapters in Phase 2

### Phase 1: Content Review Phase (plan last — forward from start date)

- Phase 1 fills all days from start_date up to (but not including) Phase 2 start
- Distribute the 72 Kaplan chapters across study days using this mandatory ordering:
  - **ALWAYS START WITH GENERAL CHEMISTRY** — all 12 Gen Chem chapters in conceptual order (1–12), assigned first regardless of strength or weakness
  - **After Gen Chem**: apply weak-first / strong-last logic for the remaining 5 subjects:
    - Weak subjects: front-load their chapters; within each subject schedule highest-yield first
    - Strong subjects: schedule in the back half of Phase 1
  - Mix subjects across each week — avoid devoting a full day to one subject if possible
  - **Maximum 3 chapters per study day**, regardless of available hours (~1.5 hrs per chapter)
  - Chapter label format on the calendar: **"[Subject] [N]"** — e.g., "Gen Chem 1", "Biology 3", "Psychology/Sociology 5". Do NOT include star ratings or yield indicators.
  - Chapter cells: fill with subject color (see color table in Step 3); font color red `FFFF0000`
- If CARS = yes (input 12): add "Jack Westin Daily CARS Passage" to every Phase 1 study day as an additional entry beneath the chapter assignments

### Tutor sessions
- Place tutor sessions on the designated day(s) throughout the entire schedule (all three phases)
- Spread total tutoring hours evenly across weeks: (total hours) ÷ (total weeks) = target hrs/week
- Label each session in blue font `FF0000FF`: e.g., `Tutor Session (2 hrs) – 10 AM`
- Tutor sessions on blocked (vacation) days: label as `(reschedule TBD)` unless told otherwise

### Days off / vacations
- Mark every blocked date with label `Personal / Day Off` and fill `FFCFE2F3` (light blue)
- Never assign chapters, UWorld, AAMC content, or CARS on blocked days
- Phase 1: chapter distribution simply skips blocked days
- Phase 3: if a blocked day held AAMC content, carry those assignments to the next available day; raise that day's AAMC budget to up to 75% of available hours
- If vacations make it impossible to complete Phase 3 before the exam, write a **bold warning** in cell A1 of the Calendar sheet flagging the issue

### Gap filling rule
- **Never leave an available-hours day blank.** Any day in the schedule that has study hours but no assigned content must be filled.
- In Phase 3 inter-FL windows where there are fewer AAMC items than available days: fill empty days with "UWorld" (if UWorld = yes) or "High-Yield Kaplan Review – [most recent subject]" (if UWorld = no).
- This also applies to any other phase where gaps appear — prefer high-yield Kaplan review or UWorld over leaving a day empty.

---

## Step 3 — Build the Excel File with openpyxl

### Sheet names (preserve all)
- `Calendar` — primary output (build this)
- `AAMC Questions` — reference table (pre-populate from `references/aamc-assignments.md`)
- `Scores` — score tracker (create with headers: Date, Exam, C/P, CARS, B/B, P/S, Total)
- `ChemPhys`, `CARS`, `BioBiochem`, `PsychSoc` — subject tabs (create with subject name as header)

### Calendar sheet — header block (rows 1–6)

| Cell | Content | Style |
|------|---------|-------|
| F1 | "Legend" | bold |
| G1 | "Personal (e.g. vacation, exams)" | fill FFCFE2F3 |
| G2 | "Content Review" | font color FFFF0000 |
| H2 | "Practice Questions" | font color FF980000 |
| G3 | "Tentative tutoring appointment" | font color FF0000FF |
| H3 | "Question Review" | font color FF38761D |
| G4 | "Full Length Practice Test" | font color FFFF9900 |
| H4 | "Video Review" | font color FF674EA7 |
| A3 | "Starting Sunday" | bold |
| A4 | start date value | fill FFFFFF00, bold |

Row 6 day headers — B6:H6 = Sunday, Monday, Tuesday, Wednesday, Thursday, Friday, Saturday — all bold

### Per-week block structure

Each week occupies a group of rows:

**Week label row** (e.g., row 7 for week 1):
- Col A: "Week N" — bold, alternating fill FFD9D9D9 / FFCCCCCC
- Cols B–H: date strings for each day (Sunday through Saturday of that week)
  - **Always store dates as plain Python strings, never as `datetime` objects or Excel formula strings**
  - Format: `f"{d.month}/{d.day}/{d.year}"` — e.g., "5/3/2026" (no zero-padding)
  - Example: `ws['B7'] = f"{date(2026, 5, 3).month}/{date(2026, 5, 3).day}/{date(2026, 5, 3).year}"`
  - Reason: `datetime` objects with `number_format='M/D/YYYY'` render as "7/D/YYYY" in Numbers and LibreOffice
- Same fill as col A, bold

**Day-name row** (immediately after week label row):
- Col A: blank
- Cols B–H: "Sunday" through "Saturday" — fill FFC9DAF8

**Content rows** (as many as needed):
- Cols B–H: study assignments for each day
- Chapter cells: fill with subject color (see color table below)
- Tutor session text: blue font `FF0000FF`
- AAMC practice questions: dark orange font `FF980000`
- AAMC review: green font `FF38761D`
- FL test: orange font `FFFF9900`
- Blocked days: fill `FFCFE2F3`

One blank row separates each week block.

### Subject color fills (use these exactly)

| Subject | Cell Fill (hex) |
|---------|----------------|
| General Chemistry | FFEA9999 |
| Organic Chemistry | FFFFE599 |
| Physics / Math | FF9FC5E8 |
| Biology | FFB6D7A8 |
| Biochemistry | FFA4C2F4 |
| Behavioral Sciences / Psych-Soc | FFD5A6BD |

### Kaplan chapter palette (columns J–Z)

Place to the right of the weekly schedule starting around column J:
- Header: "Kaplan Chapters" (bold)
- Six subject columns, each headed with the subject name in bold with its subject fill color
- Under each header: chapter labels in the format "[Subject] [N]" (e.g., "Gen Chem 1", "Biology 3", "Psychology/Sociology 5"), each cell filled with the subject color. Do NOT include star ratings.
- This palette is always present regardless of which books the student owns

---

## Step 4 — Save, Recalculate, and Deliver

1. Save file as `/mnt/user-data/outputs/[StudentFirstName]_MCAT_Schedule.xlsx` (or current directory if that path doesn't exist)
2. Run formula recalculation:
   ```bash
   python scripts/recalc.py [output_path]
   ```
   If `scripts/recalc.py` is not present in this skill's directory, look for it in the xlsx skill or write a LibreOffice-based recalc inline
3. Verify zero formula errors in the JSON output; fix any that appear
4. Present the file to the user with `present_files` if available, otherwise state the file path

---

## Important Notes

- **Never reorder AAMC assignments.** The sequence in `references/aamc-assignments.md` is clinically designed.
- **Never skip input collection.** The schedule cannot be personalized without all 12 inputs.
- **Chapter load**: ~1.5 hrs per Kaplan chapter; maximum 3 chapters per study day. Use `floor(available_hrs / 1.5)` capped at 3.
- **Chapter labels**: "[Subject] [N]" only — no star ratings, no yield indicators on calendar cells.
- **Planning order**: backward first (Phase 3 from exam-3 days), then forward (Phase 1 from start, Phase 2 fills gap).
- **Phase 3 gap**: the last AAMC assignment must fall 2–4 days before exam day — leave a buffer.
- **No review sub-bullets in Phase 3**: list only the assignment name. Do not add "review (XX min active)" or similar.
- **CARS**: "Jack Westin Daily CARS Passage" goes on every Phase 1 and Phase 2 study day (never Phase 3) when input 12 = yes.
- **Tutor hours spread evenly**: if a student has 25 hours over 15 weeks, target ~1.67 hrs/week.
- **FL test timing**: each FL exam takes ~3.75 hrs to complete + 3.75 hrs review = 7.5 hrs total.
- **Date values**: store all dates as plain strings using `f"{d.month}/{d.day}/{d.year}"` (e.g., "5/3/2026"). Never use `datetime` objects with `number_format` — those render as "7/D/YYYY" in Numbers and LibreOffice. Never use Excel formula strings.

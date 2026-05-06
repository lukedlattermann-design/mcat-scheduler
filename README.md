# mcat-scheduler

Part of the **MCAT AI Tutoring System** — a three-skill suite for Claude Code that gives pre-medical students a complete, expert-validated MCAT preparation pipeline.

## What this skill does

Generates a fully personalized, color-coded MCAT study schedule as an `.xlsx` Excel file. Collects 12 student-specific inputs (exam date, daily study hours, subject strengths and weaknesses, available materials, tutoring schedule, vacation days, and more) and produces a three-phase study plan:

- **Phase 1 — Content Review**: All 72 Kaplan chapters distributed across study days, starting with General Chemistry and prioritizing weak subjects
- **Phase 2 — Non-AAMC Practice**: UWorld question bank sessions bridging content review and official materials
- **Phase 3 — AAMC Practice**: Official AAMC assignments in their prescribed sequence, with full-length exams placed on weekends

The schedule is rendered as a color-coded Excel workbook with subject fills, tutor session labels, and a full Kaplan chapter reference palette.

## Prerequisites

```bash
pip install openpyxl
```

## Install this skill

```bash
npx skills add lukedlattermann-design/mcat-scheduler
```

## Install the full three-skill system

For the complete workflow — schedule generation, daily check-ins, and on-demand slideshows — install all three skills:

```bash
npx skills add lukedlattermann-design/mcat-scheduler
npx skills add lukedlattermann-design/mcat-daily-checkin
npx skills add lukedlattermann-design/mcat-slideshow
```

## How the full system works

1. **`/mcat-scheduler`** — Run once at the start of your prep. Collects your details and generates a personalized week-by-week schedule as an `.xlsx` file.
2. **`/mcat-daily-checkin`** — Run every study day. Reads your schedule, tells you exactly what to study today, and asks if you are struggling with anything.
3. **`/mcat-slideshow [topic]`** — Triggered automatically by the daily check-in when you flag a struggling topic, or run manually at any time. Generates a tutor-quality `.pptx` slideshow with concept slides, worked examples, AAMC-style practice questions, and an answer key.

## Designed for

- Pre-medical students preparing for the MCAT who do not have access to professional tutoring
- MCAT tutors who want to automate schedule generation and session material preparation

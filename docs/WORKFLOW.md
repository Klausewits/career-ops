# Career-Ops Workflow

```
┌─────────────────────────────────────────────────────────────────────┐
│                        CAREER-OPS PIPELINE                          │
└─────────────────────────────────────────────────────────────────────┘

 ┌──────────┐     ┌──────────────┐     ┌──────────────┐     ┌────────┐
 │  FIND    │────▶│  PRE-FILTER  │────▶│  EVALUATE    │────▶│DECIDE  │
 └──────────┘     └──────────────┘     └──────────────┘     └────────┘
      │                  │                    │                  │
      ▼                  ▼                    ▼                  ▼
 /career-ops        portals.yml          /career-ops        Score ≥ 4.0
    scan           title_filter           pipeline            → APPLY
      +            location_filter           +
 manual paste      (auto, at scan)      liveness sweep     Score < 3.5
      │                                      +               → SKIP
      │                                  A-F scoring
      ▼                                      │
 data/pipeline.md ◀─────────────────────────┘
 (URL inbox)            surviving URLs
                        written here
                        by scanner



STAGE 1 — FIND
──────────────
Source A: /career-ops scan
  • Reads portals.yml (your 70 DoD companies)
  • Hits Greenhouse / Lever / Ashby APIs (zero tokens)
  • Falls back to WebSearch for Workday / other ATS
  • Applies title_filter + location_filter automatically
  • Deduplicates against data/scan-history.tsv
  → Writes matching URLs to data/pipeline.md ## Pending

Source B: Manual paste
  • You find a role on LinkedIn / ClearanceJobs / USAJobs
  • Paste URL into data/pipeline.md as:
      - [ ] https://... | Company | Role
  → Goes into the same inbox


STAGE 2 — PRE-FILTER (happens inside scan, before pipeline.md)
─────────────────────────────────────────────────────────────
Automatic filters (portals.yml):
  • title_filter.positive — must match ≥1 keyword
  • title_filter.negative — must match 0 keywords
  • location_filter — blocks Houston metro + international
  • salary_filter — $100K floor

Manual curation (optional, edit pipeline.md before running pipeline):
  • Remove roles you can already see are off-target
  • Happens BEFORE /career-ops pipeline
  • What we did today with the NG/Palantir/Cohere trimming


STAGE 3 — EVALUATE (/career-ops pipeline)
──────────────────────────────────────────
For each URL in data/pipeline.md ## Pending:

  1. Liveness sweep — node check-liveness.mjs (zero tokens)
     • Expired → marked [x] ~~strikethrough~~ → Processed
     • Active → continues

  2. JD fetch — Playwright → WebFetch → WebSearch

  3. Disqualifier check (from modes/_profile.md):
     • Location hard reject (Houston / hybrid outside Austin-SA)
     • Deep technical domain required
     • Wrong seniority / wrong function
     → Immediate SKIP, no full report

  4. Full evaluation — Blocks A–F:
     A. CV match — skills, proof points, exact lines cited
     B. North Star — archetype fit vs your 4 DoD archetypes
     C. Comp — salary vs $130K–$200K target
     D. Culture — company health, remote policy, mission
     E. Red flags — ghost posting, stale, structural problems
     F. Global score — weighted 1–5

  5. Block G — Posting legitimacy (separate from score)

  6. Report written → reports/NNN-company-YYYY-MM-DD.md

  7. Tracker entry → data/applications.md (via TSV merge)

  8. URL moved → data/pipeline.md ## Processed


STAGE 4 — DECIDE
─────────────────
Score 4.5+  Strong match → apply immediately
Score 4.0–4.4  Good match → apply
Score 3.5–3.9  Borderline → apply only with specific reason
Score < 3.5   Recommend against → skip

Review: /career-ops tracker → dashboard view of all scores/statuses


STAGE 5 — APPLY (for roles scoring 4.0+)
──────────────────────────────────────────
/career-ops pdf {slug}      → tailored CV PDF
/career-ops cover           → cover letter
/career-ops apply           → live form-fill assistant (browser)
/career-ops contacto        → LinkedIn outreach to recruiter/hiring mgr
/career-ops interview-prep  → company-specific prep doc


STAGE 6 — TRACK
─────────────────
data/applications.md        → master tracker (all evaluated roles)
data/follow-ups.md          → follow-up cadence
/career-ops followup        → calculates overdue follow-ups
/career-ops patterns        → rejection pattern analysis


CYCLE
─────
Run scan every 3–7 days → new URLs land in pipeline.md → run pipeline
Cadence: scan → curate → evaluate → apply → follow up → repeat
```

---

## Key Files (where things live)

```
data/
  pipeline.md          ← URL inbox (Stage 1 output, Stage 3 input)
  applications.md      ← Master tracker (Stage 3 output)
  scan-history.tsv     ← Dedup history (prevents re-scanning same URLs)
  follow-ups.md        ← Follow-up log

config/
  profile.yml          ← Your identity, comp targets, contact info

modes/
  _profile.md          ← YOUR rules: archetypes, scoring, location, disqualifiers
  _shared.md           ← System scoring logic (auto-updated, don't edit)
  oferta.md            ← Evaluation mode
  pipeline.md          ← Pipeline processing mode
  scan.md              ← Scanner mode
  (+ others)

reports/               ← Generated evaluation reports (NNN-company-date.md)
output/                ← Generated CV PDFs
interview-prep/        ← STAR stories + company-specific prep docs

portals.yml            ← Your 70-company scan list with filters
```

---

## Known Structural Issue

The project root has 20+ `.mjs` scripts at the top level with no grouping.
These are upstream system files (auto-updated) — reorganizing them would
break the update path. Tracked as a known issue for the upstream project.

Your personal files are clean:
- **Config:** `config/` and `portals.yml`
- **Data:** `data/`
- **Output:** `reports/` and `output/`
- **Prep:** `interview-prep/`
- **CV:** `cv.md` + variants at root (by convention)

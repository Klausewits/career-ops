# Personal Data Inventory

Files in this repo that contain personal data. All are gitignored and protected by a pre-commit hook.

**Never push these to GitHub.** The fork is public.

## Protection layers

1. **`.gitignore`** — prevents `git add` from staging these files
2. **`.git/hooks/pre-commit`** — blocks commit if any are staged (catches `git add --force`)
3. This doc — reference for what exists and why it's sensitive

*Note: The pre-commit hook lives in `.git/hooks/` and is local only — it does not travel with the repo when someone forks or clones. Each user must set up their own hook.*

---

## User-layer files (personal data)

| File | Contains |
|------|----------|
| `cv.md` | Canonical CV — full work history, contact info |
| `cv-pm-dod.md` | CV variant: Senior Program Manager DoD |
| `cv-sm-dod.md` | CV variant: Senior Scrum Master / RTE DoD |
| `cv-consultant.md` | CV variant: Senior Program Consultant Federal |
| `article-digest.md` | Proof points from programs — real metrics, program names, agency names |
| `voice-dna.md` | Writing voice guardrail — personal style rules |
| `portals.yml` | Company watchlist, scan queries — personal targeting strategy |
| `Search Criteria.md` | Search criteria and scoring rules (Obsidian source of truth) |
| `config/profile.yml` | Identity, contact info, compensation targets, archetypes |
| `modes/_profile.md` | Archetypes, negotiation scripts, location policy, scoring rules |
| `data/applications.md` | Application tracker — company names, scores, status, dates |
| `data/pipeline.md` | Pending job URLs |
| `data/scan-history.tsv` | Scan dedup history |
| `data/follow-ups.md` | Follow-up tracker |
| `data/Company Watchlist.md` | Curated DoD/federal company watchlist with tiers and ATS notes |
| `interview-prep/story-bank.md` | STAR+R stories — real program names, outcomes, personal reflections |
| `interview-prep/{company}-{role}.md` | Company-specific interview prep notes |
| `writing-samples/` | Personal writing samples |
| `reports/` | Evaluation reports — company names, JD content, scores |
| `output/` | Generated CV PDFs |
| `jds/` | Saved job descriptions |
| `.env` | API keys / secrets |
| `auth/linkedin_cookies.json` | LinkedIn session auth |

---

## If the pre-commit hook is missing

The hook lives in `.git/hooks/pre-commit` (local, not committed). If you clone or reset the repo, recreate it:

```bash
# From the repo root:
cat .git/hooks/pre-commit   # verify it exists
chmod +x .git/hooks/pre-commit  # ensure it's executable
```

Or ask Claude Code to recreate it: *"recreate the personal data pre-commit hook."*

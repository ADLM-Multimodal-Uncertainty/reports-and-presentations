# Notion workspace setup

Create one teamspace "ADLM WiSe26/27 - Multimodal Epistemic Uncertainty" and the pages below.
Everything that is a list of similar things is a database (table / board view), everything else is a page.
Keep it flat: one level of nesting under the home page.

## Home page

Pinned at the top:
- Research question (one sentence) and the dEU definition.
- Links: GitHub repo, W&B project, shared cluster directory path, supervisor Slack channel.
- Next deadline with countdown (04.11 P1, 16.12 P2, 05.02 code, 10.02 poster, 24.02 report).
- Buttons/links to: Roadmap, Weekly log, Tasks, Experiments, Literature, Decisions, Ideas, Presentation prep.

## 1. Roadmap (database, timeline view + table view)

Properties
- Milestone (title)
- Phase (select: P0 kickoff, P1 baseline, P2 iterate, P3 write-up)
- Due (date)
- Owner (person)
- Status (select: not started / in progress / done / slipped)
- Depends on (relation to Roadmap)
- Notes (text)

Seed with the milestones from `roadmap.md` (M0.1 ... M2.5, poster and report drafts).

## 2. Weekly log (database, list view sorted by week desc)

One page per calendar week, created Monday. Template:

    # Week <n> (<Mon date> - <Sun date>)
    ## What worked
    ## What did not work
    ## Blocked on
    ## Decisions made this week (link to Decisions db)
    ## Supervisor meeting (<date>)
    - Attendees:
    - We presented:
    - Feedback:
    - Action items (checkbox list with owner and due date):
    ## Next week
    ## Numbers that changed (link W&B / Experiments rows)

Properties: Week (title), Dates (date range), Scribe (person), Supervisor meeting held (checkbox).

This log is the raw material for the final poster and the "reasoning behind decisions" part of the report.

## 3. Tasks (database, board view grouped by Status; second view "My tasks" filtered by Owner = me)

Properties
- Task (title)
- Status (select: Backlog / This week / In progress / Review / Done)
- Owner (person)
- Area (select: data, model, uq, infra, literature, presentation, report)
- Milestone (relation to Roadmap)
- Due (date)
- GitHub issue (url)
- Size (select: S < 2h, M half day, L 1-2 days, XL needs splitting)
- Blocked by (relation to Tasks)

Rules
- Every task has exactly one owner. Pair work is still one owner.
- "In progress" is limited to two tasks per person.
- A task that is XL gets split before it leaves Backlog.
- Anything that needs code goes to GitHub as an issue too; paste the link.

## 4. Experiments (database, table view; group by Design)

One row per logical experiment (not per fold, not per seed). Properties
- Name (title), convention: `<design>-<model>-<uq>-<variant>` e.g. `d1-fusion-ens5-full-meta`
- Design (select: D1 paired models / D2 modality dropout / D3 control / tabular only / other)
- Model (select: image-only / fusion / modality-dropout)
- UQ method (select: ensemble / mc-dropout / laplace / ddu / conformal / none)
- Metadata variant (select: full / permuted / subset-demographic / subset-symptoms / corrupted / none)
- Commit (text, 7-char hash)
- Config path (text)
- W&B link (url)
- Folds x seeds (text, e.g. 5x5)
- BACC (number), Macro AUC (number), ECE (number)
- Mean EU (number), Mean AU (number), Mean dEU (number), dEU 95% CI (text)
- Status (select: planned / running / done / failed / superseded)
- Owner (person)
- Conclusion (text): one sentence on what this experiment changed in our understanding
- Started / finished (dates)

Rule: a run without a committed hash and a W&B link is not an experiment, it is debugging.

## 5. Literature (database)

Columns from `literature.md` section 3:
paper (title) | year | venue | task / data | UQ method | modalities | EU measure | split strategy |
key result | relevance (1-5) | reader (person) | status (to read / skimmed / read / summarised) |
PDF (file or link) | notes (page body)

Seed with the 6 papers in `resources/` and the "papers to add" list.

## 6. Decisions log (database, table view sorted by date)

Properties: Decision (title), Date, Context (text), Alternatives considered (text), Chosen because (text),
Revisit if (text), Made by (person), Related experiment (relation).

Seed entries
- Patient-grouped 5-fold CV instead of image-level split (DermaCalibra uses image-level: leakage).
- Deep ensembles as first EU estimator (Lakshminarayanan 2017; Hoarau 2026 use it; strongest in benchmarks).
- Information-theoretic decomposition (TU/AU/EU) as primary measure; variance-based as a check.
- No evidential deep learning as primary method (Bengs 2023 critique; Hoarau 2026 exclude it).
- ResNet50 ImageNet-pretrained backbone (DermaCalibra, Pundhir et al.: best on PAD-UFES-20 size).
- ISIC 2019 only after P2 (supervisor instruction).

## 7. Ideas (database, simple list)

Properties: Idea (title), Proposed by, Date, Status (parked / exploring / adopted / rejected),
Why / why not (text). Anything anyone thinks of goes here first; the Monday sync promotes ideas to tasks.

## 8. Presentation and report prep (pages with checklists)

- P1 checklist (from `presentations/p1/outline.md`).
- P2 checklist: baseline numbers table, issues list, decisions table, what we need from the audience.
- Poster checklist: figure list, who makes which figure, print deadline.
- Report checklist: MICCAI template, section owners, contribution statement, deadline 24.02 12:00.

## Weekly use

- Monday 30 min: review board, move cards, create the week's log page.
- During the week: update Experiments rows as runs finish; write decisions the day they are made.
- Supervisor meeting: open the week page, fill it live; action items become Tasks.
- Friday: each person writes three lines in the week page.

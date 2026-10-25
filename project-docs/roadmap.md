# Roadmap

Project: The Value of a Second Modality: Epistemic Uncertainty in Multimodal Medical Image Classification
Supervisor: Raphael (Chair for AI in Medicine and Healthcare, TUM)
Team: Larissa Manalil, Yannic Neu, Emre Kalkan

## Research question

Does adding clinical metadata to a skin-lesion image classifier reduce the model's
*epistemic* uncertainty? Per sample:

    dEU = EU(image) - EU(image + metadata)

Is dEU > 0, ~0, or < 0? A null result is a valid result. Secondary: does dEU differ for
correct vs incorrect predictions, common vs rare classes, informative vs weak metadata,
clean vs corrupted metadata?

Dataset: PAD-UFES-20 (2298 smartphone images, 1373 patients, 1641 lesions, 6 classes).
ISIC 2019 is reserved for external validation later; do not use it before Presentation 2.

## Deadlines and weights

| Date              | Event                | Weight | What is expected                                      |
|-------------------|----------------------|--------|-------------------------------------------------------|
| 04.11.2026        | Presentation 1       | 15%    | Introduction to the topic, EDA, first ideas           |
| 16.12.2026        | Presentation 2       | 15%    | Baseline + first approaches, encountered issues       |
| 05.02.2027 12:00  | Code submission      | 20%    | Reproducible repo, configs, README, no notebooks      |
| 10.02.2027 16:00  | Poster session       | 20%    | Summary of the whole project, Q&A                     |
| 24.02.2027 12:00  | Report (6-8 pages)   | 30%    | MICCAI template, decisions + reasoning, contributions |

Presentations: 7 min + 2-4 min questions, one presenter per group, grade is shared.
One joker (one missed session) per person.

## Phases and milestones

### Phase 0: now -> 04.11 (Presentation 1)

Milestones
- M0.1 Everyone has cluster access (VPN, ssh selene, one GPU sbatch job finished).
- M0.2 PAD-UFES-20 images downloaded to the shared project directory; EDA figures produced.
- M0.3 Patient-grouped 5-fold split committed and leakage-tested.
- M0.4 Literature matrix with the 6 given papers + at least 6 new ones.
- M0.5 One-page project abstract, method diagram, experimental design agreed with supervisor.
- M0.6 P1 slides rehearsed to 7 minutes.

### Phase 1: 05.11 -> 16.12 (Presentation 2)

Milestones
- M1.1 Training pipeline runs end to end on the cluster from a config (image-only).
- M1.2 Fusion model (image + metadata) trains with the same backbone, seeds, schedule.
- M1.3 Deep ensemble (5 seeds x 5 folds) for both models; TU/AU/EU computed; first dEU numbers.
- M1.4 Modality-dropout model (single model, evaluate with/without metadata) implemented.
- M1.5 Known issues list and final decisions about which UQ methods and controls to keep.

### Phase 2: 17.12 -> 05.02 (Code submission)

Milestones
- M2.1 Second and third UQ methods (MC dropout, last-layer Laplace) on the same checkpoints.
- M2.2 Controls: permuted metadata, metadata subsets, corrupted metadata.
- M2.3 Secondary questions answered with stratified plots and paired statistics.
- M2.4 ISIC 2019 external validation (if time permits and supervisor agrees).
- M2.5 Code freeze: configs for every reported run, README reproduces the main table.

### Phase 3: 06.02 -> 24.02 (Poster, report)

- Poster drafted by 03.02 (before code freeze), final 09.02.
- Report outline by 10.02, full draft 17.02, final 23.02.
- Contribution statement per team member.

## To-dos until Presentation 1 (10 days)

Everyone, day 1-2
- Read Hullermeier & Waegeman 2021 sections 1-3 and 5 (concepts, entropy decomposition).
- Read Hoarau et al. 2026 section 3.5 and Appendix A (the exact EU/AU formulas we reuse).
- Skim Kendall & Gal 2017 and Lakshminarayanan et al. 2017.
- Agree on the definitions: TU = H(mean p), AU = mean H(p), EU = TU - AU.

Person A, data and EDA
- Download PAD-UFES-20 images (Mendeley Data) to /vol/miltank/projects/practical_wise2627/<group>/data.
- Do the EDA in the `eda` repo; polish the figures for the slides.
- Implement the patient-grouped 5-fold split; check the per-fold table; commit the split to `data/splits/`.
- Tabular-only baseline numbers as the "how much information is in the metadata" slide.

Person B, infrastructure
- CIT account activation, VPN config with the two extra routes, ssh selene.
- Move .conda/.cache/.vscode-server to /meta/users/<user>/ and symlink.
- Set up the shared Python environment in the cloned repo; write and submit a short GPU test job.
- Create the W&B team/project; add `WANDB_API_KEY` handling to the cluster guide.
- GitHub: invite teammates, enable branch protection on main (PR + 1 review), create issue labels.
- Set up shs (VS Code tunnel into a slurm job) and document any pitfalls in the main repo's docs/cluster_guide.md.

Person C, literature and presentation
- Fill the literature database (Notion) from literature.md; find 6-10 additional papers.
- Write the one-page abstract and the method diagram.
- Draft the P1 deck from presentations/p1/outline.md.

Everyone, day 8-9
- Assemble the P1 deck, decide the presenter, rehearse twice with a timer.
- Send the deck to the supervisor the day before.

## Steady-state work split (after P1)

- Data and evaluation owner: dataset, splits, metadata encoding, metrics, statistics, ISIC 2019 preparation.
- Modelling owner: encoders, fusion, modality-dropout training, baseline tuning.
- UQ owner: decomposition, ensembles / MC dropout / Laplace, controls, uncertainty plots.
- Rotating weekly: presentation lead, meeting scribe.
- Problem analysis and design decisions are done together; implementation is never three people on one file.
- Ownership is enforced by `.github/CODEOWNERS`; cross-area changes need the owner's review.

## Weekly rhythm

- Monday: 30 min internal sync. Update Notion tasks board, pick this week's items.
- Supervisor meeting (slot agreed with Raphael): 3-slide status (worked / did not work / blocked). Scribe writes the log page during the meeting.
- Friday: each owner posts a short written update in the weekly log (what ran, what the numbers say, next step).
- Commit before every training run. Every run has a W&B link and a row in the Experiments database.

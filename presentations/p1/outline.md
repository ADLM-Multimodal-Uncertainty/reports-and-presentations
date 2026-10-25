# Presentation 1 outline (04.11.2026, 7 minutes + 2-4 minutes questions)

Grading expectation: "Introduction to your topic, first ideas". Ten slides maximum; one
sentence per bullet; every figure comes from the EDA output (first-pass figures in the `eda` repo, `figures/`).

## Slide 1: Title (15 s)

- Project title, team names, supervisor (Raphael), chair.
- One line each about who we are.

## Slide 2: The question in one sentence (45 s)

- "Does a second modality (clinical metadata) reduce a model's epistemic uncertainty about a skin lesion?"
- dEU = EU(image) - EU(image + metadata). > 0, ~0 or < 0?
- Why it is not obvious: information theory says H(Y | X_I, X_M) <= H(Y | X_I), but that is about the
  true conditional distribution, not about the epistemic uncertainty of a finite network trained on 2k images.

## Slide 3: What we want from this presentation (30 s)

- We are presenting: dataset analysis and a proposed experimental design.
- We are looking for: feedback on the design (especially the modality-dropout idea and the controls)
  and on which UQ method to use first.

## Slide 4: Aleatoric vs epistemic, in one figure (45 s)

- Total = aleatoric + epistemic; TU = H(mean p), AU = mean H(p), EU = mutual information.
- Epistemic is what more data could remove; aleatoric is in the data. Metadata could in principle lower both.
- Cite Hullermeier & Waegeman 2021, Depeweg et al. 2018.

## Slide 5: Dataset: structure (45 s)

- PAD-UFES-20: 2298 smartphone images, 1373 patients, 1641 lesions, 6 classes.
- Figure: images per lesion / lesions per patient (why the split must be grouped by patient).
- Figure: class distribution (BCC 845 ... MEL 52): strongly imbalanced, MEL estimates will be noisy.

## Slide 6: Dataset: metadata (45 s)

- 21 clinical attributes; 804 images have the entire clinical block missing; age and region never missing.
- Figure: missingness heatmap; missingness vs biopsied and vs class (is missingness itself a shortcut?).
- Figure: metadata informativeness (Cramer's V / MI per feature) and tabular-only baseline BACC under
  patient-grouped CV: how much signal does the second modality carry on its own?

## Slide 7: Related work and the gap (45 s)

- Hoarau et al. 2026: more modalities should lower aleatoric uncertainty; EU lowered by more data.
  Our question is the complementary one on EU, with paired per-sample comparison.
- DermaCalibra 2026: PAD-UFES-20 SOTA (84.2% BACC) with metadata fusion + MC dropout, but image-level
  split (leakage) and uncertainty used as a loss weight, not analysed.
- Pacheco & Krohling: canonical PAD-UFES-20 fusion baselines.

## Slide 8: Proposed design (60 s)

- Diagram: image encoder (pretrained ResNet50) + tabular MLP -> late fusion -> probabilities -> TU/AU/EU.
- Design 1: paired image-only vs fusion models, identical backbone/seeds/folds.
- Design 2: one model trained with modality dropout, evaluated with and without metadata (same weights).
- Design 3 controls: permuted metadata, metadata subsets, corrupted metadata.
- UQ method first: deep ensemble (5 seeds); later MC dropout and last-layer Laplace on the same checkpoints.
- Evaluation: 5-fold patient-grouped CV; BACC, macro AUC, ECE; dEU with patient-level bootstrap CI and
  a paired test; stratified by correctness and class.

## Slide 9: Timeline and open questions (30 s)

- P2 (16.12): baseline + fusion + ensemble, first dEU numbers. Code freeze 05.02, poster 10.02, report 24.02.
- Open questions for the audience / supervisor:
  1. Is the modality-dropout design a fair "same model" comparison, or does it change what the model learns?
  2. Which EU estimator would you trust most on 2k images?
  3. Should missing-metadata rows be dropped, imputed, or treated as a third condition?

## Slide 10: Backup (not presented)

- Per-fold class counts of the split; image resolution histogram; sample image grid per class;
  preprocessing details (resize 224, ImageNet normalisation, flips/rotations/colour jitter);
  metadata encoding (one-hot with explicit "unknown", standardised age/diameters, missingness indicators).

## Rehearsal checklist

- Time each slide; total under 6:30 to leave buffer.
- Every number on a slide traceable to a file in the EDA output.
- Presenter can answer: why patient-grouped split, why ensembles first, why not evidential DL.

# Literature

Working definitions used throughout (information-theoretic decomposition, Depeweg et al. 2018):

    TU(x) = H( E_theta[ p(y | x, theta) ] )        total uncertainty: entropy of the mean prediction
    AU(x) = E_theta[ H( p(y | x, theta) ) ]        aleatoric: mean entropy of the individual predictions
    EU(x) = TU(x) - AU(x)                          epistemic: mutual information I(y; theta | x) >= 0

`theta` ranges over ensemble members / MC-dropout masks / Laplace posterior samples.
Planned implementation location: `src/adlm_uq/uq/` in the main repo (`adlm-multimodal-uncertainty`).

## 1. Papers we already have (`resources/`)

| # | Paper | Year | Data / task | UQ method | Modalities | EU measure | Split | Key result | Use for us |
|---|-------|------|-------------|-----------|------------|------------|-------|------------|------------|
| 1 | Hullermeier & Waegeman, "Aleatoric and epistemic uncertainty in ML" (`s10994-021-05946-3.pdf`) | 2021 | survey | all families | n/a | entropy / MI, credal sets | n/a | Conceptual framework; EU estimates are model-relative | Definitions, background section, caveats |
| 2 | Hoarau, Quost, Destercke, Waegeman, "Reducing aleatoric and epistemic uncertainty through multi-modal data acquisition" (`s10994-026-07042-w.pdf`) | 2026 | toy, BIOSCAN-5, MIMIC-IV | Deep ensembles, Laplace, RF, distance-based; conformal (APS) | tabular, image, DNA, EHR | entropy decomposition and label-wise variance (App. A) | per dataset | Hypothesis: more modalities reduce AU; more data reduces EU. Property 1: H(y / x_1:j') <= H(y / x_1:j) | Closest work. Reuse their measures, method choice, Property 1 as motivation. Gap: no image + tabular medical case, no paired per-sample dEU |
| 3 | Kendall & Gal, "What uncertainties do we need in Bayesian DL for computer vision?" (`1703.04977v2.pdf`) | 2017 | segmentation, depth | MC dropout + heteroscedastic loss | image | predictive variance of MC samples | standard | AU and EU behave differently; EU shrinks with more data | AU vs EU framing; MC dropout recipe |
| 4 | Lakshminarayanan, Pritzel, Blundell, "Simple and scalable predictive uncertainty estimation using deep ensembles" (`NIPS-2017-...pdf`) | 2017 | classification, regression | Deep ensembles (M=5), random init, shuffled order | image, tabular | disagreement / entropy | standard | Ensembles beat MC dropout in calibration and OOD | Our first EU estimator; follow their recipe exactly (no bagging) |
| 5 | Wang et al., "DermaCalibra" (`diagnostics-16-00630.pdf`) | 2026 | PAD-UFES-20, 6 classes | MC dropout (T=10) inside loss weighting | image + 81-dim metadata | variance of softmax used as focal-loss weight | **image-level stratified** 66.7/16.7/16.7 | BACC 84.2%, macro AUC 96.9% | Metadata encoding recipe (21 attributes -> 81 dims, median imputation, "unknown" level), ResNet50 justification, SOTA reference numbers (with the caveat below) |
| 6 | BayesAI Consortium, "Handbook of Bayesian Deep Learning" (`handbook_of_bdl.pdf`) | 2025 | reference | sampling, Laplace, VI, ... | n/a | n/a | n/a | n/a | Part II (Laplace) when implementing last-layer Laplace; do not read cover to cover |

### How each paper feeds the project

1. Hullermeier & Waegeman: sections 1-3 for vocabulary, section 5 (ensembles, Bayesian) for the estimators.
   Key sentence for the report: epistemic uncertainty is a property of the learner, not of the data; so dEU
   is a statement about *our* models, and we must show it is stable across estimators and seeds.
2. Hoarau et al.: Appendix A gives the formulas we implement; section 3.5 explains why they exclude
   evidential deep learning (Bengs et al. 2023 criticism); Property 1 is the information-theoretic
   motivation on slide 2. Their Table 1 (some modalities do not help some diseases) is the template for our
   "informative vs weak metadata" analysis.
3. Kendall & Gal: use Figure 1 style side-by-side AU/EU plots; MC dropout implementation (dropout active at
   test time, T passes).
4. Lakshminarayanan et al.: M=5 ensemble members, random init + shuffled data order, no bagging. Also their
   adversarial-training augmentation is optional and we skip it.
5. DermaCalibra: adopt the metadata encoding; cite the BACC as an upper reference. Critique (state it in P1
   and in the report): the split is stratified at image level, so images of the same lesion / patient can be
   in train and test (1641 lesions for 2298 images, 1373 patients). Their numbers are therefore optimistic for
   our patient-grouped setting. Their uncertainty is a training signal, not an analysed quantity, so they do
   not answer our question.
6. Handbook: chapters 7-8 (Laplace, linearised Laplace, last-layer) when wiring `laplace-torch`.

## 2. Papers to add (search order: Google Scholar, Connected Papers from #2 and #5, PubMed)

PAD-UFES-20 and multimodal skin lesion baselines
- Pacheco et al. 2020, "PAD-UFES-20: a skin lesion dataset composed of patient data and clinical images
  collected from smartphones", Data in Brief. Dataset paper; cite for collection protocol and biopsy rate.
- Pacheco & Krohling 2020, "The impact of patient clinical information on automated skin cancer detection",
  Computers in Biology and Medicine. First image + metadata fusion study on this data.
- Pacheco & Krohling 2021, "An attention-based mechanism to combine images and metadata in deep learning
  models applied to skin cancer classification" (MetaBlock), IEEE JBHI. Canonical fusion baseline.

Uncertainty decomposition and its limits
- Depeweg et al. 2018, "Decomposition of uncertainty in Bayesian deep learning for efficient and
  risk-sensitive learning", ICML. The TU/AU/EU decomposition.
- Wimmer et al. 2023, "Quantifying aleatoric and epistemic uncertainty in machine learning: are conditional
  entropy and mutual information appropriate measures?", UAI. Why the decomposition can mislead.
- Mucsanyi, Kirchhof, Oh 2024, "Benchmarking uncertainty disentanglement: specialized uncertainties for
  specialized tasks", NeurIPS. Shows disentanglement is largely unsolved; essential for our discussion.
- Schweighofer et al. 2023, "Introducing an improved information-theoretic measure of predictive
  uncertainty". Alternative EU measure to report as a robustness check.
- Bengs, Hullermeier, Waegeman 2023, "On second-order scoring rules for epistemic uncertainty
  quantification", ICML. Why we avoid evidential DL as primary method.

Estimators
- Gal & Ghahramani 2016, "Dropout as a Bayesian approximation", ICML.
- Daxberger et al. 2021, "Laplace Redux: effortless Bayesian deep learning", NeurIPS (laplace-torch).
- Mukhoti et al. 2023, "Deep deterministic uncertainty: a new simple baseline", CVPR (feature-density EU).
- Ovadia et al. 2019, "Can you trust your model's uncertainty?", NeurIPS (benchmark under shift).
- Guo et al. 2017, "On calibration of modern neural networks", ICML (temperature scaling, ECE).

Multimodal uncertainty
- Han et al. 2021, "Trusted multi-view classification", ICLR (evidential fusion; compare, do not adopt).
- Zhang et al. 2023, "Provable dynamic fusion for low-quality multimodal data" (QMF), ICML.
- Jung et al. 2022, "Uncertainty estimation for multi-view data: the power of seeing the whole picture",
  NeurIPS.
- Kossen et al. 2023, "Active acquisition for multimodal temporal data", for the missing-modality angle.

Statistics for clustered medical data
- Patient-level (cluster) bootstrap for CIs; Wilcoxon signed-rank for paired dEU; mixed-effects model
  with patient random intercept for the report (statsmodels MixedLM).

## 3. Notion literature database (columns)

paper | year | venue | task / data | UQ method | modalities | EU measure | split strategy | key result |
relevance to us (1-5) | who read it | status (to read / skimmed / read / summarised) | notes link

Rule: at most three papers are read in full by any one person; the rest are skimmed (abstract, figures,
method, results). Summaries go in the notes page, one paragraph plus "what we take from it".

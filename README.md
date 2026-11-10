BDLitchi — Forensic Validity Audit

Code accompanying a forensic, validity-first audit of BDLitchi, a publicly released litchi (Litchi chinensis) leaf-disease image dataset (11,094 images, 11 classes, 26 capture sessions; Mendeley Data, DOI 10.17632/jhb24mszdk.1, CC BY 4.0). The audit does not propose a new model or dataset — it checks whether a benchmark's reported accuracy reflects genuine biological signal or an artefact of how the data was collected and split: near-duplicate leakage across train/val/test, split discipline, statistical power, calibration, corruption robustness, a colour-only baseline, and zero-shot external transfer.

External validation uses a second, independently collected dataset: the Lychee Leaf Disease Dataset (Nirob et al.), also public on Mendeley Data.

Repository structure

Each notebook is self-contained and was run on Kaggle; outputs from an earlier notebook are re-attached as a Kaggle Dataset input to the next one. Run in this order:

Notebook	What it does
nb01-identity-disjoint-splits.ipynb	Builds the specimen unit (pHash near-duplicate clustering + bounded 30s temporal blocks), then the naive (image-level) and specimen-disjoint splits plus the common held-out test set. Produces manifest_full.csv, per-class diagnostics, effective-sample-size table, and the block-span/session-gap sensitivity sweeps.
nb02-corrected-leakage-ablation.ipynb	The leakage ablation: trains ResNet-50 and ViT-Base/16, 3 seeds each, once per split protocol, with early stopping disabled so both arms get an identical epoch budget. Separates reporting inflation (what each protocol would publish) from the true generalisation gap (both models scored on the same common test set).
nb03-independent-training-runs.ipynb / nb04-independent-training-runs.ipynb	Independent training runs (3 seeds each) for the five-backbone benchmark (MobileNetV3-Large, EfficientNet-B0/B3, ResNet-50, ViT-Base/16) on the specimen-disjoint split.
nb05-colour-shortcut.ipynb	Colour-only (HSV-histogram k-NN) baseline and grayscale-retraining ablation; prepares the external validation cohort.
nb06-aggregate.ipynb	Aggregates every notebook above into the final numbers used in the manuscript: corrected ablation effect sizes, Friedman/Wilcoxon statistics, per-class effective-sample-size table, colour-reliance table, specimen-level bootstrap intervals.
nb07-regenerating.ipynb	Regenerates the figures/tables used in the manuscript from the aggregated results.
nb08-cosine-similarity-check.ipynb	A supervisor-suggested cross-check of specimen identity via generic ResNet-50 embedding cosine similarity. Reported as an honest negative finding in the paper: a non-fine-tuned embedding cannot resolve individual-leaf identity in this dataset, so it is not used as a leakage metric.
nb09-synthetic-dedup-benchmark.ipynb	A 100-image synthetic benchmark with duplicate identity known by construction, used to validate the specimen method against exact-hash, pHash-only, and cosine-similarity alternatives.
nb10-hamming-sensitivity.ipynb	Sensitivity of the specimen count to the pHash Hamming-distance threshold (0–32).
nb11-external-validity.ipynb	Zero-shot transfer of the trained checkpoints to the independent Nirob et al. lychee dataset; open-set (full label space) and closed-set (shared-classes-only) accuracy.
Revision_Notebooks/NB_hamming_sensitivity/ (added after running on Kaggle)

Two companion notebooks that check whether the leakage-ablation result itself (not just the specimen count, which nb10 already covers) is sensitive to the pHash Hamming threshold chosen in nb01/nb02 (threshold = 5):

nb01b-hamming-threshold-sweep.ipynb — rebuilds specimens/splits at two extra thresholds (4 and 6) bracketing the paper's chosen value, reusing nb01's EXIF-scan and pHash-code cells unchanged.
nb02b-hamming-threshold-ablation.ipynb — reruns the leakage ablation from nb02 across thresholds 4/5/6 (training code copied verbatim), producing hamming_ablation_sensitivity.csv: reporting inflation and true gap per (architecture, threshold), directly comparable to the main-text ablation table.

Run nb01b first, upload its output as a Kaggle dataset, then run nb02b against it.

Data and code availability

BDLitchi itself is not redistributed here — see the Mendeley DOI above. All analysis and training code for every result in the manuscript is in this repository.

Citation

A citation entry will be added here once the manuscript is accepted for publication.

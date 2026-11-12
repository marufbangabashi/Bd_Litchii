# BDLitchi — Forensic Validity Audit

Code accompanying a forensic, validity-first audit of **BDLitchi**, a publicly released
litchi (*Litchi chinensis*) leaf-disease image dataset (11,094 images, 11 classes, 26
capture sessions; Mendeley Data, DOI [10.17632/jhb24mszdk.1](https://doi.org/10.17632/jhb24mszdk.1),
CC BY 4.0). The audit does not propose a new model or dataset — it checks whether a
benchmark's reported accuracy reflects genuine biological signal or an artefact of how
the data was collected and split: near-duplicate leakage across train/val/test, split
discipline, statistical power, calibration, corruption robustness, a colour-only
baseline, and zero-shot external transfer.

External validation uses a second, independently collected dataset: the Lychee Leaf
Disease Dataset (Nirob et al.), also public on Mendeley Data.

## Repository structure

Every notebook below is self-contained and was run on Kaggle, with each one's saved
cell output kept in the `.ipynb` file itself — the numbers, tables and figures you see on
GitHub are the actual run that produced the corresponding result in the manuscript, not
a re-executed copy. Outputs from an earlier notebook are re-attached as a Kaggle Dataset
input to the next one; there is no folder structure here, every file sits at the repo
root. Run in this order:

**A note on filenames:** a few notebooks were exported from Kaggle under the kernel's
own auto-generated slug rather than the name used while developing them, so the
filename and the notebook's own title (its first markdown cell) don't always match.
Where that happens, the table below says so explicitly — the notebook's *content* is
what the description refers to, not the filename.

| Notebook (as named in this repo) | What it does |
|---|---|
| `nb01-identity-disjoint-splits.ipynb` | **NB01.** Builds the **specimen** unit (pHash near-duplicate clustering + bounded 30 s temporal blocks), then the naive (image-level) and specimen-disjoint splits plus the common held-out test set. Produces `manifest_full.csv`, per-class diagnostics, the effective-sample-size table, and the block-span/session-gap sensitivity sweeps. Threshold = 5 throughout, fixed by the 20-specimen-per-class floor (Table 4 in the manuscript). |
| `nb01a-identity-disjoint-specimen.ipynb` | **NB01b**, despite the filename — its own second cell is titled "NB01b — Hamming-threshold sensitivity of the specimen construction". Rebuilds the specimen set and splits at pHash Hamming threshold 3, reusing NB01's EXIF-scan and pHash cells unchanged. Thresholds 4 and 6 were already resolved in earlier iterations of this same sweep (4 succeeded, feeding `nb02b` below; 6 is infeasible — four classes fall under the 20-specimen floor, matching NB01's own sensitivity table), so this file's saved run covers only the one threshold still needed. |
| `nb02-corrected-leakage-ablation.ipynb` | **NB02.** The main leakage ablation: trains ResNet-50 and ViT-Base/16, 3 seeds each, once per split protocol (naive vs. specimen-disjoint), with early stopping disabled so both arms get an identical epoch budget. Separates *reporting inflation* (what each protocol would publish from its own test partition) from the *true generalisation gap* (both models scored on the same common test set) — manuscript Table 11. |
| `nb02b-leakage-ablation.ipynb` | **NB02b** — companion to `nb01a`/NB01b above. Reruns the leakage ablation across pHash Hamming thresholds 3, 4 and 5 (the paper's own value), reusing NB02's training/evaluation code verbatim. Checks whether Table 11's headroom-scaling result depends on the specific threshold chosen. Produces `ablation_runs_all_thresholds.csv` and `hamming_ablation_sensitivity.csv` — manuscript Table 12. |
| `nb03-independent-training-runs.ipynb` / `nb04-independent-training-runs.ipynb` | **NB03 / NB04.** Independent training runs (3 seeds each) for the five-backbone benchmark (MobileNetV3-Large, EfficientNet-B0/B3, ResNet-50, ViT-Base/16) on the specimen-disjoint split. |
| `nb05-colour-shortcut.ipynb` | **NB05.** Colour-only (HSV-histogram *k*-NN) baseline and grayscale-retraining ablation; prepares the external validation cohort. |
| `nb06-aggregate.ipynb` | **NB06.** Aggregates every notebook above into the final numbers used in the manuscript: leakage-ablation effect sizes, Friedman/Wilcoxon statistics, the per-class effective-sample-size table, the colour-reliance table, and specimen-level bootstrap intervals. |
| `nb07-regenerating.ipynb` | **NB07.** Regenerates the figures and tables used in the manuscript from the aggregated results. |
| `nb-08-cosine-similarity-check.ipynb` | **NB08** (filename has an extra hyphen from the Kaggle export). A supervisor-suggested cross-check of specimen identity via generic ResNet-50 embedding cosine similarity. Reported as an honest **negative** finding in the paper: a non-fine-tuned embedding cannot resolve individual-leaf identity in this dataset, so it is not used as a leakage metric. |
| `nb09-ground-truth.ipynb` | **NB09**, despite the filename — this is the synthetic deduplication benchmark (its own title cell: "Ground-Truth Synthetic Deduplication Benchmark"). A 100-image benchmark with duplicate identity known by construction, used to validate the specimen method against exact-hash, pHash-only, and cosine-similarity alternatives — manuscript Table 13. |
| `nb10-hamming-distance.ipynb` | **NB10**, despite the filename — this is the specimen-count Hamming-sensitivity sweep (its own title cell: "Hamming-Distance Sensitivity for Specimen Construction"). Sensitivity of the *specimen count* to the pHash Hamming-distance threshold (0–32) — manuscript Table 4. Not to be confused with `nb01a`/NB02b above, which check sensitivity of the leakage-ablation *result*, not just the specimen count. |
| `nb11-external-validity.ipynb` | **NB11.** Zero-shot transfer of the trained checkpoints to the independent Nirob et al. lychee dataset; open-set (full label space) and closed-set (shared-classes-only) accuracy. |

## Data and code availability

BDLitchi itself is not redistributed here — see the Mendeley DOI above. All analysis and
training code for every result reported in the manuscript is in this repository, with
each notebook retaining the saved output (per-run metrics, tables and figures) from the
run that produced the numbers reported in the paper.

## Citation

A citation entry will be added here once the manuscript is accepted for publication.

# Explanation audit for deepfake detectors (N10)

Code, per-image scores and analysis outputs for the manuscript
*Do explanations reveal when deepfake detectors fail? A pre-specified audit of attribution agreement and counter-class evidence under generator shift*
(Sugiantoro and Hafizuddin, Department of Informatics, UIN Sunan Kalijaga Yogyakarta).

## Contents
| File | What it holds |
|---|---|
| `HYPOTHESES_LOCKED_TEXT_N10.txt` | The locked text of hypotheses H1-H4 (Indonesian, as written before any run). `sha256sum HYPOTHESES_LOCKED_TEXT_N10.txt` returns `b7ecfb941ef4a65038b87f09fd35d2d99b2e0ced5507394975fd5238847c0f57`, the hash recorded on 2026-11-18 in `HYPOTHESES_LOCK_N10.txt` |
| `code_manuscript_N10.zip` | `code/` (fine-tuning, audit, analysis, figure scripts, Slurm jobs), `manuscript/` (LaTeX source on the Springer Nature template, figures, tables) and `docs/` (work log in Indonesian, reference verification table) |
| `results_N10.zip` | Analysis outputs used in the paper: `an_<model>_face_{cond,extra,results}.csv` and `group_medians.csv` (exploratory family grouping) |
| `perimage_<model>_face_orig.csv.gz` | Per-image scores, 79,130 rows per detector: key, generator, family, role, label, p_fake, pred, wrong, msp, margin, agreement and mass signals |

## Protocol (summary)
Sample fixed by seed 20261118 and the manifest only (up to 1,500 fakes per generator and 2,000 reals per role): 79,130 images, 56 generators, original condition, 224x224.
Detectors: ResNet-50, ConvNeXt-T, ViT-B/16, fine-tuned 6 epochs on source generators (AdamW, lr 3e-4, one-cycle). Explainers: Grad-CAM, Integrated Gradients (32 steps), SmoothGrad (16 samples, sigma 0.1); agreement is the mean pairwise top-20% IoU.
The study differs from the locked plan in two ways, both reported in the paper: three detectors (not four) and the original-image condition only. Error detection is evaluated among released decisions, separately for decisions released as real and as fake.

## Reproducing
1. Obtain DF40 and DiFF from their providers; build the manifest as described in `docs/README_N10_worklog_id.md`.
2. `sbatch 21_ft_n10.sbatch ...` then `sbatch 20_xai.sbatch <timm_name> <model_name>` (scripts in `code_manuscript_N10.zip`).
3. `python code/analyze_cond.py ...` and `python code/analyze_extra.py ...`, then `python code/make_assets.py` (edit the paths at the top).
The scripts resume interrupted runs from the last completed image key.

## Data and licences
The face images are **not** included: DF40 and DiFF licences do not permit redistribution (DiFF is available from its authors on request). Per-image scores contain image identifiers and model outputs only. Code is MIT-licensed.

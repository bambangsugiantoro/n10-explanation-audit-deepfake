# Explanation audit for deepfake detectors (N10)

Code, per-image scores and analysis outputs for the manuscript
*Do explanations reveal when deepfake detectors fail? A pre-specified audit of attribution agreement and counter-class evidence under generator shift*
(Sugiantoro and Hafizuddin, Department of Informatics, UIN Sunan Kalijaga Yogyakarta).

## Contents
| Path | What it holds |
|---|---|
| `HYPOTHESES_LOCK_N10.txt` | Pre-specified hypotheses H1-H4 and their SHA-256 hash (`b7ecfb94...0f57`), written before any detector was run |
| `code/finetune_tier.py` | Fine-tuning of ResNet-50, ConvNeXt-T, ViT-B/16 on source generators |
| `code/xai_audit.py` | Label-blind sampling and per-image Grad-CAM, Integrated Gradients, SmoothGrad for the predicted and unchosen class |
| `code/analyze_cond.py`, `code/analyze_extra.py` | Released-decision AUROC with bootstrap CIs; permutation, MSP-matched and Holm analyses |
| `code/make_assets.py` | Figures and LaTeX tables from `results/` |
| `code/*.sbatch` | Slurm jobs (2x L40S node, Apptainer container) |
| `results/an_<model>_face_{cond,extra,results}.csv` | Analysis outputs used in the paper; `group_medians.csv` is the exploratory family grouping |
| `results/perimage/` | Per-image scores (79,130 rows per detector): key, generator, family, role, label, p_fake, pred, wrong, msp, agreement and mass signals |
| `manuscript/` | LaTeX source (Springer Nature template), figures and tables |
| `docs/` | Work log (Indonesian) and reference verification table |

## Protocol (summary)
Sample fixed by seed 20261118 and the manifest only (up to 1,500 fakes per generator and 2,000 reals per role): 79,130 images, 56 generators, original condition, 224x224.
Detectors: ResNet-50, ConvNeXt-T, ViT-B/16, fine-tuned 6 epochs on source generators (AdamW, lr 3e-4, one-cycle). Explainers: Grad-CAM, Integrated Gradients (32 steps), SmoothGrad (16 samples, sigma 0.1); agreement is the mean pairwise top-20% IoU.
Error detection is evaluated among released decisions, separately for decisions released as real and as fake.

## Reproducing
1. Obtain DF40 and DiFF from their providers; build the manifest as described in `docs/README_N10_worklog_id.md`.
2. `sbatch 21_ft_n10.sbatch ...` then `sbatch 20_xai.sbatch <timm_name> <model_name>`.
3. `python code/analyze_cond.py ...` and `python code/analyze_extra.py ...`, then `python code/make_assets.py` (edit the paths at the top).
The scripts resume interrupted runs from the last completed image key.

## Data and licences
The face images are **not** included: DF40 and DiFF licences do not permit redistribution (DiFF is available from its authors on request). Per-image scores contain image identifiers and model outputs only. Code is MIT-licensed.

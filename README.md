# Channel Independence as a Failure Mode in Foundation Models for Multivariate Time Series Classification

MSc thesis, UCD School of Computer Science, 2026.
**Aisling Wallace** · Supervisor: Dr Georgiana Ifrim

---

## What this is about

Foundation models for multivariate time series usually encode each channel of a
recording in isolation and combine the per-channel representations only after
feature extraction is complete. That design choice is called **channel
independence**, and it discards the relationships between channels at the point
they might matter most.

This thesis asks not whether that is a simplification, but *when it begins to
cost something*. The answer turns out to depend on the data: the cost is
substantial where a task's discriminative signal lives between channels, and
absent where it lives inside them.

The question is tested on five benchmark datasets and on cross-patient seizure
detection from clinical EEG, where the diagnostic evidence lies in the temporal
coordination between electrode traces rather than in any single one of them.

## Findings

- **Measured channel dependency varies from under one accuracy point to nearly
  thirty** across the six datasets, and the foundation models' deficit against a
  from-scratch baseline concentrates where that dependency is high.
- **A 243-feature classical baseline** of per-channel band powers and pairwise
  channel correlations, with no pretraining at all, detects more seizure events
  at the strictest false-alarm threshold than either frozen time-series encoder.
- **Channel awareness cannot be retrofitted cheaply.** Supplying the missing
  cross-channel features to a frozen model raises window accuracy whilst
  degrading event-level detection.
- **A single unreported setting can outweigh the architecture.** Tuning the
  regularisation of the linear probe moves one model by nearly ten points and
  closes most of the apparent architectural gap, so some published evidence
  about architectures may be evidence about probe configuration instead.

## Data

**No data is committed to this repository.**

- **CHB-MIT** is available from PhysioNet:
  https://physionet.org/content/chbmit/1.0.0/
  Run `01_preprocessing/chbmit_preprocessing_v2.ipynb` against the raw EDF files
  to regenerate the windowed arrays used throughout. The preprocessing is
  deterministic under the seed set in that notebook.
- **CMJ, MP8, MP50, Rowing and SYNTH** were obtained in the form used by
  Sungu Isiacik et al. (ECML PKDD 2025) and are used under the terms of their
  release.

Derived artefacts — preprocessed arrays and extracted embeddings — are retained
and can be made available on request.

## Layout

| Folder | Contents |
|---|---|
| `notebooks/01_preprocessing` | EDF to windowed arrays: montage reduction, windowing, labelling, class balance |
| `notebooks/02_embeddings` | Frozen-encoder extraction for each model |
| `notebooks/03_channel_dependency` | The channel-dependency gap and the feature-budget control |
| `notebooks/04_experiments` | Ablations (RQ1), event scoring (RQ3), retrofit (RQ4), channel-leakage test |
| `notebooks/05_figures` | Figure generation |
| `results/` | Consolidated results workbook, one sheet per experiment |

Notebooks run in the order the folder numbers suggest. Later stages read arrays
written by earlier ones.

## Models

| Model | Checkpoint | Loaded through |
|---|---|---|
| Chronos-2 | `amazon/chronos-2` | `chronos-forecasting` |
| MantisV2 | `paris-noah/MantisV2` | `mantis-tsfm` |
| MOMENT | `AutonLab/MOMENT-1-large` | `momentfm` |
| SigLIP2 | `google/siglip2-base-patch16-224` | `transformers` |
| DINOv2 | `dinov2_vitb14` | `torch.hub`, `facebookresearch/dinov2` |
| CLIP | ViT-B-32 | `open_clip` |
| LLM-ABBA | `gpt2` + fABBA | `transformers`, `fABBA` |

ROCKET, MiniRocket and MultiRocket come from `sktime` and serve as the
from-scratch baselines.

## Reproducibility

Experiments were run in Google Colab and on Kaggle under Python 3.12, on T4 GPUs
where GPU acceleration was required. Package versions were not pinned at install
time; `requirements.txt` lists the packages without versions.

Seeds are fixed in the preprocessing pipeline and in the ablation shifts. The
randomised-kernel transforms were not seeded, which matters least for MiniRocket
and most for ROCKET, whose kernel lengths and dilations are drawn at random. The
frozen encoders are deterministic, verified directly rather than assumed.

Notebook paths point at Google Drive (`/content/drive/MyDrive/...`) and will need
adjusting to run elsewhere.

## Licence

Code released under the MIT Licence. CHB-MIT is distributed by PhysioNet under
its own terms and is not redistributed here.

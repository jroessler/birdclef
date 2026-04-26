# BirdCLEF+ 2026 — Competition Reference

**Kaggle:** https://www.kaggle.com/competitions/birdclef-2026  
**Prize pool:** $50,000 (main) + $5,000 (working notes)  
**Status:** Active

---

## Overview

The goal is to build machine learning models that automatically identify wildlife species from passive acoustic monitoring (PAM) recordings collected in the **Pantanal** — the world's largest tropical wetland, spanning ~150,000 km² across Brazil, Bolivia, and Paraguay. A network of ~1,000 acoustic recorders deployed across the region generates continuous sound data. Conventional biodiversity surveys in this vast, remote ecosystem are expensive and logistically demanding, making automated acoustic identification a key tool for conservation.

The Pantanal faces severe pressure from seasonal flooding, wildfires, agricultural expansion, and climate change. The dataset was funded by the **Bezos Earth Fund AI for Climate and Nature Grand Challenge**.

**Hosts:** Cornell Lab of Ornithology · TU Chemnitz · Google DeepMind  
**Partners:** iNaturalist · K. Lisa Yang Center for Conservation Bioacoustics · multiple Brazilian universities

---

## Task

Given a soundscape recording, produce a probability score for each of **234 species** for every **5-second window** of the recording.

- **Not just birds** — the target species span 5 taxonomic classes:

  | Class     | Species |
  |-----------|---------|
  | Aves      | 162     |
  | Amphibia  | 35      |
  | Insecta   | 28      |
  | Mammalia  | 8       |
  | Reptilia  | 1       |
  | **Total** | **234** |

- **Multi-label:** multiple species can be active in the same 5-second window.
- **Recording site:** Pantanal, Mato Grosso do Sul, Brazil  
  Lat: −16.5 to −21.6 · Lon: −55.9 to −57.6

---

## Evaluation Metric — Padded cmAP

**Padded Class-Averaged Mean Average Precision (Padded cmAP)**

Before scoring, five rows of true positives are added to each submission and ground truth. This padding:
- Allows accepting predictions for species with zero ground-truth labels in the test set
- Reduces the impact of species with very few positive labels (limits dynamic range of per-species MAP)
- Reduces sensitivity to label noise for rare species

The final score is the mean of per-class average precision scores across all 234 classes.

---

## Timeline

| Date | Milestone |
|------|-----------|
| March 11, 2026 | Competition opens |
| May 27, 2026 | Entry deadline & team merger deadline |
| **June 3, 2026** | **Final submission deadline** |
| June 17, 2026 | Working note submission deadline |
| June 24, 2026 | Notification of acceptance |
| July 6, 2026 | Camera-ready submission deadline |

All deadlines at 11:59 PM UTC.

---

## Prizes

| Award | Amount |
|-------|--------|
| Main competition (top teams) | $50,000 total |
| Best working note #1 | $2,500 |
| Best working note #2 | $2,500 |

---

## Data

### Files

| File / Folder | Description |
|---------------|-------------|
| `train.csv` | 35,549 rows — metadata for individual training clips |
| `taxonomy.csv` | 234 species with scientific name, common name, iNat taxon ID, class |
| `train_audio/` | 206 species folders, individual short `.ogg` clips (Xeno-canto + iNaturalist) |
| `train_soundscapes/` | 10,658 long `.ogg` soundscape recordings (60s each, 32 kHz, mono) |
| `train_soundscapes_labels.csv` | 1,478 rows — 5-second window labels for a subset of train soundscapes |
| `test_soundscapes/` | Hidden test soundscapes (only 1 visible in public test) |
| `sample_submission.csv` | 234-column probability output, one row per 5-sec window |
| `recording_location.txt` | Coordinates of recorder deployment sites |

### `train.csv` columns

`primary_label`, `secondary_labels`, `type`, `latitude`, `longitude`, `scientific_name`, `common_name`, `class_name`, `inat_taxon_id`, `author`, `license`, `rating`, `url`, `filename`, `collection`

### Training clip sources

| Source | Clips |
|--------|-------|
| Xeno-canto (XC) | 23,043 |
| iNaturalist (iNat) | 12,506 |
| **Total** | **35,549** |

### Soundscape label format

`train_soundscapes_labels.csv` has columns: `filename`, `start` (HH:MM:SS), `end` (HH:MM:SS), `primary_label`  
`primary_label` is a semicolon-separated list of iNat taxon IDs active in that window, e.g. `22961;23158;24321`

### Submission format

`row_id` = `<soundscape_filename_stem>_<end_second>`, e.g. `BC2026_Test_0001_S05_20250227_010002_5`  
Remaining 234 columns = probability for each species (iNat taxon ID or ebird code)

---

## Key Modelling Considerations

1. **Distribution shift** — individual training clips vs. continuous soundscape recordings differ substantially (background noise, overlapping species, recording hardware)
2. **Class imbalance** — some species have <10 training clips; filter by `rating >= 3.0` helps reduce noise
3. **Multi-label** — up to ~10 species can be active in a single 5-second window
4. **Non-bird species** — models trained only on bird audio (e.g. fine-tuned BirdNET) need adaptation for amphibians, insects, and mammals
5. **Evaluation padding** — rare species are de-risked by cmAP padding, so per-class calibration matters less than overall ranking quality

---

## Previous Years (for transfer learning context)

| Year | Location | Task |
|------|----------|------|
| 2026 | Pantanal, Brazil | Multi-taxa, 234 species |
| 2025 | Middle Magdalena Valley, Colombia | Birds only |
| 2024 | Migratory birds, Africa | Birds only |
| 2023 | Eastern Africa | Birds only |

---

## Top Public Notebooks (as of April 2026)

| Title | Votes | Link |
|-------|-------|------|
| pantanal-distill-birdclef2026-ONNX | 664 | https://www.kaggle.com/code/dingjiarun/pantanal-distill-birdclef2026-onnx |
| perch_v2 starter: train + infer | 255 | https://www.kaggle.com/code/jaejohn/perch-v2-starter-train-infer |
| Two-Pass SSM + Advanced PP | 243 | https://www.kaggle.com/code/marynaborovska/birdclef-26-two-pass-ssm-advanced-pp |
| PyTorch Baseline [Inference] | 161 | https://www.kaggle.com/code/antoinemasq/birdclef-2026-pytorch-baseline-inference |
| PyTorch Baseline [Training] | 133 | https://www.kaggle.com/code/antoinemasq/birdclef-2026-pytorch-baseline-training |

Local copies (source only) in `discussion/`.

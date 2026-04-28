# BirdCLEF+ 2026 — Competition Reference

**Kaggle:** https://www.kaggle.com/competitions/birdclef-2026  

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

## Top Public Notebooks (as of April 2026)

| Title | Votes | Link |
|-------|-------|------|
| pantanal-distill-birdclef2026-ONNX | 664 | https://www.kaggle.com/code/dingjiarun/pantanal-distill-birdclef2026-onnx |
| 0-932-test-mod-version-4 | 114 | https://www.kaggle.com/code/mtoshidesu/0-932-test-mod-version-4 |
| birdclef2026-perch-v75 | 126 | https://www.kaggle.com/code/mlnjsh/birdclef2026-perch-v75 |
| birdclef-2026-perch-v2-protossm-0-925 | 21 | https://www.kaggle.com/code/imaadmahmood/birdclef-2026-perch-v2-protossm-0-925 |
| pantanal-distill-birdclef2026-improvement | 385 | https://www.kaggle.com/code/yusufmurtaza01/pantanal-distill-birdclef2026-improvement |

Local copies (source only) in `discussion/`.

---

## Setup

```bash
export KAGGLE_API_TOKEN=XX
```

### Install venv (with python 3.12.13)
```bash
python -m .venv
source .venv/bin/activate
pip install -r requirements.txt
```

### Data Download

```bash
kaggle competitions download -c birdclef-2026 -p data
cd data && unzip birdclef-2026.zip -q
```

### Model

```bash
kaggle models instances versions download google/bird-vocalization-classifier/tensorFlow2/perch_v2_cpu/1 -p data/models/perch_tf
tar -xzf data/models/perch_tf/bird-vocalization-classifier.tar.gz -C data/models/perch_tf
```

Download ONNX model:

```bash
mkdir data/models/perch/
wget "https://huggingface.co/justinchuby/Perch-onnx/resolve/main/perch_v2_no_dft.onnx?download=true" -O data/models/perch/perch_v2_no_dft.onnx
```

Download notebooks listed above manually from Kaggle and place source copies in `discussion/`.

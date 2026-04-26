# BirdCLEF 2026 — Experiment Log

| # | Notebook | Description | OOF Macro AUC | OOF Padded cmAP | Published Score | Notes |
|---|----------|-------------|---------------|-----------------|-----------------|-------|
| 01 | exp01_perch_baseline | Perch v2 frozen embeddings + per-class LR probes (GroupKFold on 66 labeled soundscape files) | 0.6177 | 0.7810 | n/a | Labels had duplicates (2x); AUC inflated. Raw Perch: AUC 0.487. 203/234 species mapped. |
| 02 | exp02_temporal_context | Dedup labels + add prev/next/mean/max window scores as temporal features | 0.8891 | 0.8946 | n/a | Dedup alone (no temporal): AUC 0.896, cmAP 0.897. Temporal features gave -0.007 AUC — marginal hurt on small set. |
| 03 | exp03_mel_cnn | 4-layer MelCNN trained on 30% focal clips (10k clips, 5 epochs), ensembled with Perch | 0.8848 | 0.8953 | n/a | CNN alone: AUC 0.487, cmAP 0.807 — severe domain shift focal→soundscape. Ensemble with Perch×0.7 slightly hurts vs Perch alone. |
| 04 | exp04_probe_tuning | Tune Perch LR-probe regularization, optional class weighting, and raw/probe blend | 0.8934 | 0.8993 | n/a | Best: C=0.05, class_weight=balanced, probe blend=0.25. Improves cmAP vs dedup Perch baseline (+0.0019) but lowers AUC. |
| 05 | exp05_priors_proxies | Add genus-level Perch proxies for unmapped species and fold-safe site/hour priors | 0.9026 | 0.9050 | n/a | Best: max genus proxy + tuned exp04 probe + 10% fold-safe site/hour prior blend. Improves over exp04 by +0.0092 AUC and +0.0056 cmAP. |
| 05-submit | exp05_submit | Full-train exp05 inference pipeline with submission on kaggle | n/a | n/a | 0.899 | Imported notebook to Kaggle and submitted. |
| 06 | exp06_sequence_head | Small GRU sequence head over cached Perch embeddings + max-proxy scores | 0.6043 | 0.8595 | n/a | Not competitive. Best raw-Perch/GRU blend: AUC 0.8030, cmAP 0.8875. Keep exp05 as current candidate. |
| 07 | exp07_mlp_probe | Multi-task MLP (1536→512→256→234) on Perch embeddings; BCELoss+pos_weight; early stopping | 0.9238 | 0.9112 | n/a | Best: 40% MLP + 60% raw Perch, prior blend 0.05. +0.0212 AUC, +0.0062 cmAP vs exp05. Strong improvement from shared non-linear representation. |
| 08 | exp08_mlp_concat_mixup | MLP with concatenated Perch scores (1770-dim input) + mixup augmentation (alpha=0.4) | 0.9086 | 0.9035 | n/a | Regressed vs exp07. Concat input + mixup hurt — likely too much noise on 591-sample folds. |
| 09 | exp09_three_way_blend | 3-way blend: MLP OOF + LR probe OOF + raw Perch; grid search over simplex | 0.9176 | 0.9144 | n/a | Best: 10% MLP + 60% tuned_LR + 30% raw Perch + 5% prior. cmAP +0.0032 vs exp07. LR component dominates. |
| 10 | exp10_mlp_ensemble_blend | 3-seed MLP ensemble (seeds 42/123/456) in 3-way blend, finer grid | 0.9119 | 0.9099 | n/a | Regressed vs exp09. Averaging 3 seeds hurt — fold variance is data-driven, not seed-driven. Ensemble smoothed out useful per-fold signal. |

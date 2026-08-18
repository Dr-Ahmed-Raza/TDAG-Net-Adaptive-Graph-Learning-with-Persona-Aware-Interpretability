# TDAG-Net — Results Summary (2023 corpus)

_Generated 2026-08-04 19:25 · device cpu · seed 42_


## 1. Dataset

| Property | Value |
|---|---|
| Corpus | Nanjing Metro AFC 2023 |
| Source files ingested | 17 |
| Raw records read | 95,800,909 |
| Records retained | 95,733,909 |
| Stations (after excluding [333]) | 191 |
| Lines | [1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13] |
| Usable days | 57 (2023-03-01 → 2023-05-31) |
| Interval | 10 min, 06:00–23:50 |
| Ticket channels | 25 (top 24 + OTHER, 99.58% coverage) |
| Transfer (type-4) share | 0.095% |
| Windows (train/val/test) | 3,640 / 728 / 819 |
| Event days flagged | 22 (182 test windows) |

## 2. Personas discovered (TD-PDM prior)

| k | Persona | Ridership share | Codes |
|---|---|---|---|
| 0 | Commuter | 36.6% | 10 |
| 1 | Casual | 49.5% | 5 |
| 2 | Tourist/Visitor | 5.7% | 2 |
| 3 | Concession/Student | 8.2% | 8 |

## 3. Architecture decision evidence

- Origin-graph vs destination-graph cosine (**Option A**): `0.9436`
- Mean pairwise persona-graph cosine (**Option B**): `0.9855`
- Verdict: persona views are NOT more distinct than the O/D split.

## 4. Main results (test set)

| Model                     |    RMSE |     MAE |   WMAPE |     R2 |     EPE |     NDE |   Params |   Epochs |
|:--------------------------|--------:|--------:|--------:|-------:|--------:|--------:|---------:|---------:|
| LSTM                      | 47.5532 | 18.3669 | 25.0264 | 0.8868 | 19.1082 | 29.8562 |   201228 |       80 |
| TCN                       | 42.4502 | 17.1915 | 23.4249 | 0.9098 | 18.6717 | 26.6523 |    60844 |       80 |
| ConvLSTM                  | 40.0401 | 16.5339 | 22.5288 | 0.9198 | 19.2211 | 25.1391 |    51724 |       80 |
| SVR-LSTM                  | 48.8592 | 18.8717 | 25.7142 | 0.8805 | 19.7184 | 30.6762 |    69644 |       80 |
| STGCN                     | 40.6729 | 16.8920 | 23.0167 | 0.9172 | 17.8865 | 25.5364 |    38796 |       80 |
| DCRNN                     | 39.3404 | 16.5172 | 22.5060 | 0.9225 | 18.2823 | 24.6999 |    64140 |       80 |
| Graph WaveNet             | 35.6558 | 15.3145 | 20.8672 | 0.9364 | 17.2931 | 22.3864 |    65068 |       80 |
| AGCRN                     | 30.8669 | 13.4881 | 18.3786 | 0.9523 | 16.4751 | 19.3797 |   258050 |       80 |
| TGALSTM                   | 35.2160 | 15.2331 | 20.7564 | 0.9379 | 17.3800 | 22.1103 |   210527 |       80 |
| TDAG-Net (Input-Splitter) | 35.2577 | 15.8458 | 21.5913 | 0.9378 | 16.7107 | 22.1365 |   285477 |       80 |
| TDAG-Net (ours)           | 31.9480 | 14.0010 | 19.0775 | 0.9489 | 16.0786 | 20.0585 |   254260 |       80 |
| TDAG-Net w/o PC-HGAT      | 34.6549 | 15.5036 | 21.1249 | 0.9399 | 16.6512 | 21.7581 |   285477 |       80 |
| TDAG-Net w/o EC-AAM       | 34.6549 | 15.5036 | 21.1249 | 0.9399 | 16.6512 | 21.7581 |   285477 |       80 |
| TDAG-Net w/o Adaptive     | 34.6814 | 15.6138 | 21.2751 | 0.9398 | 17.3113 | 21.7747 |   285477 |       80 |

### Event days only

| Model                     |    RMSE |     MAE |   WMAPE |     R2 |     EPE |     NDE |   Params |   Epochs |
|:--------------------------|--------:|--------:|--------:|-------:|--------:|--------:|---------:|---------:|
| LSTM                      | 39.3673 | 17.0939 | 24.4620 | 0.9109 | 19.1082 | 26.3777 |   201228 |       80 |
| TCN                       | 38.3338 | 16.3667 | 23.4213 | 0.9155 | 18.6717 | 25.6852 |    60844 |       80 |
| ConvLSTM                  | 39.0553 | 16.6839 | 23.8752 | 0.9123 | 19.2211 | 26.1687 |    51724 |       80 |
| SVR-LSTM                  | 40.7606 | 17.6714 | 25.2883 | 0.9045 | 19.7184 | 27.3113 |    69644 |       80 |
| STGCN                     | 36.4055 | 15.7876 | 22.5926 | 0.9238 | 17.8865 | 24.3932 |    38796 |       80 |
| DCRNN                     | 37.3331 | 16.1627 | 23.1294 | 0.9199 | 18.2823 | 25.0147 |    64140 |       80 |
| Graph WaveNet             | 34.3887 | 14.7641 | 21.1280 | 0.9320 | 17.2931 | 23.0419 |    65068 |       80 |
| AGCRN                     | 33.9951 | 14.5182 | 20.7761 | 0.9335 | 16.4751 | 22.7781 |   258050 |       80 |
| TGALSTM                   | 35.6456 | 15.4671 | 22.1340 | 0.9269 | 17.3800 | 23.8840 |   210527 |       80 |
| TDAG-Net (Input-Splitter) | 33.8405 | 15.3294 | 21.9370 | 0.9342 | 16.7107 | 22.6746 |   285477 |       80 |
| TDAG-Net (ours)           | 32.3069 | 14.0234 | 20.0679 | 0.9400 | 16.0786 | 21.6470 |   254260 |       80 |
| TDAG-Net w/o PC-HGAT      | 33.7996 | 15.1355 | 21.6594 | 0.9343 | 16.6512 | 22.6471 |   285477 |       80 |
| TDAG-Net w/o EC-AAM       | 33.7996 | 15.1355 | 21.6594 | 0.9343 | 16.6512 | 22.6471 |   285477 |       80 |
| TDAG-Net w/o Adaptive     | 34.9245 | 15.7154 | 22.4893 | 0.9299 | 17.3113 | 23.4008 |   285477 |       80 |

## 5. Training configuration (fully tabulated — TGALSTM did not report δ)

| Hyper-parameter | Value |
|---|---|
| `seq_len` | 12 |
| `horizon` | 6 |
| `batch_size` | 32 |
| `epochs` | 80 |
| `lr` | 0.0003 |
| `weight_decay` | 0.0001 |
| `huber_delta` | 1.0 |
| `grad_clip` | 5.0 |
| `hidden_dim` | 64 |
| `tcn_channels` | 64 |
| `tcn_dilations` | (1, 2, 4, 8) |
| `gat_heads` | 4 |
| `gat_dropout` | 0.1 |
| `lstm_hidden` | 128 |
| `dropout` | 0.1 |
| `node_emb_dim` | 16 |
| `n_personas` | 4 |
| `n_card_channels` | 24 |
| `adj_topk` | 8 |
| `corr_min` | 0.3 |
| `venue_radius_km` | 3.0 |
| `early_stop_patience` | 15 |
| `seed` | 42 |

## 7. Reproduction

1. Open the notebook in Colab and mount the same Drive.
2. `Runtime ▸ Run all`. Cached stages are skipped; training resumes from `runs/*/checkpoints/last.pt`.
3. To force a stage: `CFG.force_recompute = ('stage_name',)`.


## 8. Outstanding manual inputs

- `data/metadata/station_metadata.csv` — coordinates: **supplied**
- `data/metadata/event_venues.csv` — C_venue method: **explicit nearest_station_ids**
- `data/metadata/event_calendar.csv` — 0 curated rows; 155 auto-detected candidates await verification (`DETECTED_event_candidates_2023.csv`)
- `data/metadata/weather.csv` — not supplied (optional)
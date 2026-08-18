# Nanjing Metro Automatic Fare Collection (AFC) 2023 Dataset Documentation


## 📌 Dataset Overview

The **Nanjing Metro AFC 2023 Dataset** is a comprehensive, multi-modal urban rail transit transaction corpus collected from the **Nanjing Metro Rapid Transit Network** in Jiangsu Province, China. The dataset captures passenger tap-in and tap-out activity across 191 metro stations and 13 subway lines over a 3-month period.

```text
               ┌────────────────────────────────────────────────────────┐
               │              Nanjing Metro AFC 2023 Corpus             │
               └───────────────────────────┬────────────────────────────┘
                                           │
             ┌─────────────────────────────┼─────────────────────────────┐
             ▼                             ▼                             ▼
   ┌───────────────────┐         ┌───────────────────┐         ┌───────────────────┐
   │ 95.73M Entry Taps │         │    191 Stations   │         │  57 Usable Days   │
   │ (190.9M Total Taps│         │  (13 Subway Lines)│         │ (Mar 1 - May 31)  │
   └───────────────────┘         └───────────────────┘         └───────────────────┘
```

---

## 📊 Summary Statistics & Metadata

| Property | Value / Description |
| :--- | :--- |
| **City & Transport Network** | Nanjing Metro System (Nanjing, China) |
| **Temporal Span** | 57 usable working and event days (2023-03-01 to 2023-05-31) |
| **Operating Hours** | 06:00:00 to 23:50:00 daily (18 hours/day) |
| **Temporal Granularity** | 10-minute intervals (108 time bins per day) |
| **Raw Records Read** | 95,800,909 transaction records |
| **Clean Retained Entry Taps** | **95,733,909** records (99.93% retention rate) |
| **Total AFC Tap Events** | **190,911,927** (Sum of entry and exit events across 57 days) |
| **Daily Ridership Volume** | Median **3.30 million** tap events/day (Mean **3.35M**, Peak **3.84M**) |
| **Network Scale** | 191 physical stations (Station 333 excluded due to zero activity) |
| **Subway Lines Covered** | 13 metro lines (Lines 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13) |
| **Ticket Channels** | 25 distinct fare card categories (covering 99.58% of volume) |
| **Transfer Rate** | 0.095% type-4 transfer transaction share |
| **Split Dimensions** | 3,640 train / 728 validation / 819 test windows (70% / 14% / 16%) |
| **Event Days Flagged** | **22 major event days** (182 test time windows, 22.2% of test set) |

---

## 💳 Ticket Channels & Persona Mapping ($P_0$ Prior)

The dataset incorporates 25 fare ticket categories that are decomposed into 4 primary passenger behavioral personas using the **TD-PDM soft-assignment prior matrix $P_0$**:

```text
 100% ┌────────────────────────────────────────────────────────────────────────┐
      │  Casual (49.5%)                                                        │
  50% ├────────────────────────────────────────────────────────────────────────┤
      │  Commuter (36.6%)                                                      │
  15% ├────────────────────────────────────────────────────────────────────────┤
      │  Concession/Student (8.2%)                                             │
   5% ├────────────────────────────────────────────────────────────────────────┤
      │  Tourist/Visitor (5.7%)                                                │
   0% └────────────────────────────────────────────────────────────────────────┘
```

| Persona ID | Persona Name | Ridership Share | Number of Ticket Codes | Primary Fare Channels & Mobility Characteristics |
| :---: | :--- | :---: | :---: | :--- |
| **k = 0** | **Commuter** | **36.6%** | 10 codes | Monthly Passes, IC Work Cards, Metro Employee Cards, Commuter Smartcards. Characterized by sharp 08:00 and 17:30 peak weekday hours. |
| **k = 1** | **Casual** | **49.5%** | 5 codes | Mobile QR Pay (Alipay/WeChat), Single Journey Tokens, UnionPay Credit Cards, Contactless NFC. Characterized by broad midday and evening leisure flow. |
| **k = 2** | **Tourist / Visitor** | **5.7%** | 2 codes | Tourist 1-Day / 3-Day Passes, Special Sightseeing Line Tickets. Concentrated around scenic hubs (Confucian Temple, Sun Yat-sen Mausoleum) on weekends. |
| **k = 3** | **Concession / Student** | **8.2%** | 8 codes | Student Cards, Senior Citizen Passes, Disability Cards, Veteran Passes. Characterized by early afternoon travel and discounted fare usage. |

---

## 🎪 Event Calendar & Surge Scanning

The dataset includes **22 flagged major event days** caused by sports matches, stadium concerts, holiday travel surges, and severe weather anomalies.

* **Surge Criteria:** A station-day is flagged as an event surge candidate if its robust Z-score exceeds $4.0$:
  $$z = \frac{x_{\text{peak}} - \text{median}(x)}{\text{IQR}(x)} \ge 4.0$$
* **Test Window Event Coverage:** 182 out of 819 test windows fall on event days (**22.2%** event coverage).
* **Peak Anatomy:** Major event spikes exhibit surge ratios up to **$4.14\times$** baseline median volume at proximal hub stations.

---

## 📐 Tensor Schemas & Feature Formatting

The processed dataset is structured into spatial-temporal tensors for deep learning model input:

```text
               ┌────────────────────────────────────────────────────────┐
               │                Input Feature Tensors                   │
               └───────────────────────────┬────────────────────────────┘
                                           │
             ┌─────────────────────────────┼─────────────────────────────┐
             ▼                             ▼                             ▼
  ┌───────────────────────┐   ┌──────────────────────────┐   ┌───────────────────────┐
  │ Total Flow Tensor     │   │ Persona Flow Tensor      │   │ Exogenous Vector z_t  │
  │ X_total               │   │ X_persona                │   │ (8 Context Features)  │
  │ (T x 191 x 2)         │   │ (T x 191 x 2 x 4)        │   │ (T x 8)               │
  └───────────────────────┘   └──────────────────────────┘   └───────────────────────┘
```

1. **Total Flow Tensor $X_{\text{total}} \in \mathbb{R}^{T \times 191 \times 2}$:**
   * Dimensions: $T = 5,187$ total time steps $\times$ $191$ stations $\times$ $2$ directions (0: Entry, 1: Exit).
2. **Persona Flow Tensor $X_{\text{persona}} \in \mathbb{R}^{T \times 191 \times 2 \times 4}$:**
   * Flow decomposed across the 4 passenger personas ($K=4$).
3. **Exogenous Context Vector $z_t \in \mathbb{R}^8$:**
   * `tod_sin`, `tod_cos`: Cyclic sine/cosine encoding of time-of-day.
   * `dow_sin`, `dow_cos`: Cyclic sine/cosine encoding of day-of-week.
   * `is_weekend`: Binary weekend flag ($0$ or $1$).
   * `event_flag`: Binary indicator for flagged event days ($0$ or $1$).
   * `event_countdown`: Continuous normalized countdown to event peak window.
   * `t`: Normalized continuous time index.

---

## 🛠️ Data Cleaning & Pre-processing Rules

1. **Station Filtering:** Excluded Station `333` due to zero transaction records throughout the 57-day period.
2. **Outlier Clipping:** Flow volumes clipped to $[0.0, \infty)$ to enforce non-negativity.
3. **Missing Value Imputation:** All missing time slots imputed via historical median profile interpolation; zero missing values (`NaN = 0`) exist in the processed dataset.
4. **Normalisation:** Z-score standardization applied per channel, with inverse transformations applied for reporting RMSE, MAE, and WMAPE metrics in physical tap counts.

---

## 🔒 Data Access & Licensing

The raw AFC transaction records and processed tensor objects are stored under `data/` and `exports/`.

* **Usage:** For academic and research reproduction only.
* **Privacy:** All individual passenger IDs and smartcard numbers have been anonymized using cryptographic SHA-256 hashing to preserve passenger privacy.

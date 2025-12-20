🏋️‍♂️ Gym IMU Manual Segmentation Tool

*A dataset cleanup tool for manually separating Active vs Idle motion in Apple Watch IMU recordings.*

---

## 📌 Overview

This repository provides a **manual segmentation tool** to clean the raw IMU dataset from Kaggle’s:
👉 [https://www.kaggle.com/datasets/shakthisairam123/gym-workout-imu-dataset](https://www.kaggle.com/datasets/shakthisairam123/gym-workout-imu-dataset)

The original dataset contains **long idle periods** before and after each exercise.
These idle frames **hurt machine learning training**, especially rep counting and movement classification.

### ✔ What this tool does

* Load any IMU CSV file
* Visualize **gravity signals (X/Y/Z)**
* Set:

  * **Start frame** (first active movement)
  * **End frame** (last active movement)
* Automatically export:

  * `<filename>_active.csv` → trimmed active frames
  * `<filename>_idle.csv` → everything else
* Add **exercise labels** using FINE_MAP
* Build a clean dataset ready for ML training

---

## 📁 Repository Structure

```
gym-segmentation/
│
├── data_raw/                # original CSVs from Kaggle (you add these)
├── data_processed/          # segmented active/idle CSVs (output)
├── notebooks/
│   └── manual_segmenter.ipynb   # Jupyter segmentation UI
│
├── requirements.txt
└── README.md
```

⚠️ `data_raw/` is empty on GitHub — users must download the dataset manually.

---

# 📥 1. Download Dataset

This project uses the **Gym Workout IMU Dataset** from Kaggle:

🔗 [https://www.kaggle.com/datasets/shakthisairam123/gym-workout-imu-dataset](https://www.kaggle.com/datasets/shakthisairam123/gym-workout-imu-dataset)

Steps:

1. Download the ZIP
2. Extract it
3. Move all CSV files into:

```
gym-segmentation/data_raw/
```

Example:

```
data_raw/
 ├── 011224_APULL_W61_S1_R12.csv
 ├── 171124_MGTBR_W50_S1_R08.csv
 ├── 241124_PREC_W7_5_S1_R14.csv
 └── ...
```

---

# 🛠 2. Installation

Install Python dependencies:

```bash
pip install -r requirements.txt
```

Libraries used:

* pandas
* numpy
* matplotlib
* ipywidgets
* jupyter

---

# 🧭 3. Using the Manual Segmenter

Run Jupyter Notebook:

```bash
jupyter notebook
```

Then open:

```
notebooks/manual_segmenter.ipynb
```

You will see a GUI with:

* file dropdown
* gravity visualization
* start/end frame selectors
* save buttons

### Example UI

<img width="704" height="435" alt="Screenshot 2025-12-20 at 15 33 42" src="https://github.com/user-attachments/assets/fdbf4d58-3f34-4a83-ae2f-7ef5ac1171d1" />#

---

## 🔍 Workflow

## **1️⃣ Choose a File**

Use the dropdown to select any CSV from your dataset.

Each filename contains important metadata:

```
011224_APULL_W61_S1_R12-2024-12-01_07-36-28.csv
          │    │   │   └─ R12 → Expected 12 repetitions  
          │    │   └──── S1 → Set 1
          │    └────── W61 → Weight used
          └──────────── APULL → Exercise code (e.g., Pullups)
```

✔ **RXX = expected number of reps**
✔ **WXX = weight**
✔ **S = set number**
✔ **Code maps to the exercise group** (using FINE_MAP)

---

## **2️⃣ Visualize the Motion**

Press **“Visualize”**.

You will see gravityX / gravityY / gravityZ:

🟦 gravityX
🟩 gravityY
🟥 gravityZ

Vertical dashed lines show the selected frame range.

This plot helps you locate:

* The **first movement**
* The **last movement**
* Any **idle before** or **idle after**
<img width="704" height="435" alt="Screenshot 2025-12-20 at 15 33 42" src="https://github.com/user-attachments/assets/fdbf4d58-3f34-4a83-ae2f-7ef5ac1171d1" />

---

## **3️⃣ Match With the Filename Rep Count (IMPORTANT)**

Before trimming, **you MUST check that the number of visible reps matches the filename label.**

Example:

Filename → `R10`
Then visually, you should see **10 full cycles (up–down or down–up peaks)**.

⚠ Why this matters?

Open-source data often contains:

* Long idle before starting
* Long idle after finishing
* Extra noise at the end
* Sometimes partial reps the watch captured accidentally

👉 **Your job is to cut the plot so that ONLY the correct number of reps remain.**

So the final trimmed segment must contain:

* **Exactly RXX motion cycles**
* No idle before/after
* Peaks aligned with the expected number of reps

---

## **4️⃣ Set the Frames**

After identifying the correct region:

* **Start Frame** → first clear rep peak
* **End Frame** → last clear rep peak

Everything outside this range is treated as **idle**.

You can adjust these numbers manually while checking the visualization.

---

## **5️⃣ Save Segments**

Click **Save Segments**.

This will create two files:

```
<filename>_active.csv    # Only the active movement part
<filename>_idle.csv      # Everything else marked as Idle
```

The active segment includes:

* All gravity / acceleration data
* A new column `label` (with exercise name)

The idle segment includes:

* Only idle data
* `label = Idle`

These segmented files will later be merged into one big dataset for ML training.

---

## ✔ **End Result**

You produce a **clean, correctly-labeled** dataset where:

* Active motion matches the expected rep count
* Idle noise is removed
* ML models will train MUCH better
* Rep detection and classification become far more accurate
---

# 🏷 4. Exercise Label Mapping (FINE_MAP)

Raw file codes map to higher-level exercise names:

```
LatPull
Pullups
CableRow
BicepCurl
InclinePress
ShoulderPress
LateralRaise
TricepExt
Wrist
Idle
```

Example:

| Raw Code | Mapped Label  |
| -------- | ------------- |
| CGCR     | CableRow      |
| IDBC     | BicepCurl     |
| SBLP     | LatPull       |
| MSP      | ShoulderPress |
| DWC      | Wrist         |

---

# ❓ Why Manual Segmentation?

The Kaggle dataset includes:

* long idle periods
* machine setup
* wrist rotation
* recording early
* walking between sets

These **damage ML training**.

Manual segmentation ensures:

✔ clean active reps
✔ good idle samples
✔ more accurate ML classification
✔ stable rep counting models

---

# 🤝 Team Instructions

To collaborate:

1. Clone the repository
2. Download Kaggle dataset → place in `data_raw/`
3. Open notebook
4. Segment your assigned files
5. Commit processed files only:

```bash
git add data_processed/*.csv
git commit -m "Segmented APULL files"
git push
```

**DO NOT commit:**

* data_raw/
* checkpoint files
* temporary notebook outputs

---

# 📚 Dataset Attribution

```
Dataset: “Gym Workout IMU Dataset”
Author: Shakthi Sairam
License: CC BY-SA 4.0
Source: https://www.kaggle.com/datasets/shakthisairam123/gym-workout-imu-dataset
```

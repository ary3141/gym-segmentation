# 🏋️‍♂️ Gym IMU Manual Segmentation Tool

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

### Example UI (add screenshot later)

```
[screenshot_placeholder_ui.png]
```

---

## 🔍 Workflow

### 1️⃣ Choose a file

Pick a CSV from the dropdown.

### 2️⃣ Visualize motion

Click **Visualize** → gravity X/Y/Z is plotted.

```
[screenshot_placeholder_plot.png]
```

This allows you to see where active movement starts/ends.

---

### 3️⃣ Set frames

* **Start frame:** first peak / movement
* **End frame:** last movement

Idle before/after these frames will be removed.

---

### 4️⃣ Save Segments

Click **Save Segments**.

This generates:

```
data_processed/
 ├── <file>_active.csv
 └── <file>_idle.csv
```

Active file includes:

```
label = "<exercise>"
```

Idle file includes:

```
label = "Idle"
```

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

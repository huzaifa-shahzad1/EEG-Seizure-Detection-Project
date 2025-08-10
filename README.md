# EEG Seizure Detection Project

This project builds a full pipeline to **download**, **process**, and **model** EEG data from the [CHB-MIT Scalp EEG Database](https://physionet.org/content/chbmit/1.0.0/) to detect seizure events.

---

## 📂 Project Structure
```
eeg_seizure_project/
├── data/
│ ├── raw/ 
│ │ ├── chb01/
│ │ ├── chb02/
│ │ └── ...
│ ├── features/ 
│ │ └── eeg_features.csv
│ ├── processed/ 
│ └── external/ 
│
├── db/
│ ├── init.sql 
│ └── queries/ 
│
├── notebooks/
│ ├── 01_exploration.ipynb
│ └── 02_model_prototyping.ipynb
│
├── scripts/
│ ├── extract_edf.py 
│ ├── extract_features.py 
│ ├── load_to_postgres.py 
│ ├── train_model.py 
│ ├── evaluate_model.py 
│ ├── utils.py 
│ └── preprocess.py (optional) # separate preprocessing if needed
│
├── models/
│ └── model_v1.pkl 
│
├── tests/
│ └── test_feature_extraction.py
│
├── configs/
│ └── config.yaml # paths, DB creds, feature/window parameters
│
├── logs/ # pipeline run logs
│
├── requirements.txt 
├── README.md
└── .gitignore
```
## 📌 Files to Use & Their Purpose

This section outlines **exactly** which files are part of the working pipeline and how they are used.

---

### 1️⃣ Data Download
- **`scripts/extract_edf.py`**
  - Downloads **all EDF files** for selected patients.
  - Saves them under `data/raw/<patient_id>/`.
  - Also generates a `seizure_file_list.txt` mapping file → seizure label.
  - **Run first** to ensure all raw data is available.

---

### 2️⃣ Feature Extraction & Preprocessing
- **`scripts/extract_features.py`**
  - Reads EDF files from `data/raw/`.
  - Handles **duplicate channel names**.
  - Segments signals into fixed-length windows (balanced for seizure/non-seizure).
  - Extracts statistical & spectral EEG features.
  - Outputs **model-ready CSV** at `data/features/eeg_features.csv`.

---

### 3️⃣ Database Storage
- **`scripts/load_to_postgres.py`**
  - Loads `data/features/eeg_features.csv` into local PostgreSQL.
  - Adds a `patient_id` column automatically.
  - Replaces existing table in DB.

---

### 4️⃣ Model Training
- **`scripts/train_model.py`**
  - Reads data from CSV **or** PostgreSQL.
  - Trains classifier to detect seizures.
  - Saves trained model into `models/model_v1.pkl`.

---

### 5️⃣ Model Evaluation
- **`scripts/evaluate_model.py`**
  - Loads trained model from `models/`.
  - Runs evaluation on test set.
  - Outputs metrics and plots (ROC, confusion matrix) to `logs/` or `notebooks/`.

---

### 6️⃣ Utilities
- **`scripts/utils.py`**
  - Contains helper functions for:
    - Feature extraction
    - Signal preprocessing
    - File parsing

---

### 7️⃣ Configuration
- **`configs/config.yaml`**
  - Central place for:
    - Paths to data
    - DB credentials
    - Feature extraction parameters
    - Window length for segmentation

---

### 8️⃣ Optional Supporting Files
- **`notebooks/01_exploration.ipynb`**
  - For initial dataset exploration.
- **`notebooks/02_model_prototyping.ipynb`**
  - For quick testing of models before adding to main pipeline.
- **`db/init.sql`**
  - SQL script to create database schema if needed.
- **`tests/test_feature_extraction.py`**
  - Unit tests for feature extraction functions.

---



## ⚙️ Installation

1. **Clone the repository**
   ```bash
   git clone https://github.com/yourusername/eeg_seizure_project.git
   cd eeg_seizure_project

## Create and activate virtual environment
   ```bash
   python -m venv venv
   source venv/bin/activate     # Linux/Mac
   venv\Scripts\activate        # Windows
   ```

## Install dependencies
   ```bash
      pip install -r requirements.txt
   ```


## 🚀 Pipeline Workflow

### 1️⃣ Download EEG Data
Run the script to download EDF files for selected patients:
```bash
python scripts/extract_edf.py
```
- Downloads EDF files into ```data/raw/<patient_id>/```.
- Creates data/raw/seizure_file_list.txt marking each file as SEIZURE or NO_SEIZURE.

### 2️⃣ Extract Features
Generate features from EEG files:
```bash
python scripts/extract_features.py
```
- Loads EDF files, handles duplicate channels.
- Segments EEG into fixed windows (e.g., 10 seconds).
- Extracts statistical + spectral features.
- Balances dataset (equal seizure / non-seizure samples).
- Saves output to data/features/eeg_features.csv.

### 3️⃣ Load Features into PostgreSQL
Load processed CSV into local PostgreSQL:
```bash
python scripts/load_to_postgres.py
```
- Creates table eeg_features in local DB.
- Adds patient_id column.

### 4️⃣ Train Model
Train a classifier using extracted features:
```bash
python scripts/train_model.py
```
- Reads from CSV or DB.
- Splits into train/test.
- Saves trained model to models/.

### 5️⃣ Evaluate Model
Run evaluation:
```bash
   python scripts/evaluate_model.py
```
- Generates metrics (accuracy, recall, etc.).
- Saves plots to logs/ folder.

## 📊 Features Extracted
- **Statistical**: Mean, Median, Variance, Standard Deviation, Skewness, Kurtosis
- **Spectral**: Bandpower in delta, theta, alpha, beta, gamma ranges
- **Information-theoretic**: Signal Entropy

## 🛠 Requirements
- Python: 3.9+
- PostgreSQL: Default localhost:5432, user postgres
- Libraries:
```bash 
mne
pandas
numpy
scipy
scikit-learn
sqlalchemy
psycopg2
```

### ✨ Notes
EEG data is sensitive medical data — use ethically and follow PhysioNet terms.
Modify configs/config.yaml to change:
- Patients to download
- Segment length
- Features to extract
- Database credentials


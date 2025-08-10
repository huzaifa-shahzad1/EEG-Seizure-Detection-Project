# EEG Seizure Detection Project

This project builds a full pipeline to **download**, **process**, and **model** EEG data from the [CHB-MIT Scalp EEG Database](https://physionet.org/content/chbmit/1.0.0/) to detect seizure events.

---

## 📂 Project Structure
```
eeg_seizure_project/
├── data/
│ ├── raw/ # downloaded EDF files (per patient)
│ │ ├── chb01/
│ │ ├── chb02/
│ │ └── ...
│ ├── features/ # generated feature CSV(s)
│ │ └── eeg_features.csv
│ ├── processed/ # optional: intermediate npz / segments etc.
│ └── external/ # optional: external files / metadata
│
├── db/
│ ├── init.sql # DB schema / table creation scripts
│ └── queries/ # useful SQL queries
│
├── notebooks/
│ ├── 01_exploration.ipynb
│ └── 02_model_prototyping.ipynb
│
├── scripts/
│ ├── extract_edf.py # downloads EDFs and writes seizure_file_list.txt
│ ├── extract_features.py # preprocess, segment, extract features, balance dataset
│ ├── load_to_postgres.py # loads features CSV into local PostgreSQL
│ ├── train_model.py # train classifier (reads CSV or DB)
│ ├── evaluate_model.py # evaluate model and save metrics/plots
│ ├── utils.py # helper functions (e.g., parsers, feature extraction)
│ └── preprocess.py (optional) # separate preprocessing if needed
│
├── models/
│ └── model_v1.pkl # saved trained model(s)
│
├── tests/
│ └── test_feature_extraction.py
│
├── configs/
│ └── config.yaml # paths, DB creds, feature/window parameters
│
├── logs/ # pipeline run logs
│
├── requirements.txt # Python dependencies
├── README.md
└── .gitignore
```

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

1️⃣ Download EEG Data
Run the script to download EDF files for selected patients:

bash
Copy
Edit
python scripts/extract_edf.py
Downloads EDF files into data/raw/<patient_id>/.

Creates data/raw/seizure_file_list.txt marking each file as SEIZURE or NO_SEIZURE.

2️⃣ Extract Features
Generate features from EEG files:

bash
Copy
Edit
python scripts/extract_features.py
Loads EDF files, handles duplicate channels.

Segments EEG into fixed windows (e.g., 10 seconds).

Extracts statistical + spectral features.

Balances dataset (equal seizure / non-seizure samples).

Saves output to data/features/eeg_features.csv.

3️⃣ Load Features into PostgreSQL
Load processed CSV into local PostgreSQL:

python scripts/load_to_postgres.py
Creates table eeg_features in local DB.

Adds patient_id column.

4️⃣ Train Model
Train a classifier using extracted features:

python scripts/train_model.py
Reads from CSV or DB.

Splits into train/test.

Saves trained model to models/.

5️⃣ Evaluate Model
Run evaluation:

python scripts/evaluate_model.py
Generates metrics (accuracy, recall, etc.).

Saves plots to logs/ folder.

📊 Features Extracted
Statistical: Mean, Median, Variance, Standard Deviation, Skewness, Kurtosis

Spectral: Bandpower in delta, theta, alpha, beta, gamma ranges

Information-theoretic: Signal Entropy

🛠 Requirements
Python: 3.9+

PostgreSQL: Default localhost:5432, user postgres

Libraries:

mne

pandas

numpy

scipy

scikit-learn

sqlalchemy

psycopg2

✨ Notes
EEG data is sensitive medical data — use ethically and follow PhysioNet terms.

Modify configs/config.yaml to change:

Patients to download

Segment length

Features to extract

Database credentials


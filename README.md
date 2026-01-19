# Sentinel4AI – AI/ML Module Documentation

## 📌 Overview
This directory contains all Machine Learning logic for Sentinel4AI.  
Responsibilities of the AI/ML module:

- Malicious Payload Classifier (NLP)
- CVE Severity Prediction Model
- Attack Prediction Model (multiclass)
- Risk Scoring Algorithm (numeric + ML hybrid)
- Inference Pipeline & API Integration
- Data Processing, Cleaning, Feature Engineering

The ML module acts as the **brain** of the entire platform.

---

## 🚀 Project Structure
```
ml/
├─ data/
│   ├─ raw/
│   └─ processed/
├─ notebooks/
├─ src/
│   ├─ data_ingest.py
│   ├─ feature_pipeline.py
│   ├─ train_payload_classifier.py
│   ├─ train_attack_predictor.py
│   ├─ inference.py
│   └─ utils.py
├─ saved_models/
├─ reports/
└─ README.md
```

---

## ⚙️ Installation & Setup

### Create Virtual Environment
```bash
cd ml
python -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
```

### requirements.txt example
```
numpy
pandas
scikit-learn
xgboost
torch
transformers
datasets
sentencepiece
nltk
spacy
tqdm
joblib
```

### NLP Setup
```bash
python -m nltk.downloader punkt stopwords
python -m spacy download en_core_web_sm
```

---

## 📦 Data Collection

### 1. CVE Dataset (NVD JSON feed)
**Download:**  
https://nvd.nist.gov/vuln/data-feeds

**Store in:**
```
ml/data/raw/cve/
```

### 2. Payload Dataset
**Use:**  
PayloadsAllTheThings repository

**Processed CSV example:**
```csv
payload,label
"' OR 1=1 --",SQLI
"<script>alert(1)</script>",XSS
...
```

### 3. Scanner Outputs (from backend)
Used as ML input.

---

## 🧠 Models

### 1. Payload Classifier (NLP)
**Purpose:**  
Detect whether input contains SQLi, XSS, LFI, or safe text.

**Model choices:**
- TF-IDF + Logistic Regression (simple)
- DistilBERT fine-tuning (better)

**Train:**
```bash
python src/train_payload_classifier.py
```

### 2. CVE Severity Predictor
**Input:**  
CVE description text

**Output:**  
Severity (0–10)

**Model:**  
XGBoost regressor or transformer regression head

### 3. Attack Prediction Model
**Predicts:**  
SQLi, XSS, RCE, DDoS, MITM, or NONE

**Train:**
```bash
python src/train_attack_predictor.py
```

**Save models in:**
```
ml/saved_models/
```

---

## 🔧 Feature Engineering

**Features include:**
- `open_port_count`
- `nikto_warning_count`
- `weak_tls_flag`
- `payload_classifier_output`
- `cve_avg_severity`
- scanner text embeddings (BERT or TF-IDF)

**Pipeline is in:**
```
src/feature_pipeline.py
```

---

## 🧪 Evaluation

**Metrics:**
- Payload: F1, precision, recall
- CVE: RMSE, MAE
- Attack predictor: Accuracy, F1 macro, confusion matrix

**Reports stored in:**
```
ml/reports/
```

---

## 📡 Inference API Contract

### Input (from backend):
```json
{
  "scan": {
    "open_ports": [22, 80],
    "nikto_warnings": 3,
    "ssl_issues": true,
    "scanner_text": "Possible SQLi detected...",
    "cve_list": [{"severity": 9.8}]
  }
}
```

### Output:
```json
{
  "predicted_attack": "SQLi",
  "probabilities": {"SQLi": 0.92, "XSS": 0.03},
  "risk_score": 88,
  "explanation": "High SQL-related signals found."
}
```

---

## 🧩 Integration with Backend

Backend imports or calls:
```python
from ml.src.inference import predict_attack
```

OR run ML inference microservice via:
```bash
uvicorn ml.src.inference_api:app --port 9000
```

---

## ✔️ Checklist
- [ ] Dataset ready
- [ ] Feature pipeline saved
- [ ] Payload classifier trained
- [ ] Attack predictor trained
- [ ] Inference module completed
- [x] README finished

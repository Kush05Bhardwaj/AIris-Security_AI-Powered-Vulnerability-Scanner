# Sentinel4AI – Backend (FastAPI) Documentation

## 📌 Overview
The backend is responsible for:
- Running vulnerability scans
- Parsing scanner outputs
- Aggregating results
- Storing data in the database
- Calling ML inference
- Exposing REST APIs
- Generating PDF reports
- Sending real-time logs via WebSockets

---

## 🗂️ Project Structure
```
backend/
├─ app/
│   ├─ main.py
│   ├─ auth/
│   ├─ scanners/
│   ├─ aggregator/
│   ├─ routes/
│   ├─ db/
│   ├─ models/
│   └─ utils/
├─ templates/
├─ reports/
├─ tests/
└─ README.md
```

---

## ⚙️ Setup
```bash
cd backend
python -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
uvicorn app.main:app --reload
```

### requirements.txt
```
fastapi
uvicorn[standard]
python-nmap
pymongo
sqlalchemy
psycopg2-binary
requests
python-multipart
PyJWT
jinja2
pdfkit
python-dotenv
```

---

## 🔐 Authentication (JWT)

**Endpoints:**
- `POST /auth/register`
- `POST /auth/login`

**Token returned:**
```json
{"access_token": "…", "token_type": "bearer"}
```

Middleware checks for JWT on protected routes.

---

## 🔍 Scanning Engine

### 1. Nmap Scanner
**Location:**
```
scanners/nmap_scanner.py
```

**Executes:**
```bash
nmap -sV -p- -oX -
```

**Parses:**
- `open_ports`
- `service_names`
- os detection (optional)

### 2. Nikto Scanner
**Location:**
```
scanners/nikto_scanner.py
```

**Runs:**
```bash
nikto -h <target> -o output.txt
```

**Extracts:**
- warnings
- outdated software
- potential vulnerabilities

### 3. SSLScan
**Location:**
```
scanners/ssl_scanner.py
```

**Extract:**
- weak TLS protocols
- insecure ciphers

### 4. DirSearch
**Location:**
```
scanners/dirsearch_scanner.py
```

**Find:**
- hidden directories
- exposed files

---

## 🔗 Aggregator Module

**File:**
```
aggregator/aggregate.py
```

**Produces ML-ready JSON:**
```json
{
  "open_ports": [22, 80],
  "services": ["ssh", "http"],
  "nikto_warnings": 3,
  "ssl_issues": true,
  "sensitive_paths": ["/admin"],
  "scanner_text": "Nikto found XSS...",
  "cve_list": [{"severity": 9.8}]
}
```

---

## 🤖 ML Integration

Backend calls:
```python
resp = requests.post("http://localhost:9000/predict", json={"scan": aggregated_json})
```

Stores ML result into `scans` table/collection.

---

## 📄 Report Generation

### Using Jinja2 + wkhtmltopdf
HTML → PDF

**Templates:**
```
templates/report.html
reports/report_<id>.pdf
```

**Endpoint:**
```
GET /report/download/{id}
```

---

## 🔴 WebSockets (Live Logs)

**Channel:**
```
/ws/scan/{scan_id}
```

**Frontend sees:**
```
Running nikto...
80/tcp open http
XSS payload found...
```

---

## 🗄️ Database Schema

### MongoDB (recommended)

**Collections:**
- `users`
- `scans`
- `reports`

**Scan document example:**
```json
{
  "target": "http://dvwa",
  "status": "completed",
  "scan_result": {...},
  "ml_result": {...},
  "created_at": "..."
}
```

---

## ✔️ Backend Checklist
- [ ] FastAPI scaffold
- [ ] Auth working
- [ ] Scanner wrappers implemented
- [ ] Aggregator ready
- [ ] ML integration done
- [ ] WebSockets working
- [ ] PDF generator implemented
- [ ] Tests completed

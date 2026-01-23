# Sentinel4AI – Frontend Documentation (Next.js)

## 📌 Overview
The frontend is built using **Next.js + React + Tailwind + Recharts** and provides:

- Login & registration
- Start scan form
- Live scan logs (WebSockets)
- Results dashboard
- Attack prediction UI
- Risk visualization
- PDF report preview & download

---

## 🗂️ Project Structure
```
frontend/
├─ pages/
│   ├─ index.js
│   ├─ login.js
│   ├─ register.js
│   ├─ scan.js
│   ├─ results/[id].js
│   ├─ report/[id].js
├─ components/
│   ├─ LogTerminal.jsx
│   ├─ RiskMeter.jsx
│   ├─ VulnTable.jsx
│   ├─ PredictionCard.jsx
├─ services/
│   └─ api.js
├─ styles/
└─ README.md
```

---

## ⚙️ Setup
```bash
cd frontend
npm install
npm run dev
```

---

## 🌐 API Service Layer

**services/api.js:**
```js
import axios from "axios";
const API = "http://localhost:8000";

export const startScan = (data) => axios.post(`${API}/scan/start`, data);
export const getScanResults = (id) => axios.get(`${API}/scan/results/${id}`);
export const getPrediction = (id) => axios.get(`${API}/ml/predict_attack?scan_id=${id}`);
export const login = (data) => axios.post(`${API}/auth/login`, data);
```

---

## 🔴 WebSockets (Live Logs)

```js
import { io } from "socket.io-client";
const socket = io("http://localhost:8000");

socket.emit("join_scan", { scan_id });

socket.on("scan_log", (log) => {
  // append to terminal component
});
```

`LogTerminal.jsx` displays logs in a terminal-style UI.

---

## 📊 Charts (Recharts)

### Risk Meter
Use a custom circular progress bar.

### Radar Chart
Visualize vulnerability categories.

### Bar Chart
Severity counts.

---

## 🔐 Auth Flow

- Store JWT in `localStorage`
- Add interceptor for Axios

```js
axios.interceptors.request.use(config => {
  config.headers.Authorization = `Bearer ${localStorage.getItem("token")}`;
  return config;
});
```

---

## 🖥️ UI Pages

### 1) Login / Register
Simple forms → redirects to dashboard.

### 2) Scan Page
- Input field for target URL
- "Start Scan" button

### 3) Live Logs Page
- Terminal-like streaming logs
- Updates via WebSocket

### 4) Results Page
- Vulnerability table
- Risk meter
- Attack prediction card
- ML probability bar chart

### 5) Report Page
- Show HTML preview
- "Download PDF" button

---

## ✔️ Frontend Checklist
- [ ] Pages created
- [ ] Components implemented
- [ ] API services ready
- [ ] WebSockets integrated
- [ ] UI polished with Tailwind
- [ ] Dark mode (optional)
- [ ] Charts working
- [ ] Report preview ready

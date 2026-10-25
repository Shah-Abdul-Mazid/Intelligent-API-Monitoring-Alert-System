# Intelligent API Monitoring & Alert System

An automated monitoring, anomaly detection, and AI-powered alerting platform designed to proactively track microservice API health, identify SLA breaches, and generate human-readable root-cause alerts for SRE and engineering teams.

---

## 1. Project Description

Modern healthcare and enterprise systems depend heavily on external and internal APIs (such as patient records, appointment bookings, and billing services). These APIs can fail intermittently, exceed latency thresholds, or return abnormal payloads with zero records.

This project autonomously ingests API telemetry, detects anomalies using deterministic multi-factor rules, stores events in a persistent database, and uses an AI model (OpenAI GPT-4o-mini with an intelligent fallback engine) to generate clear, actionable incident alerts. It includes a real-time web dashboard for triage and complete REST API support.

---

## 2. Features

- **Automated Health Monitoring:** Evaluates HTTP status codes, response times, and payload record counts.
- **Multi-Factor Anomaly Detection:** Detects critical server failures (5xx), client errors (4xx), latency spikes, and silent zero-record returns.
- **AI-Powered Alert Generation:** Leverages OpenAI LLM (or automatic heuristic engine fallback) to produce concise, human-readable root-cause hypotheses and remediation steps.
- **Batch & Real-Time Ingestion:** Ingests static JSON files (`sample_api_responses.json`) or live HTTP requests via `POST /monitor`.
- **Persistent Data Storage:** Stores all telemetry records and active anomalies in an ACID-compliant SQLite database (WAL mode).
- **Interactive Web Dashboard:** Clean, user-friendly UI with real-time KPI metrics, active alerts triage, live telemetry streams, and traffic simulation.
- **Email Dispatch Support:** Modular notification service for dispatching alert summaries.


---
## 3. System Architecture

[![Architecture diagram of shah-abdul-mazid/intelligent-api-monitoring-alert-system](https://gitdiagram.com/shah-abdul-mazid/intelligent-api-monitoring-alert-system/diagram.png)](https://gitdiagram.com/shah-abdul-mazid/intelligent-api-monitoring-alert-system?utm_source=readme&utm_medium=picture)


### High-Level Architecture Flow
<p align="center">
  <img src="https://res.cloudinary.com/imnrqlsw/image/upload/v1792928107/FastAPI_Anomaly_Alert_Pipeline-2026-10-25-111555.png" alt="High-Level Architecture Diagram" width="100%" />
</p>

---

### Detailed Anomaly Alert & Monitoring Pipeline
<p align="center">
  <img src="https://res.cloudinary.com/imnrqlsw/image/upload/v1792927973/diagram.png" alt="Detailed Pipeline Diagram" width="100%" />
</p>


### Architecture Breakdown & Technical Details

1. **User / Interface Layer (Frontend Dashboard)**
   * **Client Interface**: Built with React, Vite, and Tailwind CSS, decorated with Lucide Icons for responsive telemetry visualization.
   * **Real-time Telemetry & Health Views**: Communicates asynchronously with the backend via REST endpoints to display live response latency charts, error rates, system uptime metrics, and historic alert logs.
   * **Dynamic Configuration**: Allows engineers to update alert thresholds, toggle notification channels, and manage target endpoint poll frequencies directly from the UI.

2. **API & Validation Layer (FastAPI Backend)**
   * **High-Performance Routing**: Powered by FastAPI and Uvicorn to process high-throughput monitoring signals with minimal overhead.
   * **Request Ingestion & Validation**: Utilizes Pydantic v2 data models for rigorous schema validation, type checking, and structured metric parsing before data hits internal processing pipelines.
   * **Target Endpoint Polling**: Executes synthetic HTTP/HTTPS health checks on registered target services on scheduled intervals or on-demand webhooks.

3. **Anomaly & AI Detection Engine (Dual-Core Processing)**
   * **Heuristic Anomaly Detection Rules**: Evaluates response codes, latency deviations beyond standard baseline thresholds, unexpected structural shifts in JSON payloads, and silent endpoint degradations.
   * **AI Root Cause Contextualization**: Integrates with the OpenAI API (`gpt-4o-mini`) to translate complex raw failure logs and dynamic telemetry patterns into human-readable actionable summaries and remediation recommendations.
   * **Zero-Dependency Fallback Engine**: Employs a local, rule-based heuristic fallback system to guarantee continuous anomaly analysis and notification formatting even during external API downtime or network isolation.

4. **Persistence & Notification Layer (Database & Alerting)**
   * **Optimized Local Persistence**: Uses SQLite backed by SQLAlchemy ORM with Write-Ahead Logging (WAL) enabled to support high-frequency concurrent writes and query reads without database locking.
   * **Multi-Channel Alert Dispatcher**: Routes generated context alerts through custom webhooks for Slack and Discord, as well as native Email notifications via SMTP.
  

---

## 4. Technologies Used

- **Backend:** Python, FastAPI, Uvicorn, Pydantic v2
- **Frontend:** React, Vite, Tailwind CSS, Lucide Icons
- **Database:** SQLite (SQLAlchemy ORM with Write-Ahead Logging)
- **AI Integration:** OpenAI API (GPT-4o-mini) + Zero-Dependency Heuristic Fallback Engine
- **Testing:** Pytest, HTTPX TestClient

---

## 5. Prerequisites

- **Python 3.10+** (Required)
- **Git** (Required)
- **Node.js 18+** *(Optional — only required if building frontend from source; the production UI is already pre-compiled)*

---



## 6. Installation

1. **Clone the repository:**
   ```bash
   git clone <repository-url>
   cd "Intelligent-API-Monitoring-Alert-System"
   ```

2. **Create and activate a virtual environment:**
   - **Windows (PowerShell):**
     ```powershell
     python -m venv backend\.venv
     .\backend\.venv\Scripts\Activate.ps1
     ```
   - **Linux / macOS:**
     ```bash
     python3 -m venv backend/.venv
     source backend/.venv/bin/activate
     ```

3. **Install backend dependencies:**
   ```bash
   pip install -r backend/requirements.txt
   ```

---

## 7. Configuration

Create an environment configuration file in `backend/.env` (or copy from `backend/.env.example`):

```ini
# Application Configuration
APP_NAME="Intelligent API Monitoring & Alert System"
DEBUG=False
PORT=<PORT>
HOST="127.0.0.1"

# Database
DATABASE_URL="sqlite:///./monitoring.db"

# Latency Thresholds (Milliseconds)
LATENCY_WARNING_MS=2000.0
LATENCY_CRITICAL_MS=4000.0

# Optional: OpenAI API Key
# If omitted or left empty, the system automatically runs the built-in Intelligent Fallback Engine
OPENAI_API_KEY=
OPENAI_MODEL="gpt-4o-mini"
```

> **Note:** The system runs completely out of the box with zero external API keys or third-party cloud services required.

---

## 8. Usage

### Start the Application (Single Command)

The backend server hosts both the REST API and the pre-built web dashboard simultaneously:

**On Windows (PowerShell):**
```powershell
$env:PYTHONPATH="backend"
python backend/run.py
```

**On Linux / macOS:**
```bash
PYTHONPATH=backend python backend/run.py
```

### Accessing the Application
Once the service starts, it prints the active local URL in your terminal.
- **Web Dashboard:** Open the printed local URL in your web browser.
- **Interactive API Documentation:** Append `/docs` to the local URL for the interactive Swagger interface.
- **System Health Check:** Append `/health` to verify service and database operational status.

---

## 9. Dashboard Walkthrough

1. Open the local application URL in your browser.
2. Click **"Load Sample Data"** in the top bar to ingest the test dataset (`sample_api_responses.json`).
3. View the **Active Alerts** list displaying anomaly tags, severity levels (`CRITICAL`, `WARNING`), and AI-generated root causes.
4. Click **"Simulate Telemetry"** to fire custom API test payloads and watch real-time evaluation.
5. Use the **"Mark Resolved"** action to triage alerts and observe the live health score calculation.

---

## 10. Project Structure

```text
Intelligent API Monitoring & Alert System/
├── backend/
│   ├── src/
│   │   ├── api/             # REST route handlers (/monitor, /alerts, /health)
│   │   ├── core/            # Configuration settings and structured logger
│   │   ├── database/        # SQLAlchemy database models and session setup
│   │   ├── schemas/         # Pydantic v2 validation models
│   │   └── services/        # Anomaly detection, AI generation, and email service
│   ├── tests/               # Pytest unit and integration test suite
│   ├── requirements.txt     # Python backend dependencies
│   ├── run.py               # Main application runner
│   └── .env.example         # Template environment variables
├── frontend/
│   ├── src/                 # React source code (components, hooks, styles)
│   ├── dist/                # Pre-built static bundle served by backend
│   └── package.json         # Frontend configuration
├── sample_api_responses.json# Sample API telemetry batch input
├── AI_Prompts.docx          # Documented AI usage, design steps, and prompts
├── .gitignore
└── README.md
```

---

## 11. API Documentation

| Method | Endpoint | Description |
|---|---|---|
| `POST` | `/monitor` | Ingest single or batch telemetry payloads |
| `POST` | `/monitor/load-sample` | Ingest dataset from `sample_api_responses.json` |
| `GET` | `/monitor/records` | Fetch paginated historical telemetry |
| `GET` | `/alerts` | Fetch active anomalies (`?status=ACTIVE` or `ALL`) |
| `GET` | `/alerts/stats` | Real-time system health metrics and counts |
| `POST` | `/alerts/{id}/resolve` | Mark an anomaly as resolved |
| `POST` | `/alerts/{id}/notify-email`| Dispatch email notification for an alert |
| `DELETE`| `/alerts/clear` | Purge telemetry and alerts for testing |
| `GET` | `/health` | System and database health status |

### Ingestion Payload Example (`POST /monitor`)

```json
{
  "api_name": "AppointmentAPI",
  "endpoint": "/v1/appointments/today",
  "status_code": 500,
  "response_time_ms": 1420.5,
  "records_returned": 0
}
```

### Generated Alert Example

```json
{
  "alert": "AppointmentAPI failed with status 500 and returned 0 records. Possible outage or data issue.",
  "root_cause": "Internal server error causing zero records returned from upstream appointment service.",
  "recommendation": "Check microservice pod health and database connection pool."
}
```

---

## 12. Testing

Execute the automated test suite using `pytest`:

**Windows (PowerShell):**
```powershell
$env:PYTHONPATH="backend"
pytest backend/tests
```

**Linux / macOS:**
```bash
PYTHONPATH=backend pytest backend/tests
```

**Test Coverage:**
- `test_anomaly_detector.py`: Validates deterministic rules (status codes, latency thresholds, zero records).
- `test_api.py`: Validates Pydantic schema validation, endpoints, and batch ingestion.

---

## 13. Contributing

1. Fork the repository.
2. Create a feature branch: `git checkout -b feature/your-feature-name`.
3. Commit your changes: `git commit -m "feat: add your feature"`.
4. Push to the branch: `git push origin feature/your-feature-name`.
5. Open a Pull Request.

---

## 14. License

This project is licensed under the **MIT License**.

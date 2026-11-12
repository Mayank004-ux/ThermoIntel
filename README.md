# ThermoIntel 🛰️🔥
### AI-Assisted Industrial Thermal Intelligence & Anomaly Monitoring Platform

[![FastAPI](https://img.shields.io/badge/FastAPI-0.141.1-009688?style=flat&logo=fastapi&logoColor=white)](https://fastapi.tiangolo.com)
[![React](https://img.shields.io/badge/React-19.2-61DAFB?style=flat&logo=react&logoColor=black)](https://react.dev/)
[![Vite](https://img.shields.io/badge/Vite-8.2-646C99?style=flat&logo=vite&logoColor=white)](https://vitejs.dev/)
[![TypeScript](https://img.shields.io/badge/TypeScript-6.0-3178C6?style=flat&logo=typescript&logoColor=white)](https://www.typescriptlang.org/)
[![XGBoost](https://img.shields.io/badge/XGBoost-Classifier-EB5424?style=flat)](https://xgboost.readthedocs.io/)
[![Leaflet](https://img.shields.io/badge/Leaflet-1.9-199900?style=flat&logo=leaflet&logoColor=white)](https://leafletjs.com/)
[![License](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)

---

## 📌 Overview

**ThermoIntel** is an end-to-end industrial thermal intelligence and monitoring platform built for **Smart India Hackathon (SIH 26162)**. The platform ingests near-real-time satellite thermal anomaly feeds from **NASA FIRMS** (VIIRS / MODIS) and combines geospatial indexing, spatial polygon intersections with over 39,000 industrial zones across India, and machine learning classification to distinguish routine industrial operations from hazardous thermal anomalies and industrial fire risks.

---

## 🚀 Key Features

- **Near-Real-Time Satellite Ingestion**: Continuously polls NASA FIRMS API for thermal anomaly detections across the Indian subcontinent.
- **Geospatial & Spatial Indexing**:
  - Spatial intersection against 39,000+ industrial land-use polygons in India.
  - Spatial point-in-polygon and buffer queries using Shapely and grid-indexed spatial boundaries.
  - Country boundary clipping via GeoJSON to filter out international noise.
- **Hybrid Intelligence Scoring & ML Classification**:
  - **XGBoost & Scikit-Learn Classifier**: Classifies detections into *Potential Industrial Thermal Source*, *Thermal Anomaly*, or *Industrial Fire Candidate*.
  - Multi-factor risk engine computing an **Intelligence Score** (0–100) based on Fire Radiative Power (FRP), brightness temperature, confidence, persistence, and land-use context.
- **Persistent Thermal Source Detection**:
  - Tracks thermal recurring baselines to identify stationary industrial heat emitters (e.g., thermal power plants, steel furnaces, refineries, flare stacks, cement kilns).
- **Dual-View Operational Interface**:
  - **3D Tactical Globe**: Interactive Three.js globe visualizing global and regional thermal hotspots.
  - **Full-Bleed 2D High-Resolution Map**: Interactive Leaflet workstation powered by Esri Dark Gray / CARTO basemaps with dynamic hotspot markers scaled by FRP.
- **Comprehensive Analytics HUD**:
  - 14-day temporal trend charts.
  - Severity level filters (High, Medium, Low) and keyword search.
  - Real-time telemetry cards with average intelligence score and active source counts.
- **Industrial Proximity Inspector**:
  - Detailed side drawer showing distance to closest industrial parks, coordinates, satellite sensor, brightness, and raw telemetry.

---

## 🏗️ Architecture

```mermaid
graph TD
    NASA[NASA FIRMS Satellite Feed] -->|Thermal Detections| Backend[FastAPI Backend - Port 8001]
    OSM[Industrial Land-Use GeoJSON 39k+ Polygons] -->|Spatial Index| Backend
    Backend -->|Feature Extraction| ML[XGBoost & Scikit-Learn Model]
    Backend -->|Historical Cache & Baselines| DB[(SQLite: thermointel.db)]
    Backend -->|REST API JSON| Frontend[React 19 + TypeScript + Vite]
    Frontend --> Map[Leaflet Map with Dark Basemap]
    Frontend --> Globe[Three.js 3D Tactical Globe]
    Frontend --> HUD[Live Analytics HUD & Filter Panels]
```

---

## 🗂️ Project Structure

```text
thermointel/
├── .env.example                # Sample frontend environment file
├── .gitignore                  # Git ignore rules (protects API keys, DBs, models)
├── index.html                  # HTML entry point
├── package.json                # Frontend dependencies and npm scripts
├── tsconfig.json               # TypeScript configuration
├── vite.config.ts              # Vite configuration
│
├── src/                        # Frontend source code
│   ├── App.tsx                 # Main workstation UI, HUD, Leaflet map, and analytics
│   ├── Globe3D.tsx             # Interactive Three.js 3D globe component
│   ├── app.css                 # Dark-mode styling, glassmorphism, and HUD styles
│   ├── main.tsx                # React DOM root entry
│   └── index.css               # Global base styles
│
└── backend/                    # FastAPI backend
    ├── .env.example            # Sample backend environment file
    ├── requirements.txt        # Python dependencies (FastAPI, Shapely, XGBoost, etc.)
    ├── requirements-dev.txt    # Additional development dependencies (scikit-learn)
    └── app/
        ├── main.py             # FastAPI entrypoint, spatial filters, and REST endpoints
        ├── db.py               # SQLite database layer for persistence and baselines
        ├── ml_features.py      # Feature engineering pipeline for ML model
        ├── train_model.py      # Script to train/retrain the XGBoost classifier
        ├── india.geojson       # India national boundary for spatial clipping
        ├── industrial_landuse.geojson  # 39,280 industrial land-use polygons
        └── models/
            ├── thermal_classifier.json   # Native XGBoost booster model
            ├── thermal_classifier.joblib # Scikit-Learn joblib classifier model
            ├── label_encoder.joblib      # Label encoder for target classes
            └── classes.json              # Class definitions
```

---

## ⚙️ Environment Configuration

Environment secrets are kept strictly isolated and are ignored by version control.

### 1. Frontend `.env` (Root Directory)

Create a `.env` file in the root directory (or copy from `.env.example`):

```env
# Backend API URL (default: http://127.0.0.1:8001)
VITE_API_URL=http://127.0.0.1:8001

# Optional: CARTO Basemap key (defaults to Esri World Dark Gray Canvas if omitted)
# VITE_CARTO_API_KEY=your_carto_api_key_here
```

### 2. Backend `.env` (`backend/` Directory)

Create a `.env` file in the `backend/` directory (or copy from `backend/.env.example`):

```env
# Required: NASA FIRMS Map Key
# Get a free key at: https://firms.modaps.eosdis.nasa.gov/api/map_key
FIRMS_MAP_KEY=your_nasa_firms_map_key_here
```

---

## 🛠️ Step-by-Step Guide to Run the Project

### Prerequisites

Make sure the following tools are installed on your machine:
- **Node.js**: `v18+` (v20+ recommended)
- **Python**: `v3.10+`
- **NASA FIRMS MAP KEY**: Free registration from [NASA FIRMS API Key Portal](https://firms.modaps.eosdis.nasa.gov/api/map_key)

---

### Step 1: Set Up & Start the Backend

1. **Open a terminal** and navigate to the `backend` directory:
   ```bash
   cd backend
   ```

2. **Create and activate a Python virtual environment**:
   * **Windows (PowerShell)**:
     ```powershell
     python -m venv .venv
     .\.venv\Scripts\Activate.ps1
     ```
   * **Linux / macOS**:
     ```bash
     python3 -m venv .venv
     source .venv/bin/activate
     ```

3. **Install the backend dependencies**:
   ```bash
   pip install -r requirements.txt
   pip install scikit-learn
   ```

4. **Create the environment file**:
   ```bash
   # Windows PowerShell
   Copy-Item .env.example .env

   # Linux / macOS
   cp .env.example .env
   ```
   Open `backend/.env` and paste your NASA FIRMS key:
   ```env
   FIRMS_MAP_KEY=your_actual_key_here
   ```

5. **Start the FastAPI backend server**:
   ```bash
   uvicorn app.main:app --reload --port 8001
   ```
   * The backend API will be running at: **`http://127.0.0.1:8001`**
   * Interactive Swagger documentation: **`http://127.0.0.1:8001/docs`**
   * Health check endpoint: **`http://127.0.0.1:8001/api/health`**

---

### Step 2: Set Up & Start the Frontend

1. **Open a second terminal** and navigate to the project root:
   ```bash
   cd Thermointel
   ```

2. **Install Node dependencies**:
   ```bash
   npm install
   ```

3. **Create the frontend environment file** (optional, defaults connect to `http://127.0.0.1:8001`):
   ```bash
   # Windows PowerShell
   Copy-Item .env.example .env

   # Linux / macOS
   cp .env.example .env
   ```

4. **Start the Vite development server**:
   ```bash
   npm run dev
   ```
   * Open your browser and navigate to: **`http://localhost:5173`**

---

## 📡 API Reference

The FastAPI backend provides the following REST API endpoints:

| Method | Endpoint | Description |
|---|---|---|
| `GET` | `/api/health` | Service health status, database connection, and layer verification |
| `GET` | `/api/firms/events` | Retrieves processed thermal events with ML classification, scores, and severity |
| `GET` | `/api/firms/clusters` | Spatial clustering of nearby thermal anomalies within 5 km |
| `GET` | `/api/firms/context` | Proximity check against nearest industrial polygons (`latitude`, `longitude`, `radius_km`) |
| `GET` | `/api/firms/persistent` | List of recurring thermal sources active across multiple days |
| `GET` | `/api/firms/trends` | 14-day aggregate event volume and intelligence score trends |
| `GET` | `/docs` | Interactive OpenAPI / Swagger UI documentation |

---

## 🧪 Testing & Validation

Run quality and validation checks across both frontend and backend:

```bash
# Frontend Typecheck & Production Build
npm run build

# Frontend Linter
npm run lint

# Backend Model Training & Validation (Optional)
cd backend
python app/train_model.py
```

---

## 🔒 Security & Privacy

- All `.env` files, API keys, and local SQLite databases are strictly excluded via [`.gitignore`](file:///.gitignore).
- Sensitive environment configurations are loaded safely via `python-dotenv` with automatic path fallback.
- Never commit active API keys or database instances to public version control.

---

## 📄 License

This project is licensed under the MIT License.

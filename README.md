# Real Time Flood Detection Model

A full-stack flood risk assessment system that fetches real-time ERA5 weather data from the Copernicus Climate Data Store (CDS) and runs AI-powered analysis to predict flood risk for any coordinates worldwide.

**Live Demo:** [hydroalert-ivory.vercel.app](https://hydroalert-ivory.vercel.app)

---

## How It Works

1. User enters latitude/longitude and an analysis window (1–30 days)
2. The FastAPI backend fetches ERA5 reanalysis data (precipitation, soil moisture, etc.) via the CDS API
3. The `FloodPredictor` model processes the data and returns a structured risk assessment
4. The React frontend renders risk scores, weather charts, severity breakdowns, and time-window forecasts

---

## Tech Stack

| Layer | Technology |
|---|---|
| Frontend | React 18, TypeScript, Vite, Tailwind CSS, Recharts, React-Leaflet |
| Backend | Python, FastAPI, Uvicorn |
| Data | ERA5 via `cdsapi` (Copernicus CDS), `xarray`, `numpy` |
| Deploy | Vercel (frontend) |

---

## Project Structure

```
Code_base/
├── flood_model/
│   ├── api_server.py        # FastAPI app — exposes /api/predict
│   ├── flood_predictor.py   # Core prediction logic, ERA5 data fetching
│   ├── finetune_model.py    # Model fine-tuning utilities
│   ├── flood_with_ollama.py # Ollama LLM integration (experimental)
│   └── panda.py             # Data processing helpers
└── frontend/
    ├── src/
    │   ├── pages/Dashboard.tsx
    │   ├── components/
    │   │   ├── LocationInput.tsx
    │   │   ├── RiskOverview.tsx
    │   │   ├── WeatherCharts.tsx
    │   │   ├── SeverityBreakdown.tsx
    │   │   └── TimeWindowForecast.tsx
    │   └── services/api.ts
    └── vite.config.ts
```

---

## Getting Started

### Prerequisites

- Python 3.9+
- Node.js 18+
- A [Copernicus CDS account](https://cds.climate.copernicus.eu/) with API key

### Backend

```bash
cd flood_model

# Install dependencies
pip install fastapi uvicorn cdsapi xarray numpy pydantic

# Configure CDS API — create ~/.cdsapirc with:
# url: https://cds.climate.copernicus.eu/api
# key: <your-api-key>

# Or set the environment variable:
export CDS_API_KEY=<your-api-key>

# Start the server
python api_server.py
# → http://localhost:8000
# → Docs: http://localhost:8000/docs
```

### Frontend

```bash
cd frontend
npm install
npm run dev
# → http://localhost:5173
```

---

## API

### `POST /api/predict`

```json
{
  "lat": 28.6,
  "lon": 77.2,
  "days": 7
}
```

Returns a `FloodPredictionResult` with:
- `risk_assessment` — overall risk level and score
- `weather_data` — time-series precipitation, temperature, soil moisture
- `severity_breakdown` — per-factor risk scores
- `time_window_forecast` — day-by-day flood probability

### `GET /health`

Returns API and CDS predictor status.

---

## Environment Variables

| Variable | Description |
|---|---|
| `CDS_API_KEY` | Copernicus CDS API key (alternative to `~/.cdsapirc`) |

---

## Development Notes

- ERA5 data has a ~7-day delay; the backend automatically adjusts the fetch window
- Enable **Use Mock Data** in the UI to preview the interface without a CDS API key
- The frontend build runs `tsc && vite build` — keep TypeScript strict

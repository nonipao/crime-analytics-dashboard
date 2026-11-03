# Punjab Crime Dashboard

![React](https://img.shields.io/badge/React-19-61DAFB?logo=react&logoColor=black)
![Vite](https://img.shields.io/badge/Vite-8-646CFF?logo=vite&logoColor=white)
![Tailwind CSS](https://img.shields.io/badge/Tailwind_CSS-4-06B6D4?logo=tailwindcss&logoColor=white)
![FastAPI](https://img.shields.io/badge/FastAPI-009688?logo=fastapi&logoColor=white)

A simple dashboard to explore district-wise crime data for Punjab, India. Upload a CSV, see where cases are highest, and view a basic forecast for each district.

## What it does

- **Upload data:** drag and drop a CSV file with crime records.
- **Overview:** total cases, latest-year cases, the highest-risk district and the most common crime type.
- **Map:** Punjab district map coloured by risk level (Low, Medium, High, Critical).
- **Forecasts:** a simple trend forecast for each district, shown as a chart with a range around it.
- **Risk table:** all districts in one table that you can sort and search.
- **Crime categories:** breakdown of cases by crime type.

## Tech stack

| Part | Tools |
|---|---|
| Frontend | React, Vite, Tailwind CSS, Recharts, Leaflet |
| Backend | Python, FastAPI, Pandas |
| Forecasting | scikit-learn (Ridge Regression) |

## Project structure

```
PunjabCrimeDashboard/
├── backend/
│   ├── requirements.txt
│   └── app/
│       ├── main.py          # FastAPI app and routes
│       ├── ml/              # Forecasting and risk levels
│       └── services/        # CSV reading and calculations
└── frontend/
    ├── index.html
    ├── package.json
    └── src/
        ├── api/             # Calls to the backend
        ├── components/      # Sidebar, map, charts, tables
        ├── data/            # Punjab district map file (GeoJSON)
        ├── hooks/
        ├── utils/
        ├── App.jsx
        └── main.jsx
```

## API endpoints

| Method | Endpoint | What it does |
|---|---|---|
| `POST` | `/api/upload` | Upload a CSV file |
| `GET` | `/api/summary` | Overall numbers for the dashboard |
| `GET` | `/api/risk-assessment` | Risk level for every district |
| `GET` | `/api/forecast?district={name}&years={n}` | Forecast for one district |

## CSV format

The file needs exactly these four columns, in lower case:

| Column | Meaning | Example |
|---|---|---|
| `year` | Year of the records | `2023` |
| `district` | Punjab district name | `Ludhiana` |
| `crime_category` | Type of crime | `Theft` |
| `cases` | Number of cases | `412` |

Example:

```csv
year,district,crime_category,cases
2022,Ludhiana,Theft,398
2022,Ludhiana,Assault,121
2023,Ludhiana,Theft,412
2023,Amritsar,Theft,287
```

Tips:

- Each district needs at least 3 years of data to get a forecast.
- District names are not case-sensitive. Some common alternate names also work (for example SAS Nagar and Mohali).

## How to run it locally

You need Git, Python 3.10+ and Node.js 20+.

**1. Clone the project**

```bash
git clone <your-repository-url>
cd PunjabCrimeDashboard
```

**2. Start the backend**

```bash
cd backend
python -m venv .venv

# Windows (PowerShell)
.venv\Scripts\Activate.ps1
# Linux / macOS
source .venv/bin/activate

pip install -r requirements.txt
uvicorn app.main:app --reload
```

The API runs at `http://localhost:8000`, and the docs are at `http://localhost:8000/docs`.

**3. Start the frontend** (in a second terminal)

```bash
cd frontend
npm install
npm run dev
```

Open `http://localhost:5173` in your browser.

> The frontend expects the backend on port 8000. If you use a different port, create `frontend/.env` with `VITE_API_URL=http://localhost:<port>`.

## Common problems

| Problem | Fix |
|---|---|
| "Cannot reach the API" message | Make sure the backend is running on port 8000. |
| Map shows no districts | Check that `frontend/src/data/punjab-districts.json` exists. |
| Empty results after restarting | The backend keeps data in memory, so upload the CSV again. |

## Notes

- The forecasts are simple trend estimates based on a few years of data, so treat them as a rough guide.
- Recorded crime depends on how much is reported, so it is not the same as total crime.
- This is for district-level analysis only. It should not be used to profile any person or community.

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

* Each district needs at least 3 years of data to get a forecast.
* District names are not case-sensitive. Some common alternate names also work (for example SAS Nagar and Mohali).

## How to run the project locally

To run this project, you will need two separate terminal windows: one for the Python backend and one for the Node.js frontend. You also need Git, Python 3.10+ and Node.js 20+.

### 1. Clone the project

```bash
git clone <your-repository-url>
cd PunjabCrimeDashboard
```

### 2. Start the backend (Terminal 1)

Open a terminal, go to the cloned project root, and run these commands to set up and start the FastAPI server:

```powershell
# Navigate to the backend directory
cd backend

# Create a virtual environment
python -m venv .venv

# Activate the virtual environment (Windows PowerShell)
.\.venv\Scripts\Activate.ps1
# Linux / macOS
# source .venv/bin/activate

# Install required Python packages
pip install -r requirements.txt

# Start the backend server
uvicorn app.main:app --reload
```

*The API will run at `http://127.0.0.1:8000`, and the docs are at `http://127.0.0.1:8000/docs`.*

### 3. Start the frontend (Terminal 2)

Leave the backend terminal running. Open a **new** terminal window, go to the project root, and run:

```powershell
# Navigate to the frontend directory
cd frontend

# Install Node modules
npm install

# Start the development server
npm run dev
```

*The frontend terminal will show a local URL (for example `http://localhost:5173`). Open this link in your browser to view the dashboard.*

> The frontend expects the backend on port 8000. If you use a different port, create `frontend/.env` with `VITE_API_URL=http://localhost:<port>`.

### Troubleshooting note for Windows users

If you get a `metadata-generation-failed` or `pydantic-core` Rust error while installing the backend requirements on Python 3.13+, force pip to install the latest compatible versions by running:

```powershell
pip install fastapi uvicorn pandas pydantic python-multipart scikit-learn sqlalchemy slowapi fpdf2 --upgrade --no-cache-dir
```

## Common problems

| Problem | Fix |
|---|---|
| "Cannot reach the API" message | Make sure the backend is running on port 8000. |
| Map shows no districts | Check that `frontend/src/data/punjab-districts.json` exists. |
| Empty results after restarting | The backend keeps data in memory, so upload the CSV again. |

## Notes

* The forecasts are simple trend estimates based on a few years of data, so treat them as a rough guide.
* Recorded crime depends on how much is reported, so it is not the same as total crime.
* This is for district-level analysis only. It should not be used to profile any person or community.

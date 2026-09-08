# Unemployment Analysis: India

An interactive exploratory data analysis (EDA) of India's unemployment rates — regional and temporal trends, the COVID-19 pandemic's impact, correlation analysis, and an embedded Jupyter Notebook walkthrough. Dataset source: CMIE (Centre for Monitoring Indian Economy), "Unemployment in India".

The project has two parts:

1. **Interactive web app** (`src/`) — a React + TypeScript + Vite dashboard with KPI stats, a time-series chart, top-states chart, pre/post-COVID comparison, a regional (urban vs. rural) breakdown, a correlation heatmap, a raw data inspector, and an embedded, downloadable Jupyter Notebook viewer. Everything runs client-side in the browser — **no API key and no backend required**.
2. **Standalone dataset + notebook** (`public/Unemployment_in_India.csv`, `public/unemployment_analysis_india.ipynb`) — the same data and analysis as plain files you can open directly in Jupyter/VS Code outside the web app.

> **Note on the original export:** this project was originally generated with Google AI Studio. That template ships with an Express server, a Gemini API key requirement, and other scaffolding this app never actually uses. All of that has been removed below — this is a plain static site with no external API calls.

---

## Project structure

```
unemployment-analysis-india/
├── src/
│   ├── App.tsx                       # Main app shell / tab routing
│   ├── main.tsx                      # React entry point
│   ├── index.css                     # Tailwind CSS entry
│   ├── types.ts                      # Shared TypeScript types
│   ├── data/unemploymentData.ts      # Source dataset + derived stats/aggregates (source of truth)
│   ├── data/notebookData.ts          # Notebook cell content (source of truth for the .ipynb)
│   ├── utils/downloadHelpers.ts      # "Download CSV / Download notebook" logic
│   └── components/                   # Dashboard views (KPIs, charts, heatmap, notebook viewer, etc.)
├── scripts/
│   └── generate_downloads.ts         # Regenerates the two files below from src/data/*
├── public/
│   ├── Unemployment_in_India.csv     # Pre-generated dataset (served as a static file)
│   └── unemployment_analysis_india.ipynb  # Pre-generated notebook (served as a static file)
├── requirements.txt                  # Python dependencies for the notebook
├── index.html
├── package.json
├── vite.config.ts
└── tsconfig.json
```

**How the data flows:** `src/data/unemploymentData.ts` and `src/data/notebookData.ts` are the actual source of truth — they define the dataset and generate the CSV/notebook content in-browser (via `Blob` download) when you click "Download" in the app. The two files under `public/` are a pre-generated, ready-to-open copy of the same data. If you ever edit the source data files, regenerate the copies under `public/` with `npm run generate:downloads` (see below) so they stay in sync — the original project had no way to do this, since the script existed but wasn't wired into `package.json`.

---

## Part 1 — Run the web app in VS Code

### Prerequisites
- [Node.js](https://nodejs.org/) **v18 or later** (v20+ recommended)
- npm (comes bundled with Node.js)
- VS Code with the standard TypeScript support (built in — no extra extensions required)

### Steps

1. **Open the folder in VS Code**
   `File → Open Folder…` and select `unemployment-analysis-india/`.

2. **Open a terminal in VS Code**
   `Terminal → New Terminal` (or `` Ctrl+` ``).

3. **Install dependencies**
   ```bash
   npm install
   ```

4. **Start the dev server**
   ```bash
   npm run dev
   ```
   Vite will print a local URL, typically:
   ```
   ➜  Local:   http://localhost:3000/
   ```
   Open it in your browser (or `Ctrl+Click` the link in the VS Code terminal). Edits to any file in `src/` hot-reload instantly.

5. **Type-check the code (optional but recommended)**
   ```bash
   npm run lint
   ```

6. **Regenerate the CSV/notebook if you change the dataset (optional)**
   ```bash
   npm run generate:downloads
   ```
   This overwrites `public/Unemployment_in_India.csv` and `public/unemployment_analysis_india.ipynb` from the current contents of `src/data/unemploymentData.ts` / `src/data/notebookData.ts`.

7. **Build for production (optional)**
   ```bash
   npm run build
   npm run preview   # serve the production build locally to sanity-check it
   ```
   The output goes to `dist/`, which you can deploy to any static host (Vercel, Netlify, GitHub Pages, etc.).

That's it — **no `.env` file, no API key, and no server process are needed** to run this app.

---

## Part 2 — Run the notebook in VS Code

### Prerequisites
- Python 3.9+
- VS Code with the **Python** and **Jupyter** extensions

### Steps

1. **Create and activate a virtual environment**

   macOS/Linux:
   ```bash
   python3 -m venv .venv
   source .venv/bin/activate
   ```
   Windows (PowerShell):
   ```powershell
   python -m venv .venv
   .venv\Scripts\Activate.ps1
   ```

2. **Install dependencies**
   ```bash
   pip install -r requirements.txt
   ```

3. **Select the interpreter in VS Code**
   Command Palette (`Ctrl+Shift+P` / `Cmd+Shift+P`) → `Python: Select Interpreter` → choose the `.venv` you just created.

4. **Open and run the notebook**
   Open `public/unemployment_analysis_india.ipynb` in VS Code, pick the same `.venv` kernel in the top-right kernel selector, and run all cells (`Run All`).

   ⚠️ The notebook loads the data with `pd.read_csv('Unemployment_in_India.csv')` — a path **relative to the notebook's own folder**. Since the CSV lives right next to it in `public/`, this works as long as you keep both files together (which they already are). If you move the notebook elsewhere, move the CSV with it or update the path.

---

## What was fixed from the original AI Studio export

- Removed unused dependencies that were never imported anywhere in the code: `@google/genai`, `express`, `dotenv`, `@types/express`, `esbuild`, `autoprefixer`, `motion` (these came from AI Studio's generic template and pulled in extra packages for nothing).
- Removed `.env.example` / `metadata.json` / the `public/assets/aistudio` folder — these referenced a `GEMINI_API_KEY` and `APP_URL` that the app **never actually calls**, since it makes no Gemini/API requests at all.
- Removed `bun.lock` and a stale `package-lock.json` so the project uses a single, consistent package manager (npm) and regenerated a clean lockfile.
- **Wired up `scripts/generate_downloads.ts`**: it existed in the original export and correctly regenerates the CSV/notebook from the source data, but no npm script pointed to it, so there was no documented way to run it. Added `npm run generate:downloads`, kept `tsx` as a devDependency to run it (verified it reproduces the checked-in `public/` files byte-for-byte).
- Simplified `vite.config.ts`: removed AI-Studio-specific HMR/file-watch environment flags that had no effect outside AI Studio, and set a fixed dev port with `open: true`.
- Fixed `package.json`: gave the project a real name/version and a `dev` script that doesn't depend on AI Studio's host-binding flags.
- Verified the whole project type-checks (`tsc --noEmit`) and builds (`vite build`) cleanly, and verified the notebook executes end-to-end with no errors (`jupyter nbconvert --execute`).
- Added `requirements.txt` for the notebook (previously undocumented) and this README with real run instructions for VS Code, replacing the generic AI Studio boilerplate README.

## Tech stack

- **Frontend:** React 19, TypeScript, Vite 6, Tailwind CSS 4, Recharts (charts), lucide-react (icons)
- **Notebook/Data:** Python 3, pandas, NumPy, Matplotlib, Seaborn, Jupyter
- **Dataset:** Unemployment in India (CMIE), by state, area (urban/rural), and month

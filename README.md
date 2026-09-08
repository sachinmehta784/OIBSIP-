# Sales Prediction with Regression

An interactive machine-learning studio that predicts product sales from advertising spend across TV, Radio, and Newspaper channels, using the classic "Advertising" dataset (200 records).

The project has two parts:

1. **Interactive web app** (`src/`) — a React + TypeScript + Vite dashboard covering the full regression workflow: EDA and dataset upload, visualizations (scatter plots, correlation heatmap), a model-training studio (Linear Regression, Polynomial Regression, Random Forest), residual analysis, a channel-impact interpretation simulator, and an in-app Jupyter Notebook / Python script viewer you can copy or download. Everything runs client-side in the browser — **no API key and no backend required**.
2. **`Advertising.csv`** — the raw dataset, kept at the project root so it sits next to the Python script/notebook you download from the app (see below for why that matters).

> **Note on the original export:** this project was originally generated with Google AI Studio. That template ships with an Express server, a Gemini API key requirement, and other scaffolding this app never actually uses. All of that has been removed below — this is a plain static site with no external API calls.

---

## Project structure

```
sales-prediction-with-regression/
├── src/
│   ├── App.tsx                        # Main app shell / tab routing
│   ├── main.tsx                       # React entry point
│   ├── index.css                      # Tailwind CSS entry
│   ├── types/sales.ts                 # Shared TypeScript types
│   ├── data_advertising.json          # The 200-row Advertising dataset (bundled into the app)
│   ├── data/advertisingData.ts        # Default data, CSV parse/export helpers
│   ├── data/notebookContent.ts        # Generates the downloadable .ipynb / .py content
│   ├── utils/mlEngine.ts              # Regression models + residual analysis logic
│   └── components/                    # Dashboard views (EDA, visualizations, training, etc.)
├── Advertising.csv                    # Raw dataset (used by the downloaded Python script/notebook)
├── requirements.txt                   # Python dependencies
├── index.html
├── package.json
├── vite.config.ts
└── tsconfig.json
```

**How the data flows:** the web app's real data source is `src/data_advertising.json`, bundled directly into the app at build time — the app never fetches a CSV over the network. The "Download CSV / Download notebook / Download script" buttons in the app generate those files on the fly, in-browser, from `src/data/advertisingData.ts` and `src/data/notebookContent.ts`.

`Advertising.csv` at the project root is a separate convenience copy for the Python side: the generated Python script and notebook first try to load a local `Advertising.csv` and only fall back to downloading it from a public GitHub URL if it's missing. Keeping this file next to the script/notebook means they'll run **offline**, with no network call needed.

---

## Part 1 — Run the web app in VS Code

### Prerequisites
- [Node.js](https://nodejs.org/) **v18 or later** (v20+ recommended)
- npm (comes bundled with Node.js)
- VS Code with the standard TypeScript support (built in — no extra extensions required)

### Steps

1. **Open the folder in VS Code**
   `File → Open Folder…` and select `sales-prediction-with-regression/`.

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

6. **Build for production (optional)**
   ```bash
   npm run build
   npm run preview   # serve the production build locally to sanity-check it
   ```
   The output goes to `dist/`, which you can deploy to any static host (Vercel, Netlify, GitHub Pages, etc.).

That's it — **no `.env` file, no API key, and no server process are needed** to run this app.

---

## Part 2 — Run the Python analysis in VS Code

Use the app's "Notebook" tab to download the ready-made `.ipynb` or `.py`, or just use `Advertising.csv` at the project root with your own script.

### Prerequisites
- Python 3.9+
- VS Code with the **Python** extension (and **Jupyter** extension if you want to run a notebook)

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

4. **Download and run the script/notebook from the app**
   In the running web app, open the **Notebook** tab and click "Download Python script" or "Download notebook". Save it into this project folder (next to `Advertising.csv`) and run it:
   ```bash
   python your_downloaded_script.py
   ```
   or open the downloaded `.ipynb` in VS Code, pick the `.venv` kernel, and `Run All`.

   Both were verified end-to-end: the script prints data overview, correlation, a train/test split, fitted regression coefficients, and model comparison metrics (Linear Regression, Polynomial Regression, Random Forest) with R² scores; the notebook executes cleanly the same way.

---

## What was fixed from the original AI Studio export

- Removed unused dependencies that were never imported anywhere in the code: `@google/genai`, `express`, `dotenv`, `@types/express`, `esbuild`, `autoprefixer`, `motion`, `tsx` (these came from AI Studio's generic template and pulled in extra packages for nothing — install went from 253 packages down to well under 100).
- Removed `.env.example` / `metadata.json` / the `public/assets/aistudio` folder — these referenced a `GEMINI_API_KEY` and `APP_URL` that the app **never actually calls**, since it makes no Gemini/API requests at all.
- Removed a **duplicate copy** of the dataset: the project shipped `Advertising.csv` at the root *and* an identical copy at `public/Advertising.csv`, but the app never fetches from `public/` at runtime — it uses the bundled `src/data_advertising.json` instead. Kept a single copy at the project root, which is the one the downloaded Python script/notebook actually look for.
- Removed `bun.lock` and a stale `package-lock.json` so the project uses a single, consistent package manager (npm) and regenerated a clean lockfile.
- Simplified `vite.config.ts`: removed AI-Studio-specific HMR/file-watch environment flags that had no effect outside AI Studio, and set a fixed dev port with `open: true`.
- Fixed `package.json`: gave the project a real name/version and a `dev` script that doesn't depend on AI Studio's host-binding flags.
- Verified the whole project type-checks (`tsc --noEmit`) and builds (`vite build`) cleanly.
- Verified the generated Python script and notebook actually run end-to-end (via `tsx` extraction + `python` and `jupyter nbconvert --execute`) with no errors, and that the numbers they produce look sane (R² of ~0.90–0.99 across the three models).
- Added `requirements.txt` for the Python side (previously undocumented) and this README with real run instructions for VS Code, replacing the generic AI Studio boilerplate README.

## Tech stack

- **Frontend:** React 19, TypeScript, Vite 6, Tailwind CSS 4, Recharts (charts), lucide-react (icons)
- **ML/Data:** Python 3, scikit-learn, pandas, NumPy, Matplotlib, Seaborn
- **Dataset:** Advertising dataset (TV, Radio, Newspaper spend vs. Sales), 200 records

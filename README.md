# Iris Species Classifier

An interactive machine-learning project that classifies Iris flowers (**Setosa**, **Versicolor**, **Virginica**) using the classic Fisher's Iris dataset.

The project has two parts:

1. **Interactive web app** (`src/`) — a React + TypeScript + Vite dashboard that walks through the full ML workflow: EDA, visualizations (pairplot/box plots), feature selection, model evaluation (Logistic Regression, KNN, Decision Tree, Random Forest), and a live predictor. Everything runs in the browser — there is **no API key and no backend required**.
2. **Python analysis** (`iris_classification.py` and `iris_flower_classification.ipynb`) — the actual scikit-learn code that trains and evaluates the models, used as the source of truth for the numbers shown in the web app.

> **Note on the original export:** this project was originally generated with Google AI Studio. That template ships with an Express server, a Gemini API key requirement, and other scaffolding that this app never actually uses. All of that has been removed below — this app is a plain static site with no external API calls.

---

## Project structure

```
iris-species-classifier/
├── src/
│   ├── App.tsx                  # Main app shell / tab routing
│   ├── main.tsx                 # React entry point
│   ├── index.css                # Tailwind CSS entry
│   ├── types.ts                 # Shared TypeScript types
│   ├── data/irisData.ts         # The 150-row Iris dataset
│   ├── utils/mlEngine.ts        # Train/test split + model logic (mirrors the Python script)
│   ├── utils/notebookGenerator.ts # Generates the downloadable .ipynb / .py from in-app data
│   └── components/              # Dashboard views (EDA, visuals, models, predictor, etc.)
├── iris_classification.py       # Standalone Python script (scikit-learn)
├── iris_flower_classification.ipynb  # Jupyter Notebook version
├── requirements.txt             # Python dependencies
├── index.html
├── package.json
├── vite.config.ts
└── tsconfig.json
```

---

## Part 1 — Run the web app in VS Code

### Prerequisites
- [Node.js](https://nodejs.org/) **v18 or later** (v20+ recommended)
- npm (comes bundled with Node.js)
- VS Code with the standard **ESLint**/**TypeScript** support (built in — no extra config needed)

### Steps

1. **Open the folder in VS Code**
   `File → Open Folder…` and select `iris-species-classifier/`.

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
   Open it in your browser (or `Ctrl+Click` the link in the VS Code terminal). The dev server has hot-reload — edits to any file in `src/` update the browser instantly.

5. **Type-check the code (optional but recommended)**
   ```bash
   npm run lint
   ```
   This runs `tsc --noEmit` and reports any TypeScript errors without emitting files.

6. **Build for production (optional)**
   ```bash
   npm run build
   npm run preview   # serve the production build locally to sanity-check it
   ```
   The output goes to `dist/`, which you can deploy to any static host (Vercel, Netlify, GitHub Pages, etc.).

That's it — **no `.env` file, no API key, and no server process are needed** to run this app.

---

## Part 2 — Run the Python analysis in VS Code

The Python script and notebook reproduce the model training/evaluation shown in the web app.

### Prerequisites
- Python 3.9+
- VS Code with the **Python** extension (and **Jupyter** extension if you want to run the notebook)

### Steps

1. **Create and activate a virtual environment** (recommended so dependencies don't pollute your global Python install)

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
   Open the Command Palette (`Ctrl+Shift+P` / `Cmd+Shift+P`) → `Python: Select Interpreter` → choose the `.venv` you just created.

4. **Run the script**
   ```bash
   python iris_classification.py
   ```
   This prints dataset stats, ANOVA feature-selection scores, and accuracy/precision/recall for all four models to the terminal, and saves two chart images (`iris_pairplot.png`, `iris_boxplots.png`) to the project folder.

5. **Or run the notebook**
   Open `iris_flower_classification.ipynb` in VS Code, pick the same `.venv` kernel in the top-right kernel selector, and run all cells (`Run All`).

---

## What was fixed from the original AI Studio export

- Removed unused dependencies that were never imported anywhere in the code: `@google/genai`, `express`, `dotenv`, `@types/express`, `esbuild`, `tsx`, `autoprefixer` (these came from AI Studio's generic template and pulled in ~130 extra packages for nothing).
- Removed `.env.example` / `metadata.json` / the `public/assets/aistudio` folder — these referenced a `GEMINI_API_KEY` and `APP_URL` that the app **never actually calls**, since this project makes no Gemini/API requests at all.
- Removed `bun.lock` and a stale `package-lock.json` so the project uses a single, consistent package manager (npm) and regenerated a clean lockfile.
- Simplified `vite.config.ts`: removed AI-Studio-specific HMR/file-watch environment flags that had no effect outside AI Studio, and set a fixed dev port with `open: true` for convenience.
- Fixed `package.json`: gave the project a real name/version and a `dev` script that doesn't depend on AI Studio's host binding flags.
- Verified the whole project actually type-checks (`tsc --noEmit`) and builds (`vite build`) cleanly.
- Verified `iris_classification.py` runs end-to-end against a live scikit-learn install with no errors.
- Added `requirements.txt` for the Python side (previously undocumented) and this README with real run instructions for VS Code, replacing the generic AI Studio boilerplate README.

## Tech stack

- **Frontend:** React 19, TypeScript, Vite 6, Tailwind CSS 4, lucide-react (icons), Prism.js (code highlighting)
- **ML/Data:** Python 3, scikit-learn, pandas, NumPy, Matplotlib, Seaborn
- **Dataset:** Fisher's Iris dataset (1936), 150 samples, via `sklearn.datasets.load_iris()`

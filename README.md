# CarbonWise-Personal-CO2-Tracker
## Safeguards

This project includes several safeguards to keep tips trustworthy, stable, and privacy‑conscious.

- **Input sanitization**
  - Implemented in `ai_tips.py` via `sanitize_inputs_for_prompt()` (alias of `_sanitize_inputs_for_prompt`).
  - Only allows known activity keys from `CATEGORY_MAP`.
  - Coerces values to floats, converts negatives to 0, and clamps extremes to heuristic thresholds in `EXTREME_THRESHOLDS`.
  - Prevents prompt‑injection by ignoring unexpected keys and non‑numeric content.

- **Output guardrails**
  - Implemented in `ai_tips.py` `_sanitize_tip_output()` and `clean_tip()`.
  - Removes URLs/HTML/code blocks, filters endorsements/medical terms, normalizes whitespace.
  - Caps tip length (~220 chars) and enforces concise UI display (1–2 short sentences).

- **Caching and backoff**
  - GPT prompts use exponential backoff with jitter and a short negative cache to avoid repeated failures.
  - UI (`app.py`) adds `st.cache_data` caching for tips using a normalized numeric key of user inputs.

- **Fallbacks**
  - If GPT is unavailable or inputs are ambiguous, deterministic local tips are generated.
  - Summary generation has a rules‑based fallback when the API errors.

- **Privacy**
  - No PII is collected. User inputs are simple numeric activity values.
  - API keys are read from environment variables (e.g., `.env`), not embedded in code.

If you find an area where an additional guardrail would help, please open an issue or PR.
# Sustainability Tracker 🌍
![Updated](https://img.shields.io/badge/updated-2025--10--04-blue) ![Streamlit](https://img.shields.io/badge/built%20with-Streamlit-ff4b4b)

Track your daily CO₂ emissions across Energy, Transport, and Meals. Get personalized eco-tips (GPT with fallback), view history and trends, and export beautiful PDF summaries with your branding.

Last updated: 2025-10-04

## Screenshots

- Dashboard (Compact density) — inputs collapsed, KPIs visible
  - File: `assets/screenshot_dashboard.png`
- Tips tab — GPT vs Fallback badge, copy-ready tip and summary
  - File: `assets/screenshot_tips.png`
- PDF export options — branding and chart toggles
  - File: `assets/screenshot_pdf_options.png`
- Prompt testing suite — metrics and compact tables
  - File: `assets/screenshot_prompt_suite.png`

If you don’t have the assets yet, capture them with your OS screenshot tool and save under `assets/` with the names above.

## Video Demo

- Short walkthrough (1–2 minutes):
  - Show density toggle (Compact vs Comfy)
  - Enter values, Calculate & Save, History update
  - Generate eco tip (GPT + fallback), copy block
  - PDF export with branding
  - Prompt testing suite quick run and CSV/ZIP export
- Placeholder link: https://example.com/your-demo-video
  - Replace with your actual upload (Drive, YouTube unlisted, Loom, etc.).

---

## Final Submission

- **App**: Streamlit-based Sustainability Tracker with Dashboard, History, Breakdown, and Tips tabs.
- **AI Tips**: GPT-backed with robust local fallback, sanitization, thresholds, and copy-ready blocks.
- **Experiments**: Prompt testing suite with metrics, charts, and exports.
- **Exports**: CSV across tabs and branded PDF generation (server-side).
- **UX**: Compact layout, density control with persistence, Debug tools moved to sidebar.
- **Security & Privacy**: `.env` for secrets, no PII, output guardrails.

- **Priority next steps (P1)**: Data integrations (utility/mobility) and Regionalization (region-specific factors & localized tips). See “Future Work (Perspectives)”.
This section summarizes what was delivered each day and the final state of the app.

### Day 1 — Project setup and core emissions engine
- **CO₂ engine**: `co2_engine.calculate_co2()` and `calculate_co2_breakdown()` with `CO2_FACTORS`.
- **Basic UI**: Streamlit skeleton, inputs for Energy/Transport/Meals, total CO₂.
- **Persistence**: Save daily entries to `history.csv`.

### Day 2 — Dashboard, History, and UX structure
- **Tabs**: Dashboard, History, Breakdown, Tips.
- **History**: Append + view past entries, CSV export.
- **Breakdown**: Per-category and per-activity metrics.

### Day 3 — Visualizations and trend metrics
- **Charts**: Category bar charts; time-series for recent days.
- **KPIs**: 7-day totals and deltas per category.

### Day 4 — Robust UX, prefs, and density controls
- **Density**: `Compact`/`Comfy` with persistence via `get_pref`/`set_pref`.
- **Safer reruns**: Pending apply patterns to avoid widget state errors.

### Day 5 — Exports and branding
- **CSV downloads**: Dashboard + History.
- **PDF export (server-side)**: ReportLab + optional Matplotlib; branding (title, colors, logo), pie chart, sparklines, footer.

### Day 6 — Prompt testing suite (experiments)
- **Experiments**: Run predefined scenarios across modes (Contextualized/Directive/Persona), incl. ambiguous inputs.
- **Metrics**: OK-rate, uniqueness, bigram diversity + bootstrap CIs; Altair charts; CSV/Markdown/ZIP exports.
- **Logs**: `prompt_log.csv` viewer and clearing tools.

### Day 7 — LLM optimization, eco tips polish, and compact UI
- **LLM settings**: Runtime temperature/max_tokens (tips and summary) with persistence.
- **Caching & fallbacks**: Scoped tip-cache invalidation; robust fallbacks for tips and summaries.
- **Guardrails**: Input sanitization, threshold clamping, and output cleanup.
- **UI compactness**: Wide layout, compact CSS, centered tables with max width, shorter charts, Debug moved to sidebar.
- **Threshold alignment**: `train_km` key in `ai_tips.py` thresholds; Advanced thresholds UI.

---

## Features

- **Dashboard** (Energy, Transport, Meals) with compact/comfy density
- **Personalized Eco Tips**
  - GPT-powered tips with resilient local fallback
  - Source badge (GPT vs Fallback)
- **History & Trends**
  - Per-category KPIs
  - 7-day change metrics, sparklines (in PDF)
- **Exports**
  - CSV downloads (Dashboard + History tab)
  - PDF export (server-side) with branding:
    - Title, logo (upload or fallback), accent color, text color, chart background
    - Optional pie chart and 7-day sparklines
    - Footer with &copy;/URL + page numbers
- **Demo Mode**
  - One-click demo values, “Exit Demo Mode” restore
  - Snapshot of pre-demo values and density + status indicator
  - “View snapshot detail” popover
- **Presets**
  - Prefill demo/presets safely (no widget state errors)
- **Robust UX**
  - Theme-aware defaults (light/dark)
  - Safe reruns (no deprecated APIs), no duplicate widget keys

---

## Quickstart

### Requirements

- Python 3.9+
- Pip packages:
  - streamlit
  - pandas
  - python-dotenv
  - openai (for GPT tips, optional)
  - reportlab (for PDF export)
  - matplotlib (optional, for PDF charts)

### Install

Using Anaconda (Windows)
```powershell
conda create -n sustain python=3.10 -y
conda activate sustain
pip install streamlit pandas python-dotenv openai reportlab matplotlib
```

Using pip (Windows PowerShell)
```powershell
python -m venv .venv
. .\.venv\Scripts\Activate.ps1
pip install streamlit pandas python-dotenv openai reportlab matplotlib
```

### Run

```powershell
streamlit run app.py
```

Streamlit will open at:
- Local URL: http://localhost:8501
- Network URL: http://<your-ip>:8501

### Tech Stack

- **Frontend/UI**: Streamlit
- **AI Tips**: OpenAI Chat Completions API (optional; robust local fallback if unavailable)
- **Data/Storage**: CSV (`history.csv`) for daily entries, JSON for user prefs
- **Caching**: Streamlit `st.cache_data` + in-module negative cache for failed GPT calls
- **PDF**: ReportLab (optional Matplotlib for charts)
- **Experimentation/Charts**: Altair (with optional `altair_saver`), pandas

---

## Environment & AI Tips

Set your OpenAI API key to enable GPT tips (optional). Without it, the app falls back to local tips automatically with a clear source badge.

Create a `.env` file in the project root:
```
OPENAI_API_KEY=sk-...
```

If you see 429 “insufficient_quota” errors:
- The app will retry and then use a local fallback tip.
- You can continue testing without GPT; the UI shows “AI source: Fallback”.

## Secrets

<a id="secrets"></a>

- Store your OpenAI key in a local `.env` file next to `app.py` and never commit it.
- Alternative (temporary) for a single session in Windows PowerShell:
  ```powershell
  setx OPENAI_API_KEY "sk-..." /M
  # Or for current session only
  $env:OPENAI_API_KEY = "sk-..."
  ```
- If the key is missing or invalid, the app automatically falls back to local tips and the UI labels the source accordingly.

---

## Using the App

### Density & Layout
- Density toggle at the top: `Compact` (tight, best for PDF) or `Comfy`.
- “Reset layout” sets Compact and collapses expanders for better PDF export.

### Inputs
- Enter daily values in the three sections:
  - Energy: e.g., `electricity_kwh`, `natural_gas_m3`, `district_heating_kwh`...
  - Transport: `bus_km`, `train_km`, `petrol_liter`, etc.
  - Meals: `meat_kg`, `dairy_kg`, `vegetarian_kg`, etc.

### Calculate & Save
- Computes total emissions, KPIs, and saves to `history.csv`.
- CSV download buttons are uniquely keyed (no duplicate-widget errors).

### Eco Tips
- Generate a personalized tip based on today’s inputs.
- GPT-backed with fallback. Source badge shows “GPT” or “Fallback”.
- Copy-ready code blocks with built-in copy icon (no fragile JS).
- Summary and Tip are both shown as copyable code blocks and downloadable text.

### Prompt Testing Suite
- Run predefined scenarios across modes: `Contextualized`, `Directive`, `Persona`.
- Optional ambiguous/edge-case inputs to stress-test guardrails.
- Computes per-mode metrics: OK-rate (relevant, actionable, simple), uniqueness, bigram diversity.
- Bootstrap 95% CIs for OK-rate and diversity; charts via Altair.
- Export full results as CSV/MD/ZIP with embedded interactive HTML charts when available.

### Day 7: LLM Optimization & UI Polish
- LLM parameters are configurable at runtime:
  - `Tip temperature`, `Tip max_tokens`, `Summary temperature`, `Summary max_tokens`.
  - UI: Debug → “LLM settings” expander; persisted to `.user_prefs.json`.
  - Applied via `ai_tips.set_llm_params(...)` and used in GPT calls.
- Scoped tip cache invalidation: changing LLM settings bumps `tip_cache_salt` so new tips are regenerated.
- Error handling and loading states:
  - GPT errors are retried with backoff and then fall back to local tips; UI never crashes.
  - Tip area shows a placeholder and spinner when generation is slow.
- Advanced thresholds polish:
  - Thresholds are adjustable in the “Advanced: Edge-case thresholds” section.
  - Keys aligned with activity names; applied via `set_extreme_thresholds(...)`.
- Debug tools:
  - “Simulate API failures” toggle to exercise fallback paths.

### Demo Mode
- Toggle “Demo mode” in the header:
  - Forces Compact density on next run
  - Loads representative demo values
  - Auto-generates a tip
  - Shows snapshot capture time
  - “View snapshot detail” lets you inspect saved inputs
- “Exit Demo Mode” restores your previous values and density safely.

### Presets
- Prefill demos/presets via the “Prefill demos/presets” popover.
- Uses a “pending apply” mechanism to avoid Streamlit widget-state exceptions.

---

## PDF Export (Server-side)

Use the UI in the Eco Tips tab:
- “PDF Branding & Options” expander:
  - Title
  - Accent color (auto default by theme)
  - Text color (auto default by theme)
  - Chart background color (auto default by theme)
  - Include pie chart
  - Include 7-day sparklines
  - Margins (top/side/bottom)
  - Footer text and toggle
  - Logo upload (PNG/JPG). If none, fallback path check: `logo.png` in project root.
  - If no upload and no `logo.png`: a styled vector fallback badge (rounded rect “ST”) is drawn.
- Click “Generate Eco Tips PDF (beta)”, then the download button.

Dependencies:
- Required: `reportlab`
- Optional for charts: `matplotlib`

Install:
```powershell
pip install reportlab matplotlib
```

Note:
- The original “Export PDF tips” popover describes manual browser-based printing. The new server-side export produces a file directly.

---

## Files & Structure

- `app.py` — Main Streamlit app
  - Tabs: Dashboard, History, Breakdown, Tips
  - Demo mode, presets, density controls
  - CSV and PDF exports
  - AI source badge, copy-ready blocks
- `co2_engine.py` — Emissions engine
  - `CO2_FACTORS`, `calculate_co2()`, `calculate_co2_breakdown()`
- `utils.py` — Formatting, normalization, helper functions
  - `format_emissions()`, `percentage_change()`, `friendly_message()`, etc.
- `ai_tips.py` — GPT/local tips
  - `generate_tip()`; fallback-safe, retries
  - `LAST_TIP_SOURCE` to signal GPT vs Fallback
- `history.csv` — Saved user entries (auto-created)
- `logo.png` — Optional default logo for PDF
- `test_co2_engine.py`, `test_utils.py` — Sample tests

---

## Troubleshooting

- **DuplicateWidgetID**: Fixed by unique `key=` props on all download buttons.
- **StreamlitAPIException**: “cannot be modified after widget is instantiated”
  - We use pending state mechanisms (`_pending_values`, `_pending_density`, `_pending_demo_off`) and rerun before widgets build.
- **`st.experimental_rerun()` deprecated**
  - We use `st.rerun()` or avoid forced reruns with smooth success notices.
- **GPT quota errors (429)**
  - App retries and then falls back to local tips automatically.
- **Copy buttons “do nothing”**
  - Replaced with Streamlit code blocks. Use the built-in copy icon on the right.

---

## Testing

Run tests (if you add pytest):
```powershell
pip install pytest
pytest -q
```

---

## Changelog (2025-10-01)

- Added Demo Mode with snapshot, status, and “Exit Demo Mode” (safe).
- Fixed imports (use `co2_engine.calculate_co2` & local `get_yesterday_total`).
- Replaced all deprecated `st.experimental_rerun`.
- Unique `key` for download buttons to avoid DuplicateWidgetID.
- Reworked copy to built-in code-block copy icons.
- AI source badge (GPT vs Fallback).
- Server-side PDF export (ReportLab + optional Matplotlib):
  - Branding options and logo upload + fallback
  - Theme-aware defaults (accent, text, chart bg)
  - KPIs, summary, tip, per-category table, pie chart, per-activity table
  - 7-day sparklines per category
  - Footer (&copy;/URL + page numbers)
- Theme-aware colors and improved typography.

---

## Contributing


- **Issues/Ideas**: Open a GitHub issue describing the feature/bug with reproduction steps.
- **PRs**: Keep changes small and focused. Include before/after screenshots for UI changes.
- **Testing**: Prefer adding/adjusting unit tests where practical. See the Testing section.
- **Secrets**: Never commit real API keys; use `.env.example` if you need to illustrate variables.

---

## Known Limitations

- **Data source**: Inputs are user-entered; no live integrations (e.g., utility APIs, transport trackers).
- **Emission factors**: Single set of factors; regional variations are not yet supported.
- **Local storage**: History in CSV; no multi-user backend or authentication.
- **PDF charts**: Matplotlib required for chart rendering; otherwise PDFs omit charts.
- **LLM dependency**: GPT tips optional; if API is down or quota exceeded, fallbacks are used.

## Future Work (Perspectives)

- **[P1] Data integrations (highest impact)**
  - Utility providers (electricity/gas/water), receipts/calendar import, mobility APIs (e.g., transit, odometer apps), optional wearables.
  - Outcome: reduces manual entry friction, improves accuracy, and enables continuous tracking.
  - First milestones: CSV import → OAuth for 1–2 providers → background sync jobs.

- **[P1] Regionalization (accuracy + relevance)**
  - Region-specific emission factors and localized tips/content.
  - Outcome: more accurate CO₂ estimates and culturally relevant advice.
  - First milestones: region selector → factor bundles → auto-geo via browser consent.

- **[P2] Goals & coaching**
  - Weekly targets, streaks, and personalized nudges based on largest sources or deltas.
  - Outcome: sustained engagement and measurable impact.

- **[P2] Explainable tips**
  - Show factor references and short rationales per tip; link to reputable sources.
  - Outcome: user trust, auditability, and educational value.

- **[P3] User accounts & cloud history**
  - Auth, multi-device sync, household/team sharing, role-based exports.
  - Outcome: collaboration and long-term retention.

- **[P3] Advanced exports**
  - Weekly/monthly templated PDFs, themes, watermarking, and scheduled email exports.

- **[P3] Model experiments**
  - Pluggable LLM backends, prompt variants, and A/B testing harness.

- **[P3] Accessibility & i18n**
  - WCAG color/contrast and keyboard nav; add translations starting with top regions.

### 30/60/90-day roadmap

- **0–30 days (P1)**
  - CSV import for utilities; region selector + factor bundles.
  - OAuth for first provider; initial localized tip set.
- **30–60 days (P2)**
  - Goals & coaching (weekly targets, nudges); explainable tips with references.
  - Optional background sync for 1–2 providers.
- **60–90 days (P3)**
  - Accounts & cloud history; advanced exports (templated PDFs, schedules).
  - Pluggable LLM backends and basic A/B test harness.

Roadmap sketch: P1 (0–4 weeks) → P2 (4–8 weeks) → P3 (8–12 weeks), with weekly iterative releases.

---

## License


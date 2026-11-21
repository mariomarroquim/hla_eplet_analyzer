# HLA Eplet Analyzer

HLA Eplet Analyzer is a web application that predicts whether an anti-HLA antibody targets a given eplet.

## Project details

Flask app (browser UI + JSON web service) that predicts whether an anti-HLA antibody targets a given eplet, using a pre-trained scikit-learn RandomForest stored in `model.joblib`.

### Commands

Each script installs `requirements.txt` first.

- Dev server: `sh run_development.sh` (Flask on http://localhost:5000)
- Production: `sh run_production.sh` (runs `app.py` via Waitress on port 80; note it runs `killall python3` first)
- Tests: `sh test.sh` (equivalent to `python3 -m unittest test.py`)
- Single test: `python3 -m unittest test.SmokeTests.test_successful_prediction`
- Regenerate model: `sh generate_model.sh` (trains from `validation/datasets/pe-dataset.csv`, overwrites `model.joblib`)
- Format: `python3 -m ruff format .` (config in `ruff.toml`; check only: `python3 -m ruff format --check .`)

There is no build step.

### Architecture

- `app.py` loads `model.joblib` at import time and exposes `/` (renders `templates/index.html`), `/version` (hard-coded date string) and `/predict`.
- `/predict` reads params from `request.args` only (query string, even on POST), builds a one-row DataFrame and returns `label`, `score0`, `score1` as strings. Any exception yields a fixed 500 text message.
- **Feature contract:** `feature_columns` in `app.py` must match, in name and order, the columns `generate_model.py` trains on (the PE dataset minus `panel_info`, `panel_eplet` and the `reactive` label). Note the naming mismatch: the API param `eplet_locus=drb` maps to feature `locus_dr`, and API params `eplet_allele_count`/`eplet_min_mfi`/`eplet_max_mfi` map to `panel_allele_count`/`panel_min_mfi`/`panel_max_mfi`.
- Frontend: `static/js/custom.js` (jQuery) intercepts the form in `index.html`, calls `/predict` via `$.getJSON` and renders the result. Form field `name`s are the API param names. CSS is Pure.css plus `custom.css`.
- `generate_model.py` uses `RandomUnderSampler` + `RandomizedSearchCV` over a RandomForest with no fixed random seed, so retraining yields a different model.
- `validation/` holds the datasets (PE and SP) and the experiment scripts/notebooks behind the accuracy figures in the README; it is not used by the app at runtime.

### Gotchas

- `test.py` asserts exact probability values from the committed `model.joblib`. Regenerating the model will break `test_successful_prediction`; update the expected values when the model is intentionally replaced.
- The `/version` string is duplicated in `app.py` and `test.py`; update both together.
- Python code uses 2-space indentation and single quotes, enforced by `ruff.toml`.

## Conventions

- Don't add any new dependencies without asking first.
- If the code changes, sync both `AGENTS.md` and `README.md` as well.
- Run `python3 -m unittest test.py` to check for errors before every commit.
- Don't commit and push changes unless explicitly required.
- This project only uses the `main` branch; commit and push directly to it.

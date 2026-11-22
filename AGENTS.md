# HLA Eplet Analyzer

HLA Eplet Analyzer is a web application that predicts whether an anti-HLA antibody targets a given eplet.

## Project details

Flask app (browser UI + JSON web service) that predicts whether an anti-HLA antibody targets a given eplet,
using a pre-trained scikit-learn RandomForest stored in `model.joblib`.

### Commands

Each script installs `requirements.txt` first.

- `sh generate_model.sh`: generates `model.joblib` by training on `validation/datasets/pe-dataset.csv`.
- `sh run_development.sh`: runs a development instance w/o Waitress with Flask on port 5000.
- `sh run_production.sh`: runs a production instance via Waitress with Flask on port 80.
- `sh test.sh`: runs unit tests (equivalent to `python3 -m unittest test.py`).

There is no build step.

### Architecture

- `/`: end point that renders `templates/index.html` for human user interaction.
- `/predict`: end point that processes the  request params and returns `label`, `score0`, `score1`.
- `/version`: end point that exposed a hard-coded date string for the current release.
- `app.py`: script that loads `model.joblib`, and exposes `/`, `/predict`, and `/version` end points.
- `generate_model.py`: script that uses a RandomForest for generating the model.
- `validation/`: folder that holds datasets and experiments for stats in the README. Not used at runtime.
- **Feature contract:** `feature_columns` in `app.py` must match, in name and order,
the columns `generate_model.py` trains on (the PE dataset minus `panel_info`, `panel_eplet` and the `reactive` label).
Note the naming mismatch: the API param `eplet_locus=drb` maps to feature `locus_dr`, and
API params `eplet_allele_count`/`eplet_min_mfi`/`eplet_max_mfi` map to `panel_allele_count`/`panel_min_mfi`/`panel_max_mfi`.

## Conventions

- Don't add any new dependencies without asking first.
- If the code changes, sync both `AGENTS.md` and `README.md` as well.
- Run `python3 -m unittest test.py` to check for errors before every commit.
- Don't commit and push changes unless explicitly required.
- This project only uses the `main` branch; commit and push to it when required.

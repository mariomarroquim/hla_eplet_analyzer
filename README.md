# HLA Eplet Analyzer

This web application provides both a browser interface and a web service for predicting anti-HLA antibody targets at the eplet level using machine learning.

## Instructions

This is a standard Python application. Run `sh run_development.sh`, then open [http://localhost:5000](http://localhost:5000).

* Production: `sh run_production.sh` (serves on port 80 via Waitress).
* Tests: `sh test.sh`.
* Model regeneration: `sh generate_model.sh` (trains from `validation/datasets/pe-dataset.csv` and overwrites `model.joblib`).
* Formatting: `python3 -m ruff format .` (settings in `ruff.toml`).

### API

`GET` or `POST` to `/predict` with the params `eplet_locus` (`abc`, `drb`, `dq` or `dp`), `eplet_allele_count`, `eplet_min_mfi`, `eplet_max_mfi`, `panel_nc` and `panel_pc`. The response is a JSON object with `label` (`1` means antibody-reactive) and the probabilities `score0` and `score1`. Invalid or missing params return HTTP 500.

## Validation

The datasets comprise mismatched eplets from HLA alleles tested on single-antigen bead panels. The experiments evaluate the classification of each eplet–panel pair into low- and high-reactivity categories. All validation files are in the [validation](https://github.com/mariomarroquim/hla_eplet_analyzer/tree/main/validation) folder.

* Basic validation: 89% accuracy and 88% AUC-ROC.
* Regular cross-validation: 88% accuracy and 92% AUC-ROC.
* Cross-validation with feature selection: 89% accuracy and 94% AUC-ROC.
* Cross-validation with feature selection and hyperparameter tuning: 91% accuracy and 94% AUC-ROC.

## Support

You can contact me at mariomarroquim@gmail.com.

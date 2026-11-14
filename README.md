🇬🇧 \*\*English\*\* | 🇷🇺 \[Русская версия](README.ru.md)



\# 🪐 Exoplanet Radius Prediction



Machine learning model for estimating exoplanet radii based on mass, orbital parameters, and host star characteristics.



\[!\[Python](https://img.shields.io/badge/Python-3.12-blue?logo=python\&logoColor=white)](https://www.python.org/)

\[!\[scikit-learn](https://img.shields.io/badge/scikit--learn-1.x-orange?logo=scikit-learn\&logoColor=white)](https://scikit-learn.org/)

\[!\[Jupyter](https://img.shields.io/badge/Jupyter-Notebook-orange?logo=jupyter\&logoColor=white)](https://jupyter.org/)

\[!\[License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)



\---



\## 📋 About the Project



An exoplanet's radius is a key characteristic that determines its nature: whether it's a rocky Earth-like planet, a gas giant, or a "Neptune". Direct radius measurement is only possible via the \*\*transit method\*\* (by observing the dip in stellar brightness as the planet passes across the star's disk). For planets discovered via radial velocity or microlensing, the radius is often unknown or estimated very roughly — through model "mass–radius" relations.



\*\*Goal:\*\* build a linear regression model that predicts an exoplanet's radius (in Earth radii) based on available characteristics — mass, orbital parameters, and stellar properties.



\*\*Data:\*\* \[NASA Exoplanet Archive](https://exoplanetarchive.ipac.caltech.edu/) — confirmed exoplanets table (40,350 records, 92 columns).



\*\*Stack:\*\* Python, pandas, numpy, scikit-learn, matplotlib, seaborn, joblib.



\---



\## 🎯 Key Results



| Model | MAE (R⊕) | RMSE (R⊕) | R² | MAPE |

|---|---|---|---|---|

| DummyRegressor (baseline) | 3.39 | — | ≈ 0 | 116% |

| Baseline LinearRegression (10 features) | 2.63 | 3.51 | 0.46 | 104% |

| \*\*Final LinearRegression\*\* (11 features + `is\_missing\_mass`) | \*\*1.52\*\* | \*\*2.36\*\* | \*\*0.76\*\* | \*\*52%\*\* |



\*\*Final model (test, 951 rows):\*\*

\- \*\*MAE = 1.52 R⊕\*\* — 1.7× more accurate than baseline.

\- \*\*R² = 0.76\*\* — model explains 76% of radius variance.

\- \*\*MAPE = 52%\*\* — twice as accurate as baseline in relative terms.

\- \*\*RMSE = 2.36 R⊕\*\* — acceptable error "spread".



\### Adaptive Model



The final model works in \*\*two modes\*\*:



| Scenario | Example | Error | Comment |

|---|---|---|---|

| \*\*Mass known\*\* (RV observations) | Planet 5.49 R⊕ → predicted 5.72 R⊕ | \*\*0.23 R⊕ (4%)\*\* | Nearly perfect |

| \*\*Mass unknown\*\* (`None`) | Same planet → predicted 2.46 R⊕ | 3.03 R⊕ | Conservative estimate — model "knows" data is insufficient |



This is \*\*more honest\*\* than pretending the model works equally well for all planets.



\---



\## 💡 Top-5 Insights



\### 1. The "Neptunian Desert" in Radius Distribution

The `pl\_rade` distribution in log-scale is \*\*bimodal\*\*: two peaks (super-Earths \~2 R⊕ and gas giants \~12 R⊕) with a gap between them at 4–8 R⊕. This is a known astrophysical phenomenon — the \*\*"Neptunian desert"\*\*: planets are scarce in this zone due to atmospheric photoevaporation by nearby stars.



\### 2. Planet Mass — the Primary Physical Predictor

`pl\_bmasse` ↔ `pl\_rade`: \*\*r = 0.86\*\* in log-scale. This is the fundamental "mass–radius" relation. Without mass, R² drops from 0.46 to 0.27 — confirming its key role.



\### 3. 68% Mass Missingness "Blurs" the Signal

For transiting planets, mass is often unknown (RV requires additional observations). Median imputation "freezes" the feature — the model cannot distinguish a gas giant from a super-Earth. This is a \*\*fundamental data limitation\*\*, not a model flaw.



\### 4. `is\_missing\_mass` — Key Engineering Improvement (R² = 0.46 → 0.76)

Adding a \*\*binary missing-mass flag\*\* produced a qualitative leap: \*\*R² increased by 63%\*\*, MAE dropped by 42%, MAPE halved. The model now "knows" where mass is unreliable and adjusts its prediction. This is the classic \*\*missing indicator\*\* technique in ML.



\### 5. The Model "Cured" Regression to the Mean

The residual plot showed: before the flag, the model systematically \*\*overestimated\*\* small planets and \*\*underestimated\*\* large ones. After adding the flag, errors decreased across all groups:



| Radius group | Without flag | With flag | Improvement |

|---|---|---|---|

| < 2 R⊕ (super-Earths) | −1.68 | \*\*−0.76\*\* | +0.92 |

| 2–4 R⊕ (Neptunes) | −0.59 | \*\*−0.12\*\* | +0.47 |

| 8–15 R⊕ (Jupiters) | +3.69 | \*\*+0.69\*\* | \*\*+3.00\*\* |

| > 15 R⊕ (giants) | +7.90 | \*\*+5.15\*\* | +2.74 |



The largest improvement is where things were worst.



\---



\## 📊 Visualizations (in the notebook)



\- \*\*`log10(pl\_rade)` histogram\*\* — bimodality and the "Neptunian desert".

\- \*\*Residual plot: baseline vs final model\*\* — clear reduction of systematic errors.

\- \*\*Pred vs Actual (final model)\*\* — points align with y = x across the full range (2–20 R⊕).

\- \*\*Feature importance (bar chart)\*\* — top-3: `pl\_bmasse`, `is\_missing\_mass`, `st\_rad`.



\---



\## 🗂 Repository Structure

exoplanet-radius-prediction/

├── data/

│ └── exoplanets.csv # NASA source data

├── artifacts/

│ ├── model\_flag.joblib # Trained model (1.2 KB)

│ └── preprocessing\_params.joblib # Preprocessing parameters (3.4 KB)

├── notebooks/

│ └── project\_exoplanets.ipynb

├── README.md # English (this file)

├── README.ru.md # Russian version

└── requirements.txt



\---



\## 🚀 How to Reproduce



\### 1. Clone the repository

```bash

git clone https://github.com/DigitalOcean78/exoplanet-radius-prediction.git

cd exoplanet-radius-prediction



2\. Install dependencies



pip install -r requirements.txt



3\. Download the data

Download the Confirmed Planets table from NASA Exoplanet Archive as CSV and place it at data/exoplanets.csv.



4\. Run the notebook



jupyter notebook notebooks/project\_exoplanets.ipynb



Reproducibility: random\_state=42 is fixed across all experiments. Test metrics: MAE = 1.52, R² = 0.76.



🛠 Using the Trained Model

Artifacts are saved in the artifacts/ folder, ready to be loaded into an API:

import joblib

import numpy as np

import pandas as pd



\# Load artifacts (once at service startup)

model = joblib.load('artifacts/model\_flag.joblib')

params = joblib.load('artifacts/preprocessing\_params.joblib')



def predict\_planet\_radius(pl\_bmasse=None, st\_rad=None, st\_teff=None, ...):

&#x20;   """

&#x20;   Predicts exoplanet radius in R⊕.

&#x20;   If pl\_bmasse = None — the model "knows" mass is unreliable

&#x20;   and returns a conservative estimate.

&#x20;   """

&#x20;   # Log transformation

&#x20;   log\_cols = \['pl\_bmasse', 'pl\_orbsmax', 'st\_rad', 'st\_mass', 'sy\_dist', 'sy\_pnum']

&#x20;   raw = {col: (np.log10(v) if v and v > 0 else np.nan) for col, v in {...}.items()}

&#x20;   

&#x20;   # Median imputation and standardization

&#x20;   features = pd.DataFrame(\[raw]).fillna(params\['medians'])

&#x20;   features\['is\_missing\_mass'] = 1 if pl\_bmasse is None else 0

&#x20;   features = features\[params\['columns']]

&#x20;   features\_scaled = (features - params\['train\_mean']) / params\['train\_std']

&#x20;   

&#x20;   return model.predict(features\_scaled)\[0]



\# Example: Neptune-like planet with known mass

radius = predict\_planet\_radius(pl\_bmasse=17.3, st\_rad=1.108, ...)

print(f'Predicted radius: {radius:.2f} R⊕')



Full implementation — in the notebook, function predict\_planet\_radius\_final.



⚠️ Limitations and Warnings

1\. R² = 0.76 Below Target (≥ 0.85)

Related to the linear architecture: LinearRegression cannot capture non-linear mass–radius relations (for gas giants, the relation isn't power-law but "saturates"). To reach R² ≥ 0.85, non-linear models are needed (RandomForest, GradientBoosting).



For production use in rough-estimate tasks (MAE = 1.52 R⊕ — about 60% of median radius) the model is sufficient. For scientific publications — not.



2\. is\_missing\_mass Encodes Kepler Mission Selection

The flag reflects observational selection: small planets often lack RV masses because RV requires more observing time. When migrating to TESS or PLATO, the flag will require retraining — otherwise the model will "expect" the old selection and give biased predictions.



3\. Model Trained Only on Transiting Planets

For planets discovered by other methods (RV, microlensing, imaging), the model is not applicable without retraining — there the radius is either unknown or measured roughly.



4\. Multicollinearity st\_rad ↔ st\_mass (r = 0.899)

The st\_mass coefficient may be negative (multicollinearity artifact). Lasso with alpha = 0.05 zeroes out st\_mass and st\_teff as redundant — this confirms the interpretation.



🔮 Future Directions

Non-linear models (RandomForest, GradientBoosting, XGBoost): expected R² improvement to 0.80–0.85.



Other missingness indicators (is\_missing\_eccen, is\_missing\_orbsmax): by analogy with is\_missing\_mass. May yield additional gains.



Per-regime models: super-Earths (1–4 R⊕) vs gas giants (8–20 R⊕).



Integration with RV catalogs (HARPS, ESPRESSO): increase in planets with known mass — main bottleneck.



API deployment (Flask / FastAPI): artifacts are ready.



SHAP analysis for interpretability of individual predictions.



📚 Sources and References

NASA Exoplanet Archive — source data.



Neptunian Desert — Nature, 2017 — context on radius bimodality.



sklearn: Missing Indicator — on missing indicators.



👤 Author

DigitalOcean78



📄 License

MIT License — free to use.



⭐ If this project helped you — leave a star!


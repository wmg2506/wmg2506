<p align="right">
  <strong>English</strong> · <a href="./README_ES.md">Español</a>
</p>

# EWS Madrid · Audit of a FIES predictive model

> A completed audit showing why high training performance did not demonstrate reliable prediction for unseen municipalities.

**Author:** Marta González Vázquez  
**Context:** early-warning data and modelling audit  
**Status:** completed · September 2026  
**Scope:** 185 municipalities  
**Technologies:** Python · pandas · scikit-learn · Jupyter · data quality · temporal validation

## Executive conclusion

The audited workflow did not produce a model capable of reliably predicting FIES 2024 for municipalities excluded from training. The strong original metrics mainly described fit on known data and did not demonstrate future predictive capacity.

No final model was saved because no candidate outperformed a simple baseline that predicts the training median.

## What was audited

The review covered the full modelling chain for two continuous targets:

- moderate-or-severe FIES probability;
- severe FIES probability;
- target lineage and documented meaning;
- annual targets versus monthly predictors;
- missing-data treatment;
- temporal cut-off rules;
- train/test separation;
- model selection and out-of-sample evaluation.

Only aggregate methodological evidence is published here. The source datasets, credentials, notebooks and organisational materials remain private.

## Main findings

1. **The reserved test set was not used correctly in the original evaluation.** Training performance was given too much weight.
2. **The grains were incompatible.** Each municipality had 12 monthly predictor rows but one annual FIES target, which could multiply the target artificially.
3. **Backward filling introduced future information.** A total of 64,257 cells—7.1% of the originally missing values—used later observations.
4. **Target rounding reduced information.** The two continuous targets were reduced from 165 distinct values to 33 and 13.
5. **The target formula was not verifiable.** No auditable documentation was found for the construction of the two FIES probability variables.

## Corrections applied

- One analytical row per municipality and year.
- Predictor cut-off at 31 December 2023 for a 2024 target.
- Use of the original matrix and removal of backward filling.
- Imputation learned only inside each training partition.
- Repeated validation with 5 folds and 5 repetitions, using the same municipal splits for every model.
- Mandatory comparison with a median DummyRegressor.
- Continuous targets retained without rounding.

## Validation results

| Target | Median baseline MAE | Best candidate | Candidate MAE | Decision |
|---|---:|---|---:|---|
| Moderate or severe | **6.688** | Ridge log1p | 6.828 | Not validated |
| Severe | **1.227** | Ridge log1p | 1.467 | Not validated |

Random Forest, Gradient Boosting, regularised linear models and log-transformed targets were compared. None improved on the baseline.

Overfitting was especially visible in Gradient Boosting for the moderate-or-severe target: training MAE was **1.009**, while validation MAE increased to **8.139**.

## Recommendations

1. **Do not present 2025 or 2026 estimates as reliable predictions** with the current evidence.
2. Obtain trustworthy FIES observations for several years and document the target formula before building a temporal predictive model.
3. If only FIES 2024 is available, limit the product to descriptive distributions, associations and municipal profiles.
4. Preserve temporal cut-offs, fold-specific preprocessing and baseline comparison as mandatory controls.
5. Keep the audit trail for target lineage, imputations, municipal partitions, metrics and model decisions.

## What this case demonstrates

- detection of leakage, overfitting and grain mismatch;
- honest validation against a simple baseline;
- correction of a full data-to-model workflow;
- ability to stop an unsupported model rather than optimise a misleading metric;
- translation of technical findings into operational recommendations.

---

**Marta González Vázquez**  
Senior IT & Critical Operations · Data Quality · Analytics · Applied AI


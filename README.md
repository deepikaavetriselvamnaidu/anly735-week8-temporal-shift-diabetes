# anly735-week8-temporal-shift-diabetes
ANLY 735 replication lab evaluating temporal distribution shift in diabetes prediction using NHANES 2015–2016 and 2017–2018 data, with logistic regression and out-of-distribution performance evaluation.

# ANLY 735 Week 8: Temporal Distribution Shift in Diabetes Prediction

## Project Overview

This project investigates whether a logistic regression model for predicting self-reported physician-diagnosed diabetes maintains its predictive performance across different National Health and Nutrition Examination Survey (NHANES) cycles.

The analysis uses NHANES 2015–2016 as the development period and NHANES 2017–2018 as a later-cycle, out-of-distribution (OOD) proxy evaluation period. This project is a proxy replication inspired by the evaluation of distribution shifts in machine learning described by Koh et al. (2021). It does not attempt to reproduce the original WILDS benchmark results numerically.

## Research Question

Does a logistic regression model trained on NHANES 2015–2016 maintain similar predictive performance when evaluated on NHANES 2017–2018?

## Data Sources

The analysis uses publicly available data from NHANES, a program conducted by the National Center for Health Statistics to assess the health and nutritional status of people in the United States (National Center for Health Statistics [NCHS], n.d.).

The analysis uses the following survey cycles:

- NHANES 2015–2016
- NHANES 2017–2018

The relevant data files include:

- Demographics (DEMO)
- Body measurements (BMX)
- Diabetes questionnaire (DIQ)

These files are merged using the participant identifier `SEQN`. The analysis includes adults aged 20 years and older and uses complete cases for the specified outcome and predictors.

**Official data source:** https://www.cdc.gov/nchs/nhanes/

The raw NHANES data files are not included in this repository. Users should obtain the relevant files from the official NHANES website and follow the analysis code's file-path requirements.

## Methods

### Outcome and Predictors

The outcome is self-reported physician-diagnosed diabetes, derived from the NHANES diabetes questionnaire variable `DIQ010`. The analysis uses age (`RIDAGEYR`), body mass index (`BMXBMI`), and sex (`RIAGENDR`) as predictors.

### Model and Evaluation Design

A logistic regression model is fitted using NHANES 2015–2016 data. The development-cycle data are divided into training and held-out test subsets using an 80/20 stratified split with random seed 42. The fitted model is then evaluated on the held-out test subset and on the later NHANES 2017–2018 cycle without refitting its coefficients.

Evaluating a fixed model across different data distributions is consistent with the broader distribution-shift evaluation problem discussed by Koh et al. (2021).

### Evaluation Metrics

The analysis reports four metrics:

- **ROC-AUC:** Measures the model's ability to distinguish between participants with and without the positive outcome.
- **Brier score:** Measures the mean squared error of predicted probabilities.
- **Sensitivity:** Measures the proportion of positive cases correctly identified at the classification threshold of 0.50.
- **Specificity:** Measures the proportion of negative cases correctly identified at the same threshold.

The original analysis uses complete cases and does not apply NHANES survey weights or adjust for the complex survey design.

## Reported Results

The following results are reported in the accompanying replication laboratory assignment.

| Metric | 2015–2016 ID Test | 2017–2018 OOD Proxy |
|---|---:|---:|
| Sample size | 1,056 | 5,010 |
| ROC-AUC | 0.772 | 0.778 |
| Brier score | 0.114 | 0.118 |
| Sensitivity | 0.038 | 0.075 |
| Specificity | 0.983 | 0.986 |

*Note.* ID refers to the held-out test subset from the development-cycle data. OOD proxy refers to the later-cycle evaluation. These values are transcribed from the assignment report and should be verified by running the analysis code.

### Interpretation

The ROC-AUC increases slightly from 0.772 to 0.778, suggesting similar discrimination in the two evaluations based on the reported point estimates. The Brier score increases from 0.114 to 0.118, indicating slightly greater probability prediction error in the later-cycle evaluation.

Sensitivity is low in both evaluations, meaning that the model misses many participants with the positive outcome at the selected threshold. Specificity is high in both evaluations. Therefore, similar ROC-AUC values alone do not establish that the model is suitable for identifying individual diabetes cases.

Because confidence intervals and formal uncertainty estimates are not reported here, the observed metric differences should be interpreted descriptively rather than as evidence of a statistically significant change.

## Comparison With WILDS

Koh et al. (2021) introduced WILDS as a benchmark for evaluating machine-learning models under distribution shifts in real-world datasets. Their work highlights the importance of evaluating models beyond conventional in-distribution test settings.

This project applies a related evaluation principle by comparing a fixed model's performance across two NHANES survey cycles. However, it is a **proxy replication**, not a direct replication of WILDS datasets, models, or numerical benchmark results. The results should not be compared numerically with the original WILDS benchmark scores.

## Reproducibility

The project is intended to document the steps needed to reproduce the analysis, including:

- The official data sources and required NHANES files
- Participant-level data merging and preprocessing
- Eligibility criteria and complete-case handling
- The random seed and training/test split
- Logistic regression specification
- Evaluation metrics and classification threshold
- R and package versions
- Instructions for executing the analysis

The repository should contain the actual R/Quarto source code and any necessary configuration files. Exact execution instructions and environment details should be updated to reflect the files and software versions actually used. The reported results should be treated as verified only after they have been reproduced from the committed source code.

## Limitations

Several limitations should be considered when interpreting the results.

First, this is a proxy replication of a distribution-shift evaluation strategy rather than a direct replication of the WILDS benchmark (Koh et al., 2021).

Second, the analysis does not apply NHANES survey weights or account for its complex sampling design. Consequently, the results should not be treated as fully survey-design-adjusted population estimates.

Third, the analysis uses complete cases, which may introduce selection bias if participants with missing data differ systematically from those included.

Fourth, confidence intervals and independent calibration assessments are not included in the reported results. Similar ROC-AUC values do not guarantee that predicted probabilities are well calibrated or that sensitivity is adequate for practical screening.

Future extensions could include confidence intervals, calibration plots, calibration intercept and slope, and additional evaluation of performance across demographic subgroups. Any recalibration should be evaluated on separate data to avoid overstating performance.

## References

Koh, P. W., Sagawa, S., Marklund, H., Xie, S. M., Zhang, M., Balsubramani, A., Hu, W., Yasunaga, M., Phillips, R. L., Gao, I., Lee, T., David, E., Stavness, I., Guo, W., Earnshaw, B., Haque, I., Beery, S., Leskovec, J., Kundaje, A., ... Liang, P. (2021). WILDS: A benchmark of in-the-wild distribution shifts. *Proceedings of the 38th International Conference on Machine Learning, 139*, 5637–5664. https://proceedings.mlr.press/v139/koh21a.html

National Center for Health Statistics. (n.d.). *National Health and Nutrition Examination Survey*. Centers for Disease Control and Prevention. https://www.cdc.gov/nchs/nhanes/

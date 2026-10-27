# Determinants of Blood Pressure Variability: A Multilinear Regression Approach

[![Language: R](https://img.shields.io/badge/Language-R-276DC3.svg?style=flat-square)](https://www.r-project.org/)
[![Data: NHANES](https://img.shields.io/badge/Dataset-NHANES-0052CC.svg?style=flat-square)](https://www.cdc.gov/nchs/nhanes/about_nhanes.htm)
[![Report: PDF](https://img.shields.io/badge/Report-PDF-EC1C24.svg?style=flat-square)](./BP%20Variability%20Report.pdf)

## 📌 Overview
High blood pressure (BP) is a critical global health challenge strongly linked to cardiovascular disease and stroke. This project investigates the research question: **How do physiological and lifestyle factors collectively influence systolic blood pressure variability (BPV)?**

Using data from the **National Health and Nutrition Examination Survey (NHANES)**, this study builds, transforms, validates, and diagnoses a multilinear regression framework to quantify the individual and combined effects of physiological traits and lifestyle habits on average systolic blood pressure (`BPSysAve`).

---

## 📊 Variables & Preliminary Model
The initial specification models average systolic blood pressure against five physiological and lifestyle predictors supported by clinical literature:

* **`BPSysAve` (Response):** Average systolic blood pressure (mmHg)
* **`Gender`:** Biological sex (`male` / `female`)
* **`Age`:** Age in years
* **`BMI`:** Body Mass Index ($\text{kg/m}^2$)
* **`AlcoholYear`:** Annual frequency of alcohol consumption (0 to 365 days/year)
* **`PhysActiveDays`:** Weekly frequency of moderate-to-vigorous physical activity (0 to 7 days/week)

---

## 🔬 Methodology & Statistical Pipeline

### 1. Model Diagnostics & Condition Checks
* **Condition 1 & 2 Verification:** Evaluated the conditional mean response via Response vs. Fitted scatterplots and inspected pairwise predictor plots for non-linear relationships.
* **Assumption Testing:** Assessed residual plots and Normal Q-Q plots for linearity, homoscedasticity (constant variance), uncorrelated errors, and normality.
* **Multicollinearity Monitoring:** Computed Variance Inflation Factors (VIF) across preliminary and intermediate models (flagging threshold: $\text{VIF} > 5$). All predictors demonstrated minimal multicollinearity ($\text{VIF} \in [1.01, 1.05]$).

### 2. Box-Cox Transformations
To resolve severe fanning (heteroskedasticity), non-linearity in `BMI`, and heavy tail deviations in the preliminary Normal Q-Q plot, multi-variable **Box-Cox transformations** were applied:
* **Response:** Inverse transformation on `BPSysAve` ($\text{BPSysAve}^{-1}$)
* **Predictors:** Square root transformation on `Age` ($\sqrt{\text{Age}}$) and `PhysActiveDays` ($\sqrt{\text{PhysActiveDays}}$), inverse square root transformation on `BMI` ($\text{BMI}^{-1/2}$), and shifted log transformation on `AlcoholYear` ($\log(\text{AlcoholYear} + 1)$).

$$\text{BPSysAve}^{-1} = \beta_0 + \beta_1 \text{Gender} + \beta_2 \sqrt{\text{Age}} + \beta_3 \frac{1}{\sqrt{\text{BMI}}} + \beta_4 \log(\text{AlcoholYear} + 1) + \beta_5 \sqrt{\text{PhysActiveDays}} + \epsilon$$

### 3. Manual & Automated Model Selection
* **Manual Selection ($\alpha = 0.05$):** Conducted an omnibus ANOVA test ($p = 2.2 \times 10^{-16} < 0.05$), individual $t$-tests, and a Partial $F$-test. Because `PhysActiveDays` was statistically insignificant ($p = 0.635$, Partial $F = 0.228 > 0.05$), it was excluded from the reduced model without loss of explanatory power.
* **Automated Cross-Validation:** Deployed **All Possible Subsets**, **Forward Selection**, **Backward Elimination**, and **Stepwise Regression** optimizing Adjusted $R^2$ and AIC. All four automated algorithms converged on the exact same four-predictor specification as manual selection.

### 4. Outlier & Influence Diagnostics
Evaluated the final model using leverage thresholds, standardized residuals, **Cook's Distance**, **DFFITS**, and **DFBETAS** to quantify the impact of extreme observations on fitted values and parameter estimates.

---

## 📈 Final Model & Key Findings

The validated final regression equation is:

$$\widehat{\text{BPSysAve}^{-1}} = 8.728 \times 10^{-3} - 3.845 \times 10^{-4}\text{Gender}_{\text{male}} - 2.905 \times 10^{-4}\sqrt{\text{Age}} + 1.019 \times 10^{-2}\frac{1}{\sqrt{\text{BMI}}} - 2.475 \times 10^{-5}\log(\text{AlcoholYear} + 1)$$

* **Clinical Interpretation:** Because the response is inverse-transformed ($\text{BPSysAve}^{-1}$), a *decrease* in $\text{BPSysAve}^{-1}$ corresponds to an *increase* in systolic blood pressure (`BPSysAve`). Holding all other variables constant, male sex, older age ($\sqrt{\text{Age}}$), higher BMI (lower $1/\sqrt{\text{BMI}}$), and more frequent annual alcohol consumption ($\log(\text{AlcoholYear} + 1)$) are each statistically associated with higher systolic blood pressure.
* **Multicollinearity:** Final model VIF values ranged between **1.01 and 1.04**, confirming stable parameter variance.
* **Limitations:** The final model achieved an Adjusted $R^2$ of **0.1826**, indicating that while these demographic and lifestyle factors are statistically significant, roughly 81.7% of systolic blood pressure variation is driven by unmeasured genetic, clinical, or environmental factors. Additionally, discrete clustering in `AlcoholYear` residuals highlights correlated error structures inherent to self-reported survey data.

---

## 📂 Repository Structure

```text
blood-pressure-variability-analysis/
├── BP Variability Report.pdf       # Full manuscript with diagnostic plots, derivations, and ethics discussion
├── bp_variability_analysis.Rmd     # Complete R Markdown source code for data cleaning, modeling, and diagnostics
└── README.md                       # Project documentation and summary
```

## 👥 Authors
* **Alisha Pham**
* **Katrina Sha**
* **Dabin Choi**

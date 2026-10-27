# Determinants of Blood Pressure Variability: A Multilinear Regression Approach

[![Language: R](https://img.shields.io/badge/Language-R-276DC3.svg?style=flat-square)](https://www.r-project.org/)
[![Dataset: NHANES](https://img.shields.io/badge/Dataset-NHANES%20(n%3D2,925)-0052CC.svg?style=flat-square)](https://www.cdc.gov/nchs/nhanes/about_nhanes.htm)
[![Report: PDF](https://img.shields.io/badge/Report-PDF-EC1C24.svg?style=flat-square)](./BP%20Variability%20Report.pdf)
[![Poster: PNG](https://img.shields.io/badge/Poster-PNG-8A2BE2.svg?style=flat-square)](./BP%20Variability%20Poster.png)

## Overview
This study investigates how physiological traits and lifestyle habits collectively influence **systolic blood pressure variability (BPV)**[cite: 3, 12]. Using clinical and survey records from the **National Health and Nutrition Examination Survey (NHANES)** ($N = 10,000$ raw; $n = 2,925$ cleaned complete cases), we develop, transform, and validate a multilinear regression model to quantify key determinants of average systolic blood pressure (`BPSysAve`)[cite: 2, 3, 12].

📄 **[Full Technical Report (PDF)](./BP%20Variability%20Report.pdf)** | 📊 **[Research Poster (PNG)](./BP%20Variability%20Poster.png)**

---

## Research Poster

![Research Poster](./BP%20Variability%20Poster.png)

---

## Study Variables

| Variable | Role | Description | Transformation Applied |
| :--- | :--- | :--- | :--- |
| **`BPSysAve`** | Response | Average systolic blood pressure (mmHg)[cite: 3, 12] | Inverse ($\text{BPSysAve}^{-1}$)[cite: 4, 6] |
| **`Gender`** | Predictor | Biological sex (`male` / `female`)[cite: 3, 5] | None (Binary indicator)[cite: 6, 12] |
| **`Age`** | Predictor | Age in years[cite: 3, 12] | Square root ($\sqrt{\text{Age}}$)[cite: 4, 6] |
| **`BMI`** | Predictor | Body Mass Index ($\text{kg/m}^2$)[cite: 3, 12] | Inverse square root ($1/\sqrt{\text{BMI}}$)[cite: 6, 12] |
| **`AlcoholYear`** | Predictor | Annual drinking frequency (0–365 days/yr)[cite: 3, 4] | Shifted log ($\log(\text{AlcoholYear} + 1)$)[cite: 6, 12] |
| **`PhysActiveDays`** | Predictor | Weekly physical activity (0–7 days/wk)[cite: 3, 5] | Square root ($\sqrt{\text{PhysActiveDays}}$) *(Excluded)*[cite: 6] |

---

## Methodology

1. **Diagnostics & Assumption Testing:** Evaluated preliminary linear regression assumptions via Response vs. Fitted scatterplots, pairwise predictor plots, residual plots, and Normal Q-Q plots[cite: 3, 12]. Verified absence of multicollinearity across all specifications ($\text{VIF} \in [1.01, 1.05]$)[cite: 4].
2. **Box-Cox Transformations:** Applied multi-variable Box-Cox transformations (see table above) to stabilize residual variance (heteroskedasticity), correct non-linearity in `BMI`, and resolve heavy-tailed Q-Q deviations[cite: 3, 4, 6].
3. **Manual & Automated Model Selection:** 
   * **Manual Testing ($\alpha = 0.05$):** Conducted omnibus ANOVA ($p < 2.2 \times 10^{-16}$), individual $t$-tests, and Partial $F$-tests[cite: 4, 6, 12]. Removed $\sqrt{\text{PhysActiveDays}}$ due to statistical insignificance ($p = 0.635$, Partial $F = 0.228 > 0.05$)[cite: 6, 12].
   * **Automated Validation:** Cross-validated using **All Possible Subsets**, **Forward Selection**, **Backward Elimination**, and **Stepwise Regression** (optimizing Adjusted $R^2$ and AIC)[cite: 4, 7, 12]. All four algorithms converged on the same 4-predictor model[cite: 7, 12].
4. **Influence Diagnostics:** Assessed leverage thresholds, standardized residuals, **Cook's Distance**, **DFFITS**, and **DFBETAS** to evaluate outliers and influential observations[cite: 4, 7].

---

## Final Model & Results

$$\widehat{\text{BPSysAve}^{-1}} = 8.728 \times 10^{-3} - 3.845 \times 10^{-4}\text{Gender}_{\text{male}} - 2.905 \times 10^{-4}\sqrt{\text{Age}} + 1.019 \times 10^{-2}\frac{1}{\sqrt{\text{BMI}}} - 2.475 \times 10^{-5}\log(\text{AlcoholYear} + 1)$$

| Term | Estimate | $p$-value ($t$-test) | Final VIF |
| :--- | :---: | :---: | :---: |
| **Intercept** | `8.728e-03`[cite: 7, 12] | `< 2e-16`[cite: 12] | — |
| **`Gender` (Male)** | `-3.845e-04`[cite: 7, 12] | `< 2e-16`[cite: 12] | `1.024`[cite: 12] |
| **`sqrt(Age)`** | `-2.905e-04`[cite: 7, 12] | `< 2e-16`[cite: 12] | `1.012`[cite: 12] |
| **`1 / sqrt(BMI)`** | `1.019e-02`[cite: 7, 12] | `< 2e-16`[cite: 12] | `1.038`[cite: 12] |
| **`log(AlcoholYear + 1)`** | `-2.475e-05`[cite: 7, 12] | `0.0114`[cite: 12] | `1.038`[cite: 12] |

### Key Takeaways
* **Clinical Findings:** Because the response is inverse-transformed ($\text{BPSysAve}^{-1}$), a negative coefficient corresponds to **higher systolic blood pressure**[cite: 7, 12]. Holding other factors constant, male sex, older age, higher BMI, and more frequent alcohol consumption are all significantly associated with elevated systolic BP[cite: 7, 12].
* **Model Fit & Limitations:** The final model achieves an Adjusted $R^2$ of **0.1826**, indicating that while demographic and lifestyle variables have a statistically significant impact, roughly 81.7% of systolic blood pressure variability is driven by unmeasured genetic, clinical, or environmental factors[cite: 7, 12]. Discrete banding in `AlcoholYear` residuals also reflects correlated errors inherent to self-reported survey data[cite: 7, 8, 12].

---

## Repository Structure

```text
blood-pressure-variability-analysis/
├── BP Variability Report.pdf       # Full technical manuscript and diagnostic appendix
├── BP Variability Poster.png       # Research poster summarizing pipeline and results
├── bp_variability_analysis.Rmd     # R Markdown source code for data cleaning and modeling
└── README.md                       # Project documentation
```

## Authors
**Alisha Pham**, **Katrina Sha**, **Dabin Choi**

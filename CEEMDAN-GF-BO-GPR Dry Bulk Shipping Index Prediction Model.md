# CEEMDAN-GF-BO-GPR Dry Bulk Shipping Index Prediction Model

This project implements a hybrid prediction model based on **Complete Ensemble Empirical Mode Decomposition with Adaptive Noise (CEEMDAN)**, **Gaussian Filter (GF)**, **Bayesian Optimization (BO)** and **Gaussian Process Regression (GPR)** for accurate forecasting of the Baltic Dry Index (BDI) and other shipping price indices. It addresses the challenges of predicting nonlinear, non-stationary, and highly volatile shipping indices, and demonstrates excellent prediction accuracy and robustness even in small-sample, high-noise scenarios.

## Core Innovations of the Model

1. **Signal Decomposition and Denoising**: Decompose the index series into different Intrinsic Mode Functions (IMFs) via CEEMDAN, partition high and low-frequency components using sample entropy, and apply Gaussian filtering to high-frequency noise components for denoising while preserving effective features.
2. **Customized Kernel Functions**: Design adaptive GPR kernel function combinations for high and low-frequency components respectively to accurately capture short-term fluctuations of high frequencies and medium-to-long-term trends of low frequencies.
3. **Bayesian Hyperparameter Optimization**: Globally optimize GPR kernel function hyperparameters through BO to enhance the model's adaptability to shipping data, and visualize the optimization process via partial dependence plots.
4. **Direct Processing of Raw Data**: Eliminates the need for numerical stabilization operations such as logarithmic transformation, ensuring predicted indicators are highly aligned with the actual business needs of the shipping market.
5. **Small-Sample Robustness**: Outperforms traditional models significantly even with an extremely small subset (90 samples), addressing the issue of data sparsity in practical scenarios.

### Explanation of Key Steps

1. **CEEMDAN Decomposition**: Generate 200 groups of noisy signals and iteratively decompose to obtain IMF components with a reconstruction error as low as  $1.82 ×10^{-12}$ , ensuring decomposition completeness.
2. **Component Partition**: Using the mutation point of sample entropy as the boundary, the first 2 high-entropy IMFs are classified as high-frequency components (accounting for 0.4637% of variance), and the rest as low-frequency components (accounting for 99.5363% of variance), where low-frequency components dominate the index trend.
3. **Gaussian Filtering**: Optimal kernel width  $\\sigma=1.0$  achieves the optimal balance between noise suppression and feature preservation, outperforming other filtering methods such as wavelet thresholding and moving average.
4. **GPR Kernel Function Design**

   * High-frequency components: Matern 0.5 + White Noise (capturing abrupt fluctuations and random noise)
   * Low-frequency components: Linear×Periodic + Matern 2.5 + White Noise (capturing linear trends, periodicity, and smooth features)

5. **Bayesian Optimization**: Conduct global search for kernel function hyperparameters with the goal of minimizing prediction error, and obtain the optimal hyperparameter combination after iterative convergence.

## Experimental Datasets

* **Core Dataset**: Baltic Dry Index (BDI), from May 11, 2021 to July 23, 2025, totaling 1046 daily data points, covering multiple market phases including peak, crash, rebound, and downward trend.
* **Validation Datasets**: China Containerized Freight Index (CCFI), Shanghai Containerized Freight Index (SCFI), Shanghai Containerized Freight Index Settlement (SCFIS), with 244 weekly data points each.
* **Data Sources**: BDI from [Baltic Exchange](https://www.balticexchange.com); CCFI/SCFI/SCFIS from [Shanghai Shipping Exchange](https://www.sse.net.cn).

## Model Performance Metrics

Five metrics (**RMSE, MAE, MAPE%, R², DR% (Directional Accuracy)**) are used for comprehensive evaluation. Core performance (BDI test set) is as follows:

|Metric|Value|Description|
|-|-|-|
|RMSE|15.4496|Root Mean Square Error (smaller = higher accuracy)|
|MAE|11.5435|Mean Absolute Error|
|MAPE%|0.7417%|Mean Absolute Percentage Error|
|R²|0.9982|Coefficient of Determination (close to 1 = high fitting degree)|
|DR%|84.9840%|Directional Prediction Accuracy (above 80%)|

### Advantages over Comparative Models

Compared with GPR, CEEMDAN-GPR, SVR, GRU, CNN and other models, this model:

* Reduces RMSE by **37.78%** or more on the BDI test set, and improves directional accuracy by over 10%;
* Achieves an RMSE of only 22.8670 with 90 small samples, far lower than BLR (78.5882), SVR (78.0838), and GRU (66.2286);
* Delivers R²>0.987 and DR%>80.8% across multi-index validation (CCFI/SCFI/SCFIS).

## Robustness Verification

1. **Rolling Window Test**: 365-day training / 30-day testing, with sliding windows covering the entire market cycle. Even during extreme volatility periods (November 2023), it maintains R²=0.9554 and DR%=82.76%, with no significant difference in error variance (Levene’s test p=0.3210>0.05).
2. **Noise Resistance Test**: Adding 0~30% Gaussian white noise to high-frequency components results in only a 2.3810% increase in RMSE at 30% noise level, with R² remaining at 0.9857, demonstrating excellent anti-interference capability.
3. **Multi-Index Adaptability**: Achieves optimal performance on CCFI/SCFI/SCFIS, adapting to the fluctuation characteristics of different types of shipping price indices.

## Environment and Dependencies

The model is implemented in Python with core dependent libraries:

* Signal Processing: `emd` (CEEMDAN), `scipy` (Gaussian filtering, Fourier transform)
* Machine Learning: `scikit-learn` (GPR, model evaluation), `bayesian-optimization` (Bayesian optimization)
* Data Processing: `pandas`, `numpy`
* Visualization: `matplotlib`, `seaborn` (partial dependence plots, prediction result visualization)
* Statistical Analysis: `scipy.stats` (sample entropy, Levene’s test, residual analysis)

## Model Application Scenarios

1. **Shipping Enterprises**: Freight rate trend prediction, route adjustment, decision-making on contract signing timing, reducing market volatility risks.
2. **Investors**: Investment decision-making and risk hedging in the dry bulk shipping market.
3. **Industry Research**: Analysis of global economic and bulk commodity trade trends (BDI is a leading indicator of the global economy).
4. **Extended Applications**: Transferable to forecasting logistics freight volume, vessel arrival time, and other bulk commodity price indices.

## Limitations and Future Work

### Limitations

* Suffers from cumulative component prediction errors with large samples (>10000), affecting overall accuracy;
* Kernel function combinations are manually designed without adaptive dynamic adjustment.

### Future Work

1. Integrate deep learning models such as LSTM/Transformer to address large-sample error accumulation and enhance complex sequence modeling capabilities;
2. Introduce an adaptive kernel function combination strategy to automatically select optimal kernel functions based on data characteristics;
3. Incorporate external features such as geopolitics, bulk commodity supply and demand, and port congestion to further improve prediction accuracy;
4. Extend to multi-step forecasting to meet the medium-to-long-term decision-making needs of practical business.

## References

This model is implemented based on the 2026 Applied Mathematical Modelling paper *"*[*Forecasting dry bulk freight index for maritime decision-making: A hybrid signal decomposition and probabilistic modeling approach*](https://doi.org/10.1016/j.apm.2025.116578)*"*, with core ideas and experimental results derived from this research.

## Disclaimer

This model is solely a tool for academic research and business reference. Prediction results are affected by sudden market factors (geopolitics, natural disasters, policy adjustments) and do not constitute a direct basis for any investment or operational decisions.




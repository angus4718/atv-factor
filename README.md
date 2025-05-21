# Trading Factor Construction for JPX Tokyo Stock Exchange

This repository contains the implementation of Section 4.1 **"Trading Factor"** from the paper [A Behavioral Factor Model for China A-share Market](https://ssrn.com/abstract=4134890) by Liu et al. (2024). The code constructs the **Trading Factor** using data from the **JPX Tokyo Stock Exchange** for the period **2017 January to 2022 June**.

---

## Overview

### **Paper Context**
The trading factor is designed to capture mispricing caused by excessive trading volumes, which remain unexplained by the market trading volume. Behavioral biases, such as gambling preferences and perceived information advantages, are considered primary drivers of excessive trading.

The trading factor is calculated by:
1. Taking a **long position** in stocks with the **lowest Abnormal Trading Volume (ATV)**.
2. Taking a **short position** in stocks with the **highest ATV**.

The methodology is adapted to the JPX Tokyo Stock Exchange using:
- **Monthly stock trading data** (adjusted for splits and other corporate actions).
- **Market capitalization** as a size factor.

---

## Methodology

1. **Abnormal Trading Volume (ATV)**:
   - ATV is calculated as the residual from a time-series regression of a stock's log trading volume on the market's log trading volume:
     \[
     \log(V_{i,t}) = \alpha_i + \beta_i \log(V_{m,t}) + \epsilon_{i,t}
     \]
   - The residual (\(\epsilon_{i,t}\)) represents the abnormal trading volume (ATV).

2. **Portfolio Construction**:
   - Stocks are divided into size-based groups: **Small (S)** and **Big (B)**.
   - Stocks are independently classified into trading-based groups: **Under-traded (U)** and **Over-traded (O)**.
   - Four size-ATV portfolios are created: **SO**, **SU**, **BO**, **BU**.

3. **Trading Factor Calculation**:
   - Value-weighted portfolio returns are calculated for each portfolio.
   - The trading factor is computed as:
     \[
     \text{Trading Factor} = \frac{(\text{SU} + \text{BU}) - (\text{SO} + \text{BO})}{2}
     \]

---

## Data Sources

1. **JPX Tokyo Stock Exchange Data**:
   - **Stock Prices**: `train_files/stock_prices_1.csv`, `train_files/stock_prices_2.csv`, `train_files/stock_prices_3.csv` and `supplemental_files/stock_prices.csv`
   - **Stock List (Market Capitalization)**: `train_files/stock_list.csv`

2. **Time Period**:
   - January 2017 to June 2022

---

## Installation

1. Clone the repository:
   ```bash
   git clone https://github.com/angus4718/atv-factor.git
   cd atv-factor
   ```

2. Install dependencies:
   ```bash
   pip install -r requirements.txt
   ```

---

## Key Python Code

### Abnormal Trading Volume (ATV) Calculation:
```python
def compute_residuals(group):
    y_i = group['log_volume']
    X_subset = X.loc[group.index]
    model = sm.OLS(y_i, X_subset)
    results = model.fit()
    return results.resid
```

### Trading Factor Calculation:
```python
pivoted['TradingFactor'] = ((pivoted['SU'] + pivoted['BU']) - (pivoted['SO'] + pivoted['BO'])) / 2
```

---

## Results

The trading factor captures mispricing caused by excessive trading volumes in the JPX Tokyo Stock Exchange. I have not backtested the factor yet but I am personally unconvinced this factor can consistently outperform the market given the simple linearity assumption.

---

## References

- Liu, Jinpeng, **"A Behavioral Factor Model for China A-share Market"** (2024). [SSRN link](https://ssrn.com/abstract=4134890)
- AkihiroSugiyama, Chihiro Hio(Alpaca), Eiichiro Kaji, n-onishi, s-meitoma - JPX, Shun Takato, Shun Takato - JPX, Sohier Dane, and Tomoya Kitayama(Alpaca). JPX Tokyo Stock Exchange Prediction. https://kaggle.com/competitions/jpx-tokyo-stock-exchange-prediction, 2022. Kaggle.
---

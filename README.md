# 🌱 Soy Futures Mean-Reversion System — NOV/DIC Calendar Spread (Matba Rofex)

> Quantitative mean-reversion trading system for soy futures calendar spreads (NOV vs DIC contracts). Full pipeline: data ingestion, OLS regression, Engle-Granger cointegration test, ADF stationarity test, static and rolling z-scores, signal generation, and Excel export.

---

## 🇬🇧 English

### Strategy

Exploits the **mean-reverting behavior** of the NOV–DIC soy futures calendar spread on Matba Rofex.

The hypothesis: the spread between two consecutive soy contracts is cointegrated — it has a long-run equilibrium and deviations are temporary. When the z-score of the regression residual exceeds a threshold, the spread is expected to revert.

```
Signal logic:
  z < −1.5  →  BUY spread  (DIC cheap relative to NOV)
  z > +1.5  →  SELL spread (DIC expensive relative to NOV)
```

### Pipeline

```
NOVIEMBRE1.xlsx + DICIEMBRE2.xlsx
            │
            ▼
1. Align on common trading dates
2. OLS regression: DIC ~ const + β × NOV
3. Extract residuals
4. Engle-Granger cointegration test (coint)
5. ADF test on spread and residuals
6. Static z-score (full-sample mean/std)
7. Rolling z-score (60-obs adaptive window)
8. Signal generation on best available z-score
9. Reversion probability estimation
10. 6-panel visual dashboard
11. Excel export (data + stats + regression summary)
```

### Statistical tests

| Test | Purpose | Null hypothesis |
|---|---|---|
| **Engle-Granger** `coint()` | Are NOV and DIC cointegrated? | No cointegration |
| **ADF on spread** `adfuller()` | Is the raw spread stationary? | Unit root (non-stationary) |
| **ADF on residuals** | Is the regression residual stationary? | Unit root |

Rejecting H₀ in all three → strong evidence of mean reversion.

### Z-score hierarchy

The system selects the best available z-score automatically:

```
1. Rolling z-score on residual  ← preferred (adaptive)
2. Static z-score on residual   ← fallback
3. Static z-score on spread     ← last resort
```

### Visual dashboard (6-panel)

| Panel | Content |
|---|---|
| Spread (DIC − NOV) | Raw spread with mean line |
| Z-score | Selected z-score with ±threshold bands |
| Residuals + signals | Residuals with BUY (▲) and SELL (▼) markers |
| Residual histogram | Distribution of regression residuals |
| Q-Q plot | Normality check on residuals |
| Summary stats | Cointegration p-values, signal counts, reversion probability |

### Reversion probability

Empirical estimate: percentage of times the z-score reverts toward zero in the next period after crossing the threshold.

```python
if |z_t| > threshold and |z_{t+1}| < |z_t|:
    count as reversion
prob_reversion = reversions / total_extremes × 100
```

### Configuration

```python
CONFIG = {
    "excel_dic": "DICIEMBRE2.xlsx",
    "excel_nov": "NOVIEMBRE1.xlsx",
    "fecha_col": 0,           # Date column index
    "precio_col": 11,         # Price column index
    "zscore_umbral": 1.5,     # Signal threshold
    "coint_pvalue_max": 0.05, # Cointegration significance level
    "rolling_window": 60,     # Adaptive z-score window
    "output_excel": "resultado_reversion.xlsx",
}
```

### Requirements

```bash
pip install pandas numpy matplotlib scipy statsmodels xlsxwriter openpyxl
```

### Usage

```bash
python quant_dolares_y_soja.py
```

### Output

- Console: cointegration p-value, ADF p-values, R², β coefficient, signal counts
- Plot: 6-panel dashboard
- Excel: `resultado_reversion.xlsx` with 4 sheets (data, cointegration, metrics, regression summary)

### Skills demonstrated

- Calendar spread mean-reversion strategy on commodity futures
- Engle-Granger cointegration test (2-series, `statsmodels.tsa.stattools.coint`)
- Augmented Dickey-Fuller stationarity test on spread and OLS residuals
- OLS regression with `statsmodels` (hedge ratio estimation)
- Static and rolling z-score signal generation
- Empirical reversion probability estimation
- Multi-panel visualization: spread, z-score, signals, histogram, Q-Q plot
- Multi-sheet Excel export with regression summary table

---

## 🇦🇷 Español

### Estrategia

Explota el **comportamiento de reversión a la media** del spread calendario NOV–DIC de futuros de soja en Matba Rofex.

La hipótesis: el spread entre dos contratos consecutivos de soja está cointegrado — tiene un equilibrio de largo plazo y las desviaciones son temporarias. Cuando el z-score del residuo de regresión supera un umbral, se espera que el spread revierta.

```
Lógica de señales:
  z < −1.5  →  COMPRAR spread  (DIC barato relativo a NOV)
  z > +1.5  →  VENDER spread  (DIC caro relativo a NOV)
```

### Tests estadísticos

| Test | Propósito | Hipótesis nula |
|---|---|---|
| **Engle-Granger** `coint()` | ¿NOV y DIC están cointegrados? | Sin cointegración |
| **ADF sobre spread** | ¿El spread crudo es estacionario? | Raíz unitaria |
| **ADF sobre residuos** | ¿El residuo de regresión es estacionario? | Raíz unitaria |

Rechazar H₀ en los tres → evidencia fuerte de reversión a la media.

### Skills que demuestra

- Estrategia de reversión a la media en spread calendario de futuros de commodities
- Test de cointegración de Engle-Granger (2 series, `statsmodels`)
- Test ADF de estacionariedad sobre spread y residuos OLS
- Regresión OLS con `statsmodels` (estimación del hedge ratio)
- Generación de señales por z-score estático y rolling
- Estimación empírica de probabilidad de reversión
- Visualización multi-panel: spread, z-score, señales, histograma, Q-Q plot
- Exportación Excel multi-hoja con resumen de regresión

---

## Author

**unabomber1618** · [github.com/unabomber1618](https://github.com/unabomber1618)

> *Quantitative mean-reversion system for soy futures calendar spreads — cointegration-tested, z-score signaled, Matba Rofex.*

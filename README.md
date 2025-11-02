# stir_policy_path_predictor


# STIR Policy-Path & Fair-Value OIS Model

A Python model that links **macroeconomic surprises** (CPI, unemployment, ISM) to **expected FOMC policy moves** via a multinomial logit, then compounds those expectations to build a **fair-value SOFR OIS curve**.  
This allows you to quantify **policy-path mispricing** between your model and the market-implied OIS curve.

---

## 🧠 Concept
- Fit a **multinomial logit** to historical Fed decisions as a function of macro data.
- Use it to forecast probabilities of each possible move (`-50`, `-25`, `0`, `+25`, `+50`).
- Convert probabilities to **expected rate changes** and build a meeting-by-meeting **policy path**.
- Compound expected daily rates to derive **fair OIS discount factors**.
- Compare your model’s fair-value OIS rates vs **market OIS** to detect mispricing.

---

## ⚙️ Workflow
1. Simulate or import macroeconomic surprise data (`CPI`, `UNEMP`, `ISM`).
2. Fit a multinomial logit model:
   ```python
   res = sm.MNLogit(y, sm.add_constant(X)).fit()
   ```
3. Predict expected policy moves for future meetings.
4. Build the stepwise expected rate path.
5. Compute fair-value OIS rates using discounted compounding.
6. Compare to market (synthetic or real) OIS rates.

---

## 📊 Example Output
```
Fair-value OIS curve:
   TenorM   FairOIS    MktOIS  Mispricing_bps
0       1  0.038694  0.039145        4.506071
1       3  0.038226  0.038300        0.734379
2       6  0.038646  0.038778        1.318372
3      12  0.038940  0.038889       -0.515545
4      24  0.039754  0.039548       -2.067892
```

A positive `Mispricing_bps` means the **market OIS is higher** than the model’s fair value →  
the model implies slightly **more rate cuts** (more dovish) than the market.

---

## 🧮 Math Highlights
- **Multinomial Logit:**  
  \[
  P_k = \frac{e^{β_k'X}}{\sum_j e^{β_j'X}}
  \]
- **Expected Move:**  
  \[
  E[\Delta r] = \sum_k P_k \times \text{Move}_k
  \]
- **Fair OIS Rate:**  
  \[
  R = \frac{1}{D} - 1 \quad\text{where}\quad D = \prod (1 + r_i/360)^{-1}
  \]

---

## 🧰 Requirements
```bash
python>=3.9
numpy
pandas
matplotlib
statsmodels
dateutil
```

---

## ▶️ Run
```bash
python3 stir_policy_path.py
```

---

## 📈 Outputs
- Regression summary of multinomial logit (policy reaction function)
- Expected moves per FOMC meeting
- Fair vs Market OIS curve table
- Plots:
  - **Fair vs Market OIS Curve**
  - **Expected Policy Path**

---

## 💬 Notes
- Current setup uses **synthetic OIS data** for demonstration.
- Replace with live SOFR OIS quotes or Fed Funds futures to run on real data.
- Positive mispricing = market tighter (model more dovish).  
  Negative mispricing = market looser (model more hawkish).

---

*Author: William Nelson*  
*STIR Policy-Path & OIS Pricing Research*

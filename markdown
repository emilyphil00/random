Below is a markdown document you can copy and paste directly into a JupyterHub markdown cell.

---

# Two Approaches to Estimate Realized Volatility and Average Correlation

When constituent weights vary daily, you can approximate the index’s realized volatility and extract an “average” correlation using one of two methods:

1. **Using average weights over a 21-day window and a single covariance matrix.**  
2. **Recovering an “average” realized correlation that’s consistent with the index’s volatility, by expressing the portfolio variance in a “single‐correlation” form.**

---

## 1. Using Average Weights Over a 21-Day Window and a Single Covariance Matrix

In this approach you:

- **Average the daily weights** over the 21-day window to form an effective weight vector.
- **Compute a single covariance matrix** over that same window from the asset returns.
- Then, use the standard portfolio variance formula.

### Steps

1. **Effective Weight Vector**

   Compute the average weight over the window:
   $$
   w_{\text{avg}} = \frac{1}{T} \sum_{t=1}^{T} w_t,\quad \text{with } T = 21.
   $$

2. **Asset Volatilities and Covariance Matrix**

   Compute each asset’s realized volatility:
   $$
   \sigma_i = \operatorname{std}\left(\{r_{i,t}\}_{t=1}^{T}\right).
   $$
   
   Form the covariance matrix using the asset volatilities and the correlation matrix \(\rho\):
   $$
   \Sigma = \operatorname{diag}(\sigma_1,\dots,\sigma_N)\,\rho\,\operatorname{diag}(\sigma_1,\dots,\sigma_N).
   $$

3. **Portfolio (Index) Variance and Volatility**

   Calculate the portfolio variance using the Markowitz formula:
   $$
   \sigma_{\text{index}}^2 = w_{\text{avg}}^\top\,\Sigma\,w_{\text{avg}},
   $$
   and the realized volatility is
   $$
   \sigma_{\text{index}} = \sqrt{w_{\text{avg}}^\top\,\Sigma\,w_{\text{avg}}}.
   $$

---

## 2. Recovering an “Average” Realized Correlation Consistent with the Index’s Volatility

Here, the idea is to first compute the true realized volatility of the index (using the actual daily index returns) and then “back out” an average correlation. We start with the fact that the portfolio variance can be decomposed as follows:

### Portfolio Variance Decomposition

The standard portfolio variance is given by:
$$
\sigma_{\text{index}}^2 = \sum_{i} w_i^2\,\sigma_i^2 + 2\sum_{i<j} w_i\,w_j\,\sigma_i\,\sigma_j\,\rho_{i,j}.
$$

Assuming all pairwise correlations are equal to a constant \(\bar{\rho}\), this simplifies to:
$$
\sigma_{\text{index}}^2 = \sum_{i} w_i^2\,\sigma_i^2 + 2\,\bar{\rho}\,\sum_{i<j} w_i\,w_j\,\sigma_i\,\sigma_j.
$$

### Steps

1. **Compute the True Realized Index Volatility**

   First, calculate daily index returns using the actual daily weights:
   $$
   r_{\text{index},t} = \sum_i w_{i,t}\,r_{i,t}.
   $$
   Then, over the 21-day window, compute the realized volatility:
   $$
   \sigma_{\text{index}} = \operatorname{std}\left(\{r_{\text{index},t}\}_{t=1}^{T}\right).
   $$

2. **Effective Weights and Individual Asset Volatilities**

   Derive an effective weight vector (for example, by averaging the daily weights):
   $$
   w_{\text{avg}} = \frac{1}{T}\sum_{t=1}^{T} w_t,
   $$
   and compute the asset volatilities \(\sigma_i\) as before.

3. **Decompose the Variance**

   Define the **diagonal term** (own variance contribution):
   $$
   D = \sum_{i} w_{\text{avg},i}^2\,\sigma_i^2,
   $$
   and the **off-diagonal term** (cross-asset contributions):
   $$
   O = \sum_{i<j} w_{\text{avg},i}\,w_{\text{avg},j}\,\sigma_i\,\sigma_j.
   $$

   Under the constant-correlation assumption, the portfolio variance is:
   $$
   \sigma_{\text{index}}^2 = D + 2\,\bar{\rho}\,O.
   $$

4. **Solve for the Average Correlation**

   Rearranging the above equation gives:
   $$
   \bar{\rho} = \frac{\sigma_{\text{index}}^2 - D}{2\,O}.
   $$

This “inverts” the variance decomposition to yield an implied average correlation that is consistent with the observed index volatility.

---

## Final Remarks

- **Approach 1** provides an approximation of the index volatility using average weights and a single covariance matrix. This method is simpler but may not fully capture the day-to-day weight variation.

- **Approach 2** uses the actual daily index returns to compute the realized volatility and then recovers an average correlation that, when plugged into the constant-correlation form of the portfolio variance, matches the observed index volatility.

Each method involves approximations, so the choice depends on whether you prefer to replicate the index’s daily performance exactly (Approach 2) or use a simpler, single-period approximation (Approach 1).

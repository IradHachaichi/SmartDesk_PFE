# Actuarial SLA Risk Model

## 1. Objective

The SmartDesk actuarial module estimates the distribution of potential monthly SLA breach hours at client and portfolio levels.

It adapts the collective frequency–severity framework to an operational service-management context.

- **Frequency:** number of tickets that breach their contractual SLA threshold.
- **Severity:** excess processing duration beyond the applicable threshold.
- **Aggregate risk:** total monthly breach duration.

The model produces risk measures expressed in breach hours.

## 2. Aggregate Model

The aggregate monthly breach amount is:

$$
S = \sum_{i=1}^{N} X_i
$$

where:

- $N$ is the number of SLA breaches during the month.
- $X_i$ is the excess duration associated with breach $i$.
- $S$ is the aggregate monthly breach duration.

Frequency and severity are modelled separately because they describe different components of the risk.

## 3. Frequency Modelling

### 3.1 Monthly reconstruction

Ticket counts are reconstructed as monthly time series by client and request type. Months with no tickets are retained as zero observations to avoid upward bias in estimated frequency.

### 3.2 Candidate distributions

Two discrete distributions are compared:

- **Poisson:** mean and variance are equal under the model.
- **Negative Binomial:** allows overdispersion.

The Akaike Information Criterion (AIC) guides selection, with the simpler Poisson model favoured in case of a tie.

### 3.3 Bühlmann–Straub credibility

The credibility estimate combines individual client experience with the collective portfolio experience. The weight assigned to the individual estimate depends on the available exposure.

This allows more experienced segments to rely more heavily on their own data, while less experienced segments benefit from collective information.

### 3.4 Priority allocation

Monthly ticket counts are allocated across priority levels using historical priority proportions. This produces the frequency structure required by the severity and simulation stages.

## 4. Severity Modelling

Severity is based on ticket processing duration relative to the applicable contractual SLA threshold.

### 4.1 Candidate distributions

The project considers four positive-support distributions:

- Lognormal
- Weibull
- Gamma
- Pareto

Parameters are estimated using Maximum Likelihood Estimation, with the location parameter fixed at zero. AIC is used to compare admissible fits.

Pareto fits with an estimated shape parameter below one are excluded because they imply an infinite theoretical mean.

### 4.2 Segmentation and sparse data

Severity distributions are fitted by contractual threshold group. Segments with comparable thresholds may be pooled under a relative tolerance criterion.

When a segment has too few observations for a reliable fit, a proxy segment from the same client is used.

### 4.3 Goodness-of-fit

The selected distributions are assessed using:

- Kolmogorov–Smirnov (KS)
- Cramér–von Mises (CvM)
- Anderson–Darling (AD)

Bootstrap correction accounts for parameter estimation on the same observations used for goodness-of-fit assessment.

### 4.4 Tail behaviour

Extreme Value Theory is investigated because large severity observations can strongly influence VaR and TVaR.

A severity cap is introduced as an explicit modelling assumption to limit the influence of poorly supported extreme extrapolations. Its impact is examined through sensitivity analysis.

## 5. Calibration

A segment-level stop-loss calibration factor is estimated so that the simulated average excess above the contractual threshold reproduces the corresponding historical average excess.

This aligns modelled breach amounts with observed breach experience.

## 6. Monte Carlo Simulation

The model uses a hierarchical Monte Carlo simulation with 10,000 trajectories and a fixed random seed.

Each simulated trajectory:

1. Draws monthly ticket frequency.
2. Allocates tickets across priority levels.
3. Draws processing durations from selected severity distributions.
4. Applies contractual SLA thresholds.
5. Calculates excess duration for each breach.
6. Aggregates breach hours by client.
7. Aggregates client results at portfolio level.

Client-level results are aggregated trajectory by trajectory. This preserves the simulated portfolio distribution and allows diversification effects to emerge from the scenarios. It avoids treating the sum of client-level VaRs as portfolio VaR.

## 7. Risk Measures

### 7.1 Value at Risk

$$
VaR_{\alpha}(S)=\inf\{s:P(S\leq s)\geq\alpha\}
$$

At a 99% confidence level, VaR is the empirical monthly breach-hour threshold that is exceeded in approximately 1% of simulated scenarios.

### 7.2 Tail Value at Risk

The Tail Value at Risk (TVaR) at confidence level $\alpha$ is defined as:

\mathbb{E}\left[
S \mid S \geq \mathrm{VaR}_{\alpha}(S)
\right]
$$

TVaR measures the average aggregate breach exposure in scenarios at or beyond the VaR threshold, according to the project's empirical tail convention.

Both measures are estimated from the simulated trajectories.

## 8. Validation and Sensitivity Analysis

| Area | Validation approach |
|---|---|
| Frequency | AIC comparison and monthly trend analysis |
| Credibility | Individual versus collective estimates |
| Severity | Distribution fitting and goodness-of-fit tests |
| Tail behaviour | Extreme-value analysis and cap sensitivity |
| Calibration | Simulated versus historical average excess |
| Simulation | Convergence checks across simulation sizes |
| Portfolio | Inter-client dependence analysis |
| Priority allocation | Stability of historical priority proportions |

Sensitivity analyses examine:

- Historical versus recent frequency estimates.
- Alternative severity-cap factors.
- Frequency–severity dependence.
- Independence assumptions between clients.
- Stability of the priority mix.

## 9. Findings

The analysis identifies:

- Significant differences in risk exposure across clients.
- Concentration of portfolio risk in a major contributor.
- A material diversification effect when client scenarios are aggregated.
- Sensitivity of extreme-risk estimates to frequency trends and severity-tail assumptions.

Numerical portfolio and client-level results are intentionally withheld from this public documentation.

## 10. Limitations

The model's limitations include:

- Historical frequency series are not stationary across all segments.
- Some severity segments contain limited observations.
- Extreme-tail estimates depend on assumptions, including the severity cap.
- Positive frequency–severity dependence is observed in an important segment but is not reproduced by the independent simulation framework.
- Financial penalty parameters are illustrative and do not represent actual contractual exposure.

These limitations are part of the model interpretation and should accompany any discussion of its risk measures.

## 11. Technical Concepts

Collective risk modelling; Poisson and Negative Binomial distributions; Bühlmann–Straub credibility; Maximum Likelihood Estimation; goodness-of-fit testing; bootstrap correction; Extreme Value Theory; stop-loss calibration; Monte Carlo simulation; VaR; TVaR; sensitivity and convergence analysis.

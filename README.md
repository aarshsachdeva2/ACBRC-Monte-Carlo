# ACBRC Monte Carlo Pricing Engine

## Overview

This project implements and compares multiple Monte Carlo techniques for pricing an **Auto Call Barrier Reverse Convertible (ACBRC)**, a complex path-dependent structured financial product.

The primary objective is not only to estimate the expected payout of the product but also to identify the simulation technique that achieves the **lowest standard error (SE)** while maintaining computational efficiency.

The following methods are implemented:

* Vanilla Monte Carlo (VMC)
* Antithetic Variates (AV)
* Control Variates (CV)
* Sobol Quasi-Monte Carlo (QMC)

The project evaluates each approach using:

* Mean payout estimate
* Standard Error (SE)
* Computation time
* Cost metric (SE² × Time)

---

## Product Description

An Auto Call Barrier Reverse Convertible (ACBRC) is a path-dependent structured product containing:

### Autocall Feature

At predefined observation dates, the product terminates early if all underlying assets remain above the autocall threshold.

### Barrier Feature

A continuously monitored barrier is checked throughout the product lifetime using Brownian Bridge interpolation. If the barrier is breached, the final payout depends on the terminal performance of the underlying assets.

Because the payoff depends on:

* Multiple correlated assets
* Continuous barrier monitoring
* Early redemption logic
* Entire price paths

closed-form pricing methods are generally unavailable, making Monte Carlo simulation the preferred approach.

---

## Mathematical Framework

### Asset Dynamics

Underlying assets are simulated using Geometric Brownian Motion (GBM):

* Correlated normal shocks generated through Cholesky decomposition
* Multi-asset path simulation
* Brownian Bridge interpolation for between-step barrier monitoring

### Variance Reduction Techniques

#### Vanilla Monte Carlo (VMC)

Baseline Monte Carlo estimator.

#### Antithetic Variates (AV)

Uses negatively correlated random variables:

* Z → -Z
* U → 1-U

to reduce estimator variance.

#### Control Variates (CV)

Uses analytically tractable products as control variables:

* European Put Option
* Discounted Terminal Asset Value

Regression-based beta estimation is used to construct the variance-reduced estimator.

#### Sobol Quasi Monte Carlo (QMC)

Low-discrepancy Sobol sequences replace pseudo-random sampling to improve convergence.

To improve Sobol performance in high dimensions:

* PCA Transformation
* Cholesky-PCA Transformation

are implemented.

---

## Repository Structure

```text
.
├── VMC.py                # Vanilla Monte Carlo pricing engine
├── CV.py                 # Control Variates implementation
├── data.py               # Generation of Z and U tensors, Brownian Bridge support
├── my_config.py          # Product and simulation parameters
├── Execution.ipynb       # Execution of VMC, AV and CV experiments
├── Executeqmc.ipynb      # Sobol Quasi-Monte Carlo experiments
├── README.md
└── LICENSE
```

---

## Key Parameters

| Parameter         | Value                |
| ----------------- | -------------------- |
| Underlying Assets | 3                    |
| Investment Value  | SGD 1000             |
| Contract Tenure   | 1.25 Years           |
| Coupons           | 8% Annual            |
| Barrier Level     | 59% of Initial Price |
| Autocall Dates    | 0.5, 0.75, 1.0 Years |
| Trading Days      | 252                  |

---

## Results

### Classical Monte Carlo Methods

Control Variates achieved the strongest variance reduction among classical methods:

* ~30–40% reduction in standard error versus VMC
* Lowest SE² × Time cost metric
* Most efficient classical variance reduction technique

Antithetic Variates achieved:

* ~15–20% standard error reduction
* Lower computational overhead than Control Variates

### Sobol Quasi Monte Carlo

Sobol-based QMC delivered the best overall precision.

Key findings:

* Similar mean payout estimate to VMC
* More than 100× improvement in standard error
* Significantly lower precision-to-cost ratio despite longer runtime

---

## Technologies Used

* Python
* NumPy
* SciPy
* Pandas
* Matplotlib
* Jupyter Notebook

---

## References

* Glasserman, *Monte Carlo Methods in Financial Engineering*
* Hull, *Options, Futures and Other Derivatives*
* Natenberg, *Option Volatility and Pricing*

---

## Author

Aarsh Sachdeva

Master's Project:
**Modeling Path Dependent Financial Products Using Monte Carlo Approaches**

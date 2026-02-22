# Examples
# UHECR Forward Model — Lognormal Acceleration + Energy Losses (Fit to Auger Xmax)

This repository contains a reproducible Python workflow that simulates ultra-high-energy cosmic ray (UHECR) acceleration and propagation, then fits model parameters to Pierre Auger Observatory air-shower observables:
- mean depth of shower maximum **⟨Xmax⟩**
- shower-to-shower fluctuations **σ(Xmax)**

The implementation is designed as an end-to-end **data science pipeline**:
sampling → forward model → feature/binning → objective function → numerical optimization → diagnostic plots.

---

## What this code does

### 1) Forward model (simulation)
For each Monte Carlo particle:
- Sample a nuclear species from an abundance template (H, He, C, ..., Fe)
- Sample an initial energy `E_init` (Gamma distribution)
- Sample an acceleration efficiency `η` from a **lognormal distribution**
- Compute accelerated energy:
  \[
  E_{\rm acc} = E_{\rm init} + Z \, \eta \, V_{\max}
  \]
- Apply simplified energy-loss prescriptions over a comoving distance `d` (Mpc):
  - Bethe–Heitler pair production (approximate attenuation)
  - Photodisintegration (approximate attenuation)
  - Adiabatic energy losses via a redshift proxy

Output: final energies and masses for the simulated population.

### 2) Binned observables
The code bins events in energy (log10E) around reference bin centers and computes:
- `⟨Xmax⟩(E)` via a mass-dependent parametrization
- `σ(Xmax)(E)` from the Monte Carlo distribution

### 3) Parameter fitting
Fits the lognormal parameters of `η`:
- `log_eta_mu` (mean in log-space)
- `log_eta_sigma` (scatter)

The objective is a χ² that compares model predictions to Auger data:
- `⟨Xmax⟩` with errors
- `σ(Xmax)` with errors

Optimization is performed with `scipy.optimize.minimize` using Nelder–Mead.

### 4) Outputs
For each (V_max, d) case the script:
- prints best-fit parameters and χ²
- generates a diagnostic figure:
  - ⟨Xmax⟩ vs log10(E/eV) (data + model + reference p/Fe curves)
  - σ(Xmax) vs log10(E/eV) (data + model)

Saves: `fit_example.png`

---

## Repository structure

- `fit_eta_forward_model.py` (or your script name): main executable
- `fit_example.png`: output plot (generated)

---

## Requirements

Python 3.9+ recommended.

Install dependencies:

```bash
pip install numpy scipy matplotlib

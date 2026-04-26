# CLAUDE.md

## Status
Project is at the planning stage. The only file in this directory is
`main.pdf`, which is the project specification. No code, no scaffolding,
no dependencies installed yet. Do not assume any folders, modules, configs,
or build tools exist — if something is needed, ask or propose it.

## Project
Simulation study of a thermally drifting thermo-optical system. A Gaussian
beam is phase-shaped by an SLM, propagates through a medium whose refractive
index drifts via the thermo-optic effect, and produces an intensity pattern
at a detector. Two decoupled components:

1. **Thermal model** — a PINN surrogate (or classical heat-equation solver)
   that tracks the evolving temperature field T(r,t) and the resulting drift
   in the optical operator T_opt(t).
2. **RL controller** — updates the SLM phase φ(x,y) based on measured output
   intensity I(x,y) to maintain a target intensity pattern despite drift.

The thermal model explains drift; the controller compensates for it. Full
spec is in main.pdf.

## Environment
- Windows laptop. Use Windows-style paths and PowerShell/cmd commands when
  giving shell instructions, not bash/macOS.
- Working directory: C:\Users\cyrus\Projects\Summer Project\
- Python and tooling not yet installed; flag what's needed when it's needed.

## Notation (from main.pdf)
- T(r,t): temperature field [K]
- ΔT = T − T₀; n = n₀ + (dn/dT)·ΔT
- Δφ(x,y,t) = k ∫ Δn(x,y,z,t) dz: thermal phase perturbation
- φ(x,y): SLM phase (the control input)
- E_in: incident Gaussian field at SLM plane
- E_SLM = E_in · exp(iφ)
- T_opt(t) = T_opt,0 + ΔT_opt(t): time-varying transmission matrix
- E_out = T_opt(t) · E_SLM; I = |E_out|²
- Reward: r_t = I(x₀, y₀, t) at target location

## Settled (from main.pdf — don't reopen)
- Thermal model and controller are separate modules.
- Controller is RL, not iterative phase optimization.
- SLM is phase-only.
- Thermo-optic relation is linear in T to first order.
- Paraxial regime; detector measures intensity only.

## Open (genuinely undecided)
- PINN vs. FD heat solver as the production surrogate.
- RL algorithm choice (PPO / SAC / DDPG / other).
- Observation design for the RL agent.
- Project layout, build tool, dependency manager — not yet chosen.

## When writing code
- Don't hard-code physical constants (λ, w₀, dn/dT, n₀, ρ, cₚ, κ). Even
  before a config system exists, put them in clearly-marked module-level
  constants so they're easy to extract later.
- SI units throughout (m, s, K, W).
- 2D fields indexed as arr[iy, ix] (row=y, col=x).
- FFT: numpy convention (exp(−i k x) forward).
- Propagation along +z.
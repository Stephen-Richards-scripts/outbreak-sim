# PATHOGEN — Outbreak Simulation

An interactive, browser-based epidemic simulation using a stochastic agent-based SEIRD + Vaccinated model.

## Usage

Open `outbreak-sim.html` in any modern browser. No server, build step, or dependencies required.

## Model

Each individual is an agent in one of six states:

| State | Meaning |
|---|---|
| **S** Susceptible | Can be infected |
| **E** Exposed | Infected but not yet infectious (latent period) |
| **I** Infected | Infectious |
| **R** Recovered | Immune |
| **D** Dead | Removed from transmission |
| **V** Vaccinated | Immune from day 0 |

State transitions are stochastic each simulated day:

- **S→E** with probability β × (I / (N − D)), where β = R₀ × γ and D = current dead count
- **E→I** with probability σ = 1 / latent period
- **I→{D,R}** departs with probability γ; conditional on departure, dies with probability μ, recovers with probability (1 − μ)

The simulation ends when no Exposed or Infected individuals remain.

## Controls

| Parameter | Description |
|---|---|
| **R₀** (0.5–25) | Basic reproduction number — average secondary infections in a fully susceptible population |
| **Latent period** | Days from exposure to becoming infectious |
| **Infectious period** | Days an infected individual remains contagious |
| **Mortality rate** | Fraction of infected individuals who die |
| **Vaccinated fraction** | Population already immune at day 0 |
| **Population size** | Number of simulated individuals |
| **Days/second** | Simulation playback speed |

The **Hide Vaccinated** checkbox removes vaccinated dots from the population grid to reduce visual clutter at high vaccination fractions.

## Display

- **Population grid** — each dot is one individual, colored by state; infected agents show a glow halo
- **Time course chart** — SEIRD curves over simulated days; Y-axis scaled to the initial susceptible population; the vaccinated line is hidden when it would fall above the chart ceiling

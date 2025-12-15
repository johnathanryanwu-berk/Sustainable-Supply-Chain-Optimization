# Sustainable Multimodal Supply Chain Network Design

## Project Overview
This project optimizes a national logistics network for an AI infrastructure company, balancing shipping costs against strict carbon emission mandates ($CO_2$ caps) and sustainability quotas. 

Using **Mixed-Integer Linear Programming (MILP)** and **Benders Decomposition**, the model determines the optimal flow of heavy server equipment across the U.S. using a combination of Vans (Last Mile), Trucks (Regional), and Rail (Long Haul).

## Key Features
* **Mathematical Formulation:** Fixed-Charge Multicommodity Network Design problem.
* **Advanced Algorithms:** Implemented **Hybrid Benders Decomposition** with Penalty Methods and Warm Start injection to handle network disconnectivity and accelerate convergence.
* **Sensitivity Analysis:** quantified the "Green Premium," showing a 20.3% cost increase to achieve 70% clean transport usage.
* **Tech Stack:** Python, Gurobi Optimizer, Pandas, Matplotlib.

## Methodology
The problem was solved using two distinct approaches to validate optimality:
1.  **Branch-and-Cut (Gurobi):** Served as the baseline exact solver.
2.  **Benders Decomposition:** An iterative approach decomposing the problem into a Master Problem (Network Design) and Subproblem (Flow). 
    * *Enhancement:* Implemented "Warm Start" by seeding the Master Problem with heuristic solutions to bypass initial infeasibility.

## Results
| Scenario | Emissions Cap | Clean Quota | Total Cost |
| :--- | :--- | :--- | :--- |
| **Baseline** | Unconstrained | 0% | $68.57 M |
| **Target** | -10% Reduction | 50% | $71.55 M |
| **Aggressive** | -20% Reduction | 70% | $82.48 M |

**Key Finding:** Benders Decomposition converged to within **0.29%** of the exact Branch-and-Cut solution, mathematically validating the global optimum.


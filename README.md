# Optimizing a Multimodal Supply Chain Network  
Mixed-Integer Optimization | Sustainability-Constrained Logistics | Gurobi + Hybrid Benders Decomposition

---

## Executive Summary

This project designs and optimizes a national multimodal supply chain network for transporting equipment between major U.S. metropolitan cities.

**Objective:** Minimize total transportation cost while satisfying operational flow constraints, carbon emission caps, and corporate sustainability mandates.

The model determines:
- Which transportation routes to activate  
- Which transport modes to use (Van, Truck, Train)  
- How much flow to ship between cities  
- The cost impact of different decarbonization policies  

Key insight: Moderate emission reductions are relatively affordable, but aggressive sustainability targets lead to sharply increasing marginal costs — quantifying the “green premium” of logistics decarbonization.

---

## Business Motivation

Large logistics networks must balance:

- Cost efficiency  
- Capacity constraints  
- Environmental regulations  
- Clean transport mandates  

This project quantifies the tradeoff between cost and sustainability by simulating multiple policy regimes and evaluating their operational and financial impact.

The results provide a structured cost–carbon tradeoff curve for strategic decision-making.

---

## Network Design and Data

- Top 20 U.S. cities selected by population  
- New York, Los Angeles, and Chicago designated as primary supply hubs  
- Population used to estimate city-level demand  
- 2023 metropolitan export data used to allocate proportional supply  
- Geographic coordinates used to compute inter-city distances  
- Train mode restricted to routes ≥ 500 miles  

Transport modes:

- **Van:** Low capacity, moderate emissions  
- **Truck:** Medium capacity, higher emissions  
- **Train:** High capacity, low emissions  

---

## Mathematical Formulation (MILP)

### Sets

- N: Cities  
- M: Transportation modes  
- A ⊆ N × N × M: Valid directed arcs  

---

### Parameters

- d_i: Net supply/demand at city i  
- c_ijm: Variable cost per unit flow  
- f_ijm: Fixed activation cost  
- cap_ijm: Capacity  
- e_ijm: Emissions per unit flow  
- E_max: Emissions cap  
- alpha: Minimum clean-mode quota  

---

### Decision Variables

- x_ijm ≥ 0 : Flow on arc (i, j, m)  
- y_ijm ∈ {0,1} : Route activation indicator  

---

### Objective Function

Minimize:

Sum over all (i,j,m) in A of:

    f_ijm * y_ijm  +  c_ijm * x_ijm

---

### Constraints

**1) Flow Balance (for each city i):**

Sum over outgoing arcs x_ijm  
minus  
Sum over incoming arcs x_jim  
equals  
d_i

---

**2) Capacity Linking:**

x_ijm ≤ cap_ijm * y_ijm

---

**3) Emissions Cap:**

Sum over all arcs ( e_ijm * x_ijm ) ≤ E_max

---

**4) Clean Transport Quota:**

Let:

Total_Flow = Sum over all arcs x_ijm  
Clean_Flow = Sum over arcs where mode ∈ {Van, Train}

Constraint:

Clean_Flow ≥ alpha * Total_Flow


---

## Solution Approaches

### Method 1: Branch-and-Cut (Baseline MILP)

The full mixed-integer program was solved directly using Gurobi’s branch-and-cut algorithm.

- Solves the MILP via LP relaxation and branching  
- Generates cutting planes to tighten bounds  
- Achieved optimal solutions with negligible optimality gap  
- Served as benchmark solution  

---

### Method 2: Hybrid Benders Decomposition

To improve scalability and gain algorithmic insight, the MILP was decomposed into:

- **Master Problem (MP):** Binary route activation decisions \( y_{ijm} \) and auxiliary cost variable \( \eta \)  
- **Subproblem (SP):** Continuous minimum-cost flow optimization given fixed route decisions  

#### Why Standard Benders Can Struggle

In network design problems, standard Benders decomposition often encounters:

- Disconnected network topologies  
- Infeasible subproblems  
- Weak feasibility cuts  
- Slow convergence  

#### Hybrid Stabilization Enhancements

To address these issues, a stabilized hybrid approach was implemented:

**1. Penalty-Based Feasibility Stabilization**

High-penalty slack variables were introduced into subproblem balance constraints.  
This guarantees feasibility and generates informative optimality cuts instead of weak feasibility cuts.

**2. Warm-Start Injection**

The optimal branch-and-cut solution was injected into the master problem.

This:
- Seeds the algorithm with a high-quality network structure  
- Bypasses slow initial convergence  
- Accelerates bound stabilization  

#### Convergence Performance

The hybrid Benders approach converged within ~0.29% optimality gap of the exact MILP solution, validating computational robustness and decomposition effectiveness.

---

## Sustainability Scenarios and Results

| Scenario     | Emissions (kg) | Clean % | Cost (USD) |
|--------------|----------------|---------|------------|
| Baseline     | 4,345,046      | 46.4%   | 68.6M      |
| Target       | 3,910,541      | 56.5%   | 71.5M      |
| Aggressive   | 3,476,037      | 70.0%   | 82.5M      |

### Key Findings

- Hub-and-spoke routing reduces total cost relative to direct shipping  
- Rail usage increases as carbon caps tighten  
- The marginal cost of decarbonization increases nonlinearly  
- Moderate emission reductions are cost-efficient  
- Aggressive decarbonization significantly increases system cost  

---

## Technical Stack

- Python  
- Gurobi Optimizer  
- NumPy  
- Pandas  

---

## Computational Observations

- ~2,000 decision variables  
- ~1,100 constraints  
- Binary route activation significantly increases combinatorial complexity  
- Decomposition separates strategic network design from operational flow optimization  
- Warm-start significantly improves convergence speed  

---

## Future Extensions

- Multi-period planning  
- Stochastic demand modeling  
- Fuel price volatility sensitivity  
- Regional emissions policy variation  
- Capacity expansion planning  

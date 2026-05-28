# MOBAN Chapter 4 — Comprehensive Study Guide
# Fundamental Network Modelling Techniques: Optimisation

---

## PART 1: THEORY SUMMARY

---

### 1. Introduction & General Concepts

**Three phases for solving telecom network problems:**
1. **Mathematical modelling** — translate the realistic problem into a model (graph, MILP, …)
2. **Solving** — use a software solver (exactly or approximately)
3. **Interpretation** — is the solution realistic? Sensitivity? Comparison with alternatives?

**Key terminology (from the optimisation landscape diagram):**

| Term | Definition |
|------|-----------|
| **Objective function** f(x) | Scalar-valued function to minimise/maximise |
| **Decision variable** x | Can be continuous (real) or discrete (integer/boolean) |
| **Constraints** | Equality/inequality functions defining the feasible region |
| **Solution space X** | All feasible values of x (satisfying all constraints) |
| **Neighbourhood N(x)** | Set of solutions x' ∈ X adjacent to x (via a defined "move") |
| **Move / trajectory** | A single step from x to x'; successive moves define a trajectory |
| **Local optimum** x_lo | At least as good as all points in N(x_lo) |
| **Global optimum** x_go | At least as good as ALL points in X |

**General form of an optimisation problem:**
```
minimise or maximise: c(x)
subject to:
    aᵢ(x) = 0      i = 1, …, m₁
    aᵢ(x) ≤ 0      i = m₁+1, …, m₁+m₂
    aᵢ(x) ≥ 0      i = m₁+m₂+1, …, m
```
- **LP / heuristic split:** When c(.) and aᵢ(.) are all **linear** → use Linear Programming. Otherwise (or too complex) → use **heuristics**.

---

### 2. Linear Programming (LP) Techniques

#### 2.1 The LP Problem

**General form:**
```
minimise or maximise: z = (c₀ +) c₁x₁ + c₂x₂ + … + cₙxₙ
subject to:  functional constraints (=, ≤, ≥)
             sign restrictions: xᵢ ≥ 0, xᵢ ≤ 0, or xᵢ urs
```

**Four LP assumptions:**
- **Proportionality** — each variable's contribution is proportional to its value (no economies of scale)
- **Additivity** — total function = sum of individual contributions (no interaction between variables)
- **Divisibility** — variables can take fractional values
- **Certainty** — all parameters are known constants

**Four problem types (based on variable types):**

| Type | Variables | Complexity |
|------|-----------|------------|
| LP | All real-valued | Relatively easy (polynomial via Karmarkar; fast in practice via Simplex) |
| ILP / IP | All integer | Much harder (NP-complete) |
| MIP / MILP | Some integer, some real | Much harder |
| BIP / BILP | All boolean (0/1) | Much harder |

---

#### 2.2 Standard Form & Transformation

**Standard form** (what Simplex needs):
```
maximise:   z = c₁x₁ + c₂x₂ + … + cₙxₙ
subject to: equality constraints (=) and ≤ constraints
            xᵢ ≥ 0 for all i
```

**Four transformation steps (general → standard form):**

| Step | Situation | Action |
|------|-----------|--------|
| 1 | Minimisation problem | z' = −z (now maximise) |
| 2 | ≥ constraint | Multiply by −1 → becomes ≤ |
| 3 | xᵢ ≤ 0 (nonpositive) | Replace with xᵢ' = −xᵢ ≥ 0 |
| 4 | xᵢ urs (unrestricted) | Replace with xᵢ = xᵢ' − xᵢ'' where xᵢ', xᵢ'' ≥ 0 |

---

#### 2.3 The Simplex Algorithm

**Key insight:** For an LP problem, the optimal solution (if it exists) is always at a **corner point** (basic feasible solution, BFS) of the feasible region. The Simplex walks from one BFS to an adjacent better BFS.

**Translating to Simplex form — adding slack variables:**  
Each ≤ constraint `a₁x₁ + … + aₙxₙ ≤ b` becomes an equality by adding a slack variable s ≥ 0:
```
a₁x₁ + … + aₙxₙ + s = b
```

**Basic/non-basic variables:**
- n variables, m functional constraints → set n−m variables to 0 (= **nonbasic variables**)
- The remaining m variables are **basic variables** (solved from equality constraints)
- A basic solution where all basic variables ≥ 0 is a **basic feasible solution (BFS)**
- **Every corner point is a BFS, and vice versa**

**Simplex algorithm — general description:**

**Initialisation:**
1. Put LP into standard form S
2. Add slack variables → translated form T
3. Find a starting BFS (readily apparent if all constraints are ≤ with positive RHS; otherwise use Big M or Two-Phase method)

**Iteration (repeat until optimal):**
1. **Transformation:** Express z and basic variables as functions of nonbasic variables (row operations)
2. **Optimality test:** If all nonbasic variables have coefficients ≤ 0 in the z-expression → current BFS is optimal
3. **Basis change:**
   - **Entering variable:** nonbasic variable with the **largest positive coefficient** in z
   - **Leaving variable:** basic variable that **first reaches 0** as entering variable increases (minimum ratio test)

**Termination:** Optimal BFS found (optimal z and decision variable values).

**Worked example (from slides):**
```
max z = 8x₁ + 5x₂     →    Translated form with slacks s₁, s₂:
s.t. x₁ + x₂ ≤ 6           s₁ = 6 − x₁ − x₂
     9x₁ + 5x₂ ≤ 45         s₂ = 45 − 9x₁ − 5x₂
     x₁, x₂ ≥ 0             z  = 8x₁ + 5x₂
```
- **Starting BFS** (corner a): x₁=0, x₂=0, s₁=6, s₂=45, z=0
- **Iteration 1:** x₁ has largest positive coefficient (8). Min ratio: min(6/1, 45/9) = 5 → s₂ leaves. New corner d: x₁=5, x₂=0.
- **Iteration 2:** x₂ has positive coefficient. Min ratio: min(9/4, 1) → s₁ leaves. New corner c: x₁=3.75, x₂=2.25.
- **Optimal:** z=41.25 at (3.75, 2.25) — no more positive coefficients.

**Three special properties:**
1. **Selection criterion** — choosing variable with largest coefficient doesn't guarantee fastest improvement (but it's computationally cheap)
2. **Unbounded LP** — if the entering variable can increase without bound (no basic variable reaches 0) → LP is unbounded (z* = +∞)
3. **Cycling** — rare; avoid by randomly choosing leaving variable when tie occurs

**LP overview:**
- Simplex: widely used; theoretically NP (worst case) but P in practice for most instances
- Karmarkar's interior point algorithm: provably polynomial (P), but less used in practice

---

#### 2.4 ILP, MILP, BIP and Branch-and-Bound

**Why ILP is harder than LP:**
- LP optimum is at a corner point (BFS) — easy to find
- ILP optimum may be at an **interior point** of the LP feasible region — corner approach fails
- General ILP is **NP-complete**

**LP relaxation:**
- Replace all integer variables with continuous variables
- Solves faster; gives an **upper bound** z_LP* ≥ z_ILP* (for maximisation)
- If LP solution is all-integer → it IS the ILP optimum
- Rounding LP solution often gives infeasible or sub-optimal ILP solutions

---

**Branch-and-Bound (B&B) Algorithm:**

**Principle:** Implicitly enumerate all possible integer solutions by "divide and conquer."

**General description (flowchart):**
```
Initialise (LP relaxation of original problem)
↓
Bounding: solve LP relaxation of current subproblem → z_UB
  ↓
Fathoming? → YES → check if subproblems left → NO → STOP
  ↓ NO
Branching: split on a fractional variable
  ↓
Select next subproblem (e.g., LIFO rule)
  ↑←←←←←←←←←←←←←←←←←←←←←←←←←←↑
```

**Three fathoming criteria (when to stop branching on a subproblem):**
1. **Infeasible** — LP relaxation of subproblem has no feasible solution
2. **z_UBᵢ ≤ z_LB*** — upper bound of subproblem ≤ best incumbent integer solution found so far (can't improve)
3. **z_UBᵢ > z_LB* AND solution is all-integer** → new incumbent! Update z_LB* := z_UBᵢ; fathom (no need to branch further)

**B&B terminology:**
- **Incumbent solution:** best integer solution found so far (carries z_LB*)
- **Branching variable:** a fractional variable xᵢ = f → create two subproblems: xᵢ ≤ ⌊f⌋ and xᵢ ≥ ⌈f⌉

**Four B&B design choices:**
1. **Which variable to branch on?** (most fractional, most infeasible, etc.)
2. **Which subproblem to solve next?** LIFO (depth-first), breadth-first, best-first
3. **How to determine upper bounds?** Solve LP relaxation (standard), or simpler bounding techniques
4. **How to find lower bounds early?** Round LP solution to get feasible integer solution quickly

**B&B for MIP and BIP:**
- **MIP:** Only branch on fractional **integer-required** variables (real variables can stay fractional)
- **BIP:** Branching on a variable xᵢ = f → two subproblems: xᵢ = 0 and xᵢ = 1

**Other ILP techniques:**
- **Cutting plane:** Add constraints that cut off the fractional LP optimum without removing any integer solutions. Repeat until solution is integer or infeasible.
- **Early stopping:** Break off B&B when a sufficiently good integer solution is found. Compare z_LB* against z_LP* to gauge solution quality.

---

**Worked B&B example (from slides):**
```
max z = 8x₁ + 5x₂     (now with x₁, x₂ nonneg integers)
s.t. x₁ + x₂ ≤ 6
     9x₁ + 5x₂ ≤ 45
```
- **SP1 (LP relaxation):** z_LP=41.25 at (3.75, 2.25) → not integer → upper bound z_ILP* ≤ 41
- **Branch on x₁:** SP2: x₁≥4, SP3: x₁≤3
- **SP2:** z=41 at (4,1.8) → branch on x₂: SP4: x₂≥2 (infeasible!), SP5: x₂≤1
- **SP5:** z=40.56 at (4.44,1) → branch on x₁: SP6: x₁≥5, SP7: x₁≤4
- **SP7:** z=37 at (4,1) → **integer! Candidate. z_LB*=37**
- **SP6:** z=40 at (5,0) → **integer! Better candidate. z_LB*=40**
- **SP3:** z=39 at (3,3) → integer but z=39 < 40 → fathomed
- **Conclusion:** z_ILP* = 40 at (x₁,x₂) = (5,0)

---

#### 2.5 Workflow: Solving Network Problems with (MI)LP

```
Step 1: Analyse network problem → model as mathematical/graph problem
Step 2: Is it a known graph problem with an efficient graph algorithm?
Step 3: If NO → is it linear (or can be linearised)?
Step 4: If YES → assess complexity: LP or (M)ILP? #variables, #constraints, problem structure
Step 5: If feasible → use commercial (M)ILP solver
         - Simple solver (Excel, Python) for 'easy' problems
         - Dedicated solver (Gurobi, CPLEX) for 'hard' problems
Step 6: Check and interpret results
```

**Decision guide:**
| Problem type | Approach |
|-------------|----------|
| Polynomial graph problem (e.g., shortest path, min-cost flow) | Use graph algorithm |
| LP (continuous, linear) | Simplex — handles 1000s of variables/constraints |
| ILP/MIP (integer, linear) | B&B or cutting plane — feasible up to ~hundreds of variables |
| Too complex for exact methods | Heuristics (Section 3) |

---

### 3. Heuristics

#### 3.1 Introduction

**When to use heuristics:**
- Problems too large for exact methods (LP, B&B)
- Some variables are discrete → combinatorial explosion
- Nonlinear objective or constraints
- **Trade-off:** computation time vs. solution quality

**Goal:** Find a **good** local optimum using **reasonable** means — not necessarily the global optimum.

**Classification criteria:**

| Axis | Options |
|------|---------|
| Scope | Generic ↔ Problem-specific |
| Method | Search (trajectory) ↔ Constructive (build up) |
| Randomness | Stochastic ↔ Deterministic |
| Quality guarantee | With ↔ Without |

---

#### 3.2 Penalties

**Why penalties?** Three problems with hard constraints in search heuristics:
1. Hard to find an initial feasible solution
2. Complex moves required to stay feasible
3. Feasible region may be disconnected (can't reach all parts)

**Solution:** Add a **penalty function P(t)** to the objective:
```
F_total(x) = F(x) + P(t)   where P(t) measures constraint violation
```
**Benefits:**
- Converts hard constraints to soft constraints
- Simpler neighbourhood structure
- Can connect separated feasible regions
- Penalty weight can increase over time (low at start → easy exploration; high at end → forces feasibility)

---

#### 3.3 Dedicated Heuristic Example: PON Design

**Problem:** Connect N hubs to a service node at minimum fiber digging cost.

**Key observations:**
- Not a standard MST (not all graph nodes needed — **Steiner Tree Problem** → NP-hard as ILP)
- Promote **path reuse**: assign zero cost to already-dug street segments

**Final heuristic (problem-specific, deterministic, construction + search):**
1. Calculate all shortest paths between leaf nodes (Dijkstra)
2. Connect two closest leaf nodes (Dijkstra path)
3. Assign zero cost to used street segments
4. Connect next closest leaf nodes
5. Repeat until all connected
6. **Post-optimisation:** remove small sub-trees and reconnect

**Result on Ghent network (843 corners, 1281 segments, 44 hubs):**
- Solution in 2.7 seconds; within 0.7% of other algorithms
- Post-optimisation gives 3% cost reduction

---

#### 3.4 Steepest Descent

**Algorithm:**
```
(1) Initialise: select x₁ ∈ X; f* = f(x₁); x* = x₁
(2) n = 1; STOP = FALSE
(3) While (not STOP):
(4)     Find best x' in neighbourhood N(xₙ)
(5)     If f(x') ≤ f(xₙ):
(6)         xₙ₊₁ = x'; f* = f(x'); x* = x'
(7)     Else: STOP = TRUE
(8)     n = n+1
(9) Exit
```

**Properties:** Deterministic, generic, search heuristic
- Evaluates ALL neighbours at each step; picks the best
- **Fatal flaw:** Gets trapped in **local optima**
- **Remedy:** Restart from different initial solutions

---

#### 3.5 Tabu Search (TS)

**Principle:** Improvement over Steepest Descent — can escape local optima by allowing non-improving moves.

**Key ideas:**
- At each step: choose the **best** neighbour (even if worse than current)
- **Tabu list:** Previously visited solutions (or their attributes) are "forbidden" to prevent cycling
  - **Explicit tabu list:** Stores full solutions permanently → memory intensive
  - **Attributive tabu list:** Stores changed attributes temporarily → more common; uses **tabu tenure** (time an attribute stays tabu)
- **Final solution:** Best solution found during the entire search (not necessarily the last)

**Algorithm:**
```
(1) Initialise: x₁ ∈ X; f* = f(x₁); x* = x₁
(2) While (stopping rule not fulfilled):
(3)     Find best x' in N(xₙ) [excluding tabu solutions]
(4)     xₙ₊₁ = x'
(5)     If f(x') ≤ f*: f* = f(x'); x* = x'
(6)     Update tabu list
(7)     Update neighbourhood N(xₙ₊₁) based on tabu list
(8)     n = n+1
(9) Exit
```

**Enhanced TS features:**

| Feature | Description |
|---------|-------------|
| **Stochastic TS** | Examine only a random subset of N(x) (for large neighbourhoods) |
| **Reactive TS** | Adjust tabu tenure dynamically to prevent cycling |
| **Aspiration criteria** | Override tabu status if solution is exceptionally attractive |
| **Diversification** | Force search into unexplored regions (e.g., new move types) |
| **Intensification** | Return to promising regions for deeper exploration |

**TS example (ring network design):**
- Variables: binary (ring placed or not)
- Move: add or remove a ring
- Penalty: 5000 per uncovered node/link
- Tabu tenure: 3 (add ring), 6 (remove ring) — asymmetric because far more add options than remove
- Result: cost 35400 after 100 iterations

**Classification:** Deterministic, generic, search

---

#### 3.6 Simulated Annealing (SA)

**Analogy with metal annealing:**
| | Simulated Annealing | Metal Annealing |
|--|----|----|
| Goal | Minimise objective function | Minimise energy (perfect crystal) |
| Variables | Decision variables | Atom coordinates |
| Method | Random moves based on temperature T | Slow controlled cooling |

**Algorithm:**
```
(1) Initialise: x₁ ∈ X; f* = f(x₁); x* = x₁
(2) n=1; STOP=FALSE
(3) While (not STOP):
(4)     Draw x' at RANDOM from V(xₙ)   [random neighbour]
(5)     If f(x') ≤ f(xₙ): accept (x_{n+1} = x')
(6)         Update f* and x* if improved
(7)     Else: draw random p ∈ [0,1]
(8)         If p ≤ pₙ: accept x' anyway (x_{n+1} = x')   [Monte Carlo]
(9)         Else: reject (x_{n+1} = xₙ)
(10)    n = n+1; evaluate STOP
(11) Exit
```

**Acceptance probability** (Boltzmann distribution):
```
pₙ = { 1                          if f(x') ≤ f(xₙ)
      { e^{[f(x') - f(xₙ)] / T}   if f(x') > f(xₙ)
```
- High T → high pₙ → many "uphill" moves accepted (exploration)
- Low T → low pₙ → only improvements accepted (exploitation)
- Decreasing T over time → gradually more selective

**Cooling schedule (geometric):**
- After L trials (plateau length), multiply T by λ (cooling factor, 0 < λ < 1): T → λT
- **Parameters to tune:** T₀ (initial temperature), L (plateau length), λ (cooling factor)
- **Stopping rule:** No improvement during last plateau (or temperature reaches 0)

**SA vs. TS comparison:**
- TS examines **all** N neighbours per step; SA examines **one random** neighbour per step
- For N neighbours: 1 TS step ≈ N SA steps in time, but SA explores more diverse paths
- For **large neighbourhoods**: SA often preferred (stochastic TS also good)

**Classification:** Stochastic, generic, search

---

#### 3.7 Genetic Algorithms (GA)

**Principle:** Inspired by biological evolution — "survival of the fittest."

**Key difference from TS/SA:** Works with a **population** of solutions (not a single trajectory).

**Solution encoding:**
- Each solution is encoded as a **chromosome** (string of alleles)
- Simple: bit-string (for boolean variables)
- Custom encoding possible for better performance

**Three genetic operators:**

| Operator | Description |
|----------|-------------|
| **Crossover** | Combine two "parent" chromosomes at random cut points → two "child" chromosomes. Inherits good parts of both parents. Probability χ. |
| **Mutation** | Randomly flip one allele in a chromosome → "mutant." Drives search into new regions. Probability μ. |
| **Selection** | Choose N chromosomes from the N+M population (N parents + M children+mutants) with probability proportional to fitness f(xᵢ): pᵢ = f(xᵢ) / Σf(xⱼ). Survival of the fittest. |

**Algorithm:**
```
(1) Initialise: population X^(1) = {x₁^(1), …, x_N^(1)} ⊂ X
               f* = max{f(xᵢ^(1))}, x* = argmax{f(xᵢ^(1))}
(2) For g = 1, 2, …, G:
(3)     Crossover good parents (prob. χ) → children
(4)     Mutation of children and parents (prob. μ) → mutants
(5)     Update f* and x* if any new solution beats current best
(6)     Selection: choose N from N+M for next generation (fitness-proportional)
(7) Return f*, x*
```

**GA vs. TS/SA:**
- GA: **constructive** (crossover builds new solutions from parts) + **search** (mutation, selection)
- GA: works on building-block problems — good solution is a combination of good sub-solutions
- GA: naturally parallel (process population in parallel)
- Number of generations G specified in advance; hard to define stopping rule

**Classification:** Stochastic, generic, constructive + search

---

#### 3.8 Concluding Remarks

**Choosing a technique:**
- **Steepest Descent:** Simple; gets trapped in local optima; good baseline
- **Tabu Search:** Good for problems where full neighbourhood evaluation is feasible
- **Simulated Annealing:** Good for large neighbourhoods (random sampling)
- **Genetic Algorithms:** Good when good solutions are combinations of good sub-solutions

**Technique-specific configuration:**
- TS / SA: define (1) variables, (2) neighbourhood structure (the "move")
- GA: define (1) variables/encoding, (2) genetic operators (crossover, mutation, selection)

**Key principle:** Always add **problem-specific intelligence** to the neighbourhood structure or genetic operators. A generic heuristic with an intelligent, problem-aware move definition significantly outperforms a purely generic one.

---

## PART 2: EXERCISES — SOLVED

---

### Exercise Set A: LP Problems & Simplex Method

---

#### A.1 LP Exercise 1

**Problem:**
```
min z = 2 + 5x₁ − 3x₂
s.t.  x₁ ≥ −5
      −x₁ + 2x₂ ≤ 15
      x₁ − x₂ ≥ −8
      x₁ urs, x₂ ≥ 0
```

**(a) Standard form transformation:**

Step 1 (min → max): max z' = −2 − 5x₁ + 3x₂

Step 2 (x₁ ≥ −5 → multiply by −1): −x₁ ≤ 5

Step 4 (x₁ urs: substitute x₁ = y − 5, y ≥ 0):  
This shifts x₁ so that y = x₁ + 5 ≥ 0. Constraints become:
- −x₁ ≤ 5 → y ≥ 0 ✓ (already a sign restriction)
- −x₁ + 2x₂ ≤ 15 → −(y−5) + 2x₂ ≤ 15 → **−y + 2x₂ ≤ 10**
- x₁ − x₂ ≥ −8 → (y−5) − x₂ ≥ −8 → y − x₂ ≥ −3 → multiply by −1: **−y + x₂ ≤ 3** (i.e., x₂ ≤ y+3)
- Objective: z = −23 + 5y − 3x₂ → max z' = 23 − 5y + 3x₂

**Standard form:**
```
max z' = 23 − 5y + 3x₂
s.t.   −y + 2x₂ ≤ 10     [C1]
       −y +  x₂ ≤  3     [C2]
       y, x₂ ≥ 0
```

**(b) Simplex solution:**

Add slack variables s₁, s₂:
```
−y + 2x₂ + s₁ = 10
−y +  x₂ + s₂ =  3
```
Starting BFS: y=0, x₂=0, s₁=10, s₂=3. z'=23.

**Iteration 1:** Objective: z' = 23 − 5y + 3x₂. Coefficient of x₂ is +3 (positive) → x₂ enters.
- Min ratio: C1: 10/2=5; C2: 3/1=3 → **s₂ leaves** (min ratio = 3)

From C2: x₂ = 3 + y − s₂

Substitute into C1: −y + 2(3+y−s₂) + s₁ = 10 → **y + s₁ − 2s₂ = 4**  
Update z': z' = 23 − 5y + 3(3+y−s₂) = **32 − 2y − 3s₂**

**Optimality check:** Nonbasic variables y and s₂ have coefficients −2 and −3 (both ≤ 0) → **OPTIMAL!**

Optimal BFS: y=0, s₂=0 → x₂=3, s₁=4.  
z' = 32 → z_min = −z' + 23 = ... actually: z = −23 + 5y − 3x₂ = −23 + 0 − 9 = **−32**  
And y=0 → x₁ = y−5 = **−5**

> **Answer: z\*_LP = −32 at (x\*₁, x\*₂) = (−5, 3)**

**(c) Graphical verification:**

The feasible region (in x₁, x₂ space with x₁ urs, x₂ ≥ 0):
- x₁ ≥ −5 (vertical line x₁=−5)
- x₂ ≤ (15+x₁)/2 (upper boundary from C2)
- x₂ ≤ x₁+8 (upper boundary from C3: x₁−x₂≥−8)
- x₂ ≥ 0

Corner points: A=(−5, 3), B=(−1, 7), C=(−5, 5)

| Corner | z = 2+5x₁−3x₂ |
|--------|---------------|
| A=(−5, 3) | 2−25−9 = **−32** |
| B=(−1, 7) | 2−5−21 = −24 |
| C=(−5, 5) | Only on boundary of C1 & x₁=−5; C3 gives x₂≤3 when x₁=−5 → C is infeasible |

The binding constraint x₂ ≤ x₁+8 = 3 at x₁=−5 means x₂ cannot exceed 3 at x₁=−5. So C=(−5,5) is **infeasible** (violates x₂ ≤ x₁+8).

> **Minimum z = −32 at (−5, 3)** ✓

---

#### A.2 LP Exercise 2

**Problem (already in standard form):**
```
max z = x₁ + x₂
s.t.  x₁ + x₂ ≤ 4     [C1]
      x₁      ≤ 3     [C2]
           x₂ ≤ 3     [C3]
      x₁, x₂ ≥ 0
```

Add slacks s₁, s₂, s₃:  
Starting BFS: x₁=x₂=0, s₁=4, s₂=3, s₃=3. z=0.

**Iteration 1:** Both x₁ and x₂ have coefficient +1. Choose x₁.
- Ratios: C1: 4/1=4; C2: 3/1=3; C3: no x₁ → s₃ irrelevant
- Min ratio = 3 (C2) → **s₂ leaves**

From C2: x₁ = 3 − s₂.  
Update z: z = (3−s₂) + x₂ = **3 + x₂ − s₂**  
Update C1: (3−s₂)+x₂+s₁=4 → **x₂ + s₁ − s₂ = 1**

**Iteration 2:** z = 3 + x₂ − s₂. Coefficient of x₂ is +1 → x₂ enters.
- Ratios: C1: 1/1=1 (from x₂+s₁=1); C3: 3/1=3
- Min ratio = 1 (C1) → **s₁ leaves**

From C1: x₂ = 1 + s₂ − s₁.  
Update z: z = 3 + (1+s₂−s₁) − s₂ = **4 − s₁**  
Current solution: x₁=3, x₂=1, s₃=3−x₂=2.

**Optimality check:** z = 4 − s₁. Nonbasic: s₁ (coeff −1), s₂ (coeff 0).
- s₁ has negative coefficient → can't improve
- **s₂ has coefficient 0** → can enter basis without changing z → **infinite optimal solutions!**

Entering s₂ (with s₁=0): traces the segment from (3,1) to (1,3) along x₁+x₂=4.

> **Answer: z\*_LP = 4 at all points (x\*₁, x\*₂) = λ·(1,3) + (1−λ)·(3,1), λ ∈ [0,1]**  
> (Infinitely many optimal solutions along the edge x₁+x₂=4 between the two corner points)

---

#### A.3 LP Exercise 3

**Problem:**
```
min z = x₁ − 2x₂
s.t.  10 + x₁ + x₃ ≥ x₂    →   x₂ − x₁ − x₃ ≤ 10
      −4 ≤ x₁ + x₂ ≤ 6    →   x₁ + x₂ ≤ 6  (lower bound redundant with x₁,x₂ urs)
      x₁ urs, x₂ urs, x₃ ≥ 0
```

**Why z\* = −∞ (unbounded):**

With x₁ and x₂ unrestricted in sign, consider the sequence:
- x₁ = −t, x₂ = 6+t (so x₁+x₂ = 6 ≤ 6 ✓), x₃ = 2t−4 ≥ 0 for t ≥ 2

Check constraint 1: x₂−x₁−x₃ = (6+t)−(−t)−(2t−4) = 6+t+t−2t+4 = **10 ≤ 10** ✓

Objective: z = x₁−2x₂ = −t−2(6+t) = **−3t−12 → −∞** as t→∞

The Simplex detects this: when trying to increase a variable with positive objective coefficient, no basic variable ever reaches zero (unbounded feasible direction).

> **Answer: z\*_LP = −∞ (unbounded problem)**  
> The objective can decrease without bound by increasing x₂ while correspondingly decreasing x₁ and increasing x₃.

---

### Exercise Set B: MILP Problems & Branch-and-Bound

---

#### B.1 B&B Exercise 1

**Problem (ILP):**
```
max z = x₁ + x₂
s.t.  5x₁ + 3x₂ ≤ 30
      −x₁ + x₂ ≤  8
      x₁, x₂ ≥ 0,  integer
```

**LP relaxation (SP1):**  
Corner points of LP feasible region:  
- Origin: z=0
- 5x₁+3x₂=30 and x₁=0: x₂=10 → z=10
- −x₁+x₂=8 and x₁=0: x₂=8 → z=8
- Intersection 5x₁+3x₂=30 and −x₁+x₂=8: From second: x₂=x₁+8. Sub: 5x₁+3(x₁+8)=30 → 8x₁=6 → x₁=3/4, x₂=35/4=8.75

Solve graphically: z_LP* at corner of 5x₁+3x₂=30 and −x₁+x₂=8 is NOT optimal for max z=x₁+x₂ (isoprofit lines at 45°).  
Optimal LP corner: check all. The highest z = x₁+x₂ = x₁+(30−5x₁)/3 = 10−(2/3)x₁ → decreases with x₁ → best at x₁=0: z=10 at (0,10).  
But also check (3/4, 35/4): z = 3/4+35/4 = 38/4 = 9.5.

LP optimal: **(0, 10), z_LP* = 10** → upper bound z_ILP* ≤ 10. x₂=10 is integer! x₁=0 is integer! → **z_ILP* = 10? Wait:** check feasibility: 5(0)+3(10)=30≤30 ✓, −0+10=10 ≤ 8? **NO!** 10 > 8 → (0,10) is INFEASIBLE.

Recheck LP relaxation more carefully. The active constraints at optimum:  
Isoprofit lines z=x₁+x₂ (slope −1). Move isoprofit upward.  
Constraints: region bounded by 5x₁+3x₂≤30, −x₁+x₂≤8, x₁,x₂≥0.

Corner at intersection of −x₁+x₂=8 and x₁=0: (0,8) — feasible? 5(0)+3(8)=24≤30 ✓. z=8.  
Corner at (0,8) and 5x₁+3x₂=30 and −x₁+x₂=8 intersection (3/4, 35/4): z=9.5.  
Corner at 5x₁+3x₂=30 and x₂=0: (6,0). z=6.

LP optimal: z_LP* = 9.5 at (0.75, 8.75). Upper bound: z_ILP* ≤ 9 (since coefficients are integer, z is integer too).

**Branch on x₁ (= 0.75):**
- SP2: add x₁ ≥ 1
- SP3: add x₁ ≤ 0

**SP3 (x₁=0):** 5(0)+3x₂≤30 → x₂≤10; −0+x₂≤8 → x₂≤8. LP: x₂=8, z=8. x₂=8 integer! **Candidate: z_LB*=8.**

**SP2 (x₁≥1):** New intersection: 5x₁+3x₂=30, x₂=x₁+8 → 5x₁+3(x₁+8)=30 → 8x₁=6 → x₁=0.75... still<1.  
With x₁≥1: corner at x₁=1: 5+3x₂≤30 → x₂≤25/3≈8.33; −1+x₂≤8 → x₂≤9. LP: (1, 25/3), z≈9.33. Branch on x₂.

**SP2a: x₂≥9** with x₁≥1: 5x₁+3(9)≤30 → 5x₁≤3 → x₁≤0.6. But x₁≥1: **INFEASIBLE.**

**SP2b: x₂≤8** with x₁≥1: 5x₁+3x₂≤30, −x₁+x₂≤8, x₁≥1, x₂≤8. LP optimal: try (1,8): 5+24=29≤30 ✓, −1+8=7≤8 ✓, z=9. **Integer!** New candidate: z_LB*=9. Fathomed (integer).

No more subproblems.

> **Answer: z\*_ILP = 9 at (x\*₁, x\*₂) = (1, 8)**

---

#### B.2 B&B Exercise 2

**Problem (MILP):**
```
max z = 2x₁ + x₂
s.t.  5x₁ + 2x₂ ≤ 8
      x₁  +  x₂ ≤ 3
      x₁, x₂ ≥ 0;  x₁ integer, x₂ real
```

**LP relaxation (SP1):**  
Intersection of 5x₁+2x₂=8 and x₁+x₂=3: x₂=3−x₁ → 5x₁+2(3−x₁)=8 → 3x₁=2 → x₁=2/3, x₂=7/3. z=2(2/3)+7/3=11/3≈3.67. Upper bound z_MILP*≤3.67.

x₁=2/3 is not integer. Branch on x₁.

**SP2: x₁≤0** (i.e., x₁=0, since x₁≥0): 2x₂≤8 → x₂≤4; x₂≤3. LP: x₂=3, z=3. x₁=0 (integer). **Candidate: z_LB*=3.**

**SP3: x₁≥1**: 5+2x₂≤8 → x₂≤1.5; 1+x₂≤3 → x₂≤2. LP: x₁=1, x₂=1.5, z=2+1.5=3.5.  
x₁=1 is integer! x₂=1.5 is real (allowed). **Better candidate: z_LB*=3.5.** Fathomed (x₁ is integer, x₂ is real → solution is feasible for MILP).

SP2 candidate (z=3) < SP3 (z=3.5) — no more subproblems.

> **Answer: z\*_MILP = 3.5 at (x\*₁, x\*₂) = (1, 1.5)**

---

#### B.3 B&B Exercise 3

**Problem (ILP — same as B.2 but BOTH variables integer):**
```
max z = 2x₁ + x₂
s.t.  5x₁ + 2x₂ ≤ 8
      x₁  +  x₂ ≤ 3
      x₁, x₂ ≥ 0,  both integer
```

**SP1 LP relaxation:** z_LP*=11/3≈3.67 at (2/3, 7/3). Branch on x₁.

**SP2: x₁≤0 (x₁=0):** x₂≤4 and x₂≤3 → LP: x₂=3. z=3. Integer! **Candidate (0,3), z_LB*=3.**

**SP3: x₁≥1:** LP: x₁=1, x₂=1.5, z=3.5. x₂=1.5 not integer. Branch on x₂.

**SP3a: x₁≥1, x₂≥2:** 5+4=9>8 → **INFEASIBLE.**

**SP3b: x₁≥1, x₂≤1:** 5+2≤8 ✓, 1+1≤3 ✓. LP: x₁=1, x₂=1, z=3. Integer! **New candidate (1,1). z_LB*=3** (tie with existing).

No more subproblems.

Both (0,3) and (1,1) are optimal with z=3.

> **Answer: z\*_ILP = 3 at (x\*₁, x\*₂) = (0,3) OR (1,1) — two optimal solutions**

---

### Exercise Set C: Network Optimisation Problems

---

#### C.1 Exercise 1: Bandwidth Allocation to Services

**Problem:** N services {Sᵢ} on a transatlantic link with capacity C_tot. Time horizon divided into M equal slots. Service i: begins at slot mᵢᵇ, ends at slot mᵢᵉ, requires bandwidth Bᵢ, yields profit Pᵢ if accepted. Maximise total profit.

**(a) MILP formulation:**

**Decision variables:** xᵢ ∈ {0,1} for i=1,…,N  
(xᵢ = 1 if service Sᵢ is accepted, 0 otherwise)

**Objective:**
```
max  z = Σᵢ Pᵢ xᵢ
```

**Constraints** (capacity respected in every time slot m):
```
∀m ∈ {1,…,M}:    Σ_{i: mᵢᵇ ≤ m ≤ mᵢᵉ} Bᵢ xᵢ ≤ C_tot
```

**Variable type:** xᵢ ∈ {0,1} (BIP) → This is a **Binary Integer Programming** problem.

This is in fact a **Weighted Interval Scheduling / Knapsack-type** BIP.

**(b) Reducing number of constraints when M >> N:**

Key insight: The capacity constraint can only change when a service **starts or ends**. Between two consecutive events (start/end of any service), the set of active services does not change — so we only need one constraint per "interval between events."

**Reduction:** Instead of M constraints (one per slot), use one constraint per distinct **critical time interval** between events. There are at most **2N event times** (N starts + N ends) → at most **2N − 1 intervals** → at most **2N constraints** instead of M.

Since M >> N, this reduces the number of constraints from M to O(N) — a drastic reduction.

**(c) Solution method:**

The problem is a **BIP (Binary Integer Program)**. Approaches:
1. **Branch-and-Bound** with LP relaxation (relax xᵢ to [0,1])
2. **Greedy heuristic** (sort services by profit-per-bandwidth ratio, greedily accept)
3. **Dynamic programming** (if service intervals are nested or structured)
4. Commercial solver (Gurobi/CPLEX) for exact solution

---

#### C.2 Exercise 2: Routing with Limitations

---

##### The Physical Scenario

You have a **telecom network** — think of it as a set of routers or switching nodes connected by links (fibre cables, for example). Multiple customers or services want to send traffic across this network simultaneously. Your job is to decide **which link carries how much of whose traffic**, so that:
1. You minimise the total cost of carrying all traffic
2. No link is overloaded (capacity respected)
3. Every flow arrives within an acceptable delay (real-time service)

---

##### What is a "Commodity"?

A **commodity** is one specific traffic demand: a pair (source, destination) with a given amount of traffic in Gbit/s. For example: "3 Gbit/s must get from v₁ to v₂" is one commodity. "8 Gbit/s must get from v₂ to v₆" is a different commodity.

The key word is "multi-commodity": many such demands coexist **simultaneously**, and they **share the same physical links**. This sharing is what makes the problem hard — if you only had one demand, it would just be a shortest-path problem.

---

##### Every Symbol Explained

**The network structure:**

| Symbol | Type | Plain meaning |
|--------|------|---------------|
| V | set | All **nodes** (routers, switches) in the network |
| A | set | All **directed arcs** (links with a direction) — each physical cable counts as two arcs: one in each direction |
| G = (V, A) | graph | The full network |

**Parameters (given, not decided):**

| Symbol | Type | Plain meaning |
|--------|------|---------------|
| c(a) | number (€/Gbit/s) | **Cost per unit of traffic** sent along arc a. Sending 1 Gbit/s over arc a costs c(a) euros. |
| u(a) | number (Gbit/s) | **Capacity** of arc a — the maximum total traffic (all commodities combined) that this link can carry |
| d(a) | number (ms) | **Propagation delay** of arc a — how many milliseconds it takes for a packet to traverse this link |
| d_{ij} | number (Gbit/s) | **Demand**: how many Gbit/s must be sent from node i to node j. If d_{ij}=0, no traffic is required on that pair. |
| D_max | number (ms) | **Maximum acceptable end-to-end delay** for any flow (the real-time service requirement) |

**Index sets (derived from the problem):**

| Symbol | Plain meaning |
|--------|---------------|
| K | The set of all **commodities** — all (source, destination) pairs (i,j) where d_{ij} > 0. Each commodity k has a source node s_k, a destination node t_k, and a demand d_k = d_{s_k,t_k}. |
| I(v) | All arcs **arriving at** node v (the links that point INTO v) |
| Γ(v) | All arcs **leaving** node v (the links that point OUT OF v) |

**Decision variables (what the solver decides):**

| Symbol | Type | Plain meaning |
|--------|------|---------------|
| f^k(a) | real ≥ 0 | The **amount of traffic of commodity k** (in Gbit/s) routed through arc a. This is the core unknown. |

*Note:* There is one variable f^k(a) for every combination of commodity k AND arc a. For a network with 7 nodes, ~15 arcs, and 10 commodities, that's ~150 variables. All are continuous (real-valued) → this is an **LP**.

---

##### The Objective Function — In Plain English

```
min   Σ_{a∈A}  c(a) · Σ_{k∈K} f^k(a)
```

**Step by step what this says:**

- `Σ_{k∈K} f^k(a)` — sum all commodity flows on arc a:  
  → This gives the **total traffic on arc a** (Gbit/s), regardless of which commodity it belongs to.  
  → E.g. if arc a carries 3 Gbit/s of commodity "v₁→v₂" and 5 Gbit/s of commodity "v₃→v₄", the total is 8 Gbit/s.

- `c(a) · Σ_{k∈K} f^k(a)` — multiply total traffic on arc a by its cost per Gbit/s:  
  → This gives the **cost of using arc a** given how much you route through it.  
  → Cost is proportional to traffic (assumption of the LP model).

- `Σ_{a∈A} [...]` — sum over every arc in the network:  
  → This gives the **total network-wide cost**.

**Bottom line:** The objective = "minimise the total money paid to run all the traffic across all links." You want to use cheap arcs and avoid expensive ones, while still routing all demanded traffic.

---

##### The Constraints — In Plain English

**Constraint 1: Flow Conservation**
```
∀v∈V, ∀k∈K:   Σ_{a∈I(v)} f^k(a)  −  Σ_{a∈Γ(v)} f^k(a)  =  b_k(v)
```
Where:
```
b_k(v) = −d_k   if v = s_k   (source node of commodity k)
          +d_k   if v = t_k   (destination node of commodity k)
           0     everywhere else
```

**What this says:** At every node v, for every commodity k, **traffic must be conserved** — you can't create or lose traffic at a transit node.

*At a transit node (b_k(v) = 0):* whatever volume of commodity k's traffic enters, the same volume must leave. Nothing is stored or generated. Σ_arriving = Σ_leaving.

*At the source (b_k(v) = −d_k, negative):* the source INJECTS d_k Gbit/s into the network. So outflow exceeds inflow by d_k. Σ_leaving − Σ_arriving = d_k.

*At the destination (b_k(v) = +d_k, positive):* the destination ABSORBS d_k Gbit/s. Inflow exceeds outflow by d_k. Σ_arriving − Σ_leaving = d_k.

**Why one constraint per (node, commodity) pair?** Because flows of different commodities must be tracked separately — 3 Gbit/s of "v₁→v₂" traffic and 5 Gbit/s of "v₂→v₆" traffic passing through the same router must each be tracked independently (they go to different destinations).

---

**Constraint 2: Capacity**
```
∀a∈A:   Σ_{k∈K} f^k(a)  ≤  u(a)
```

**What this says:** On each link a, the **combined traffic from ALL commodities** must not exceed the link's physical capacity u(a).

*This is the coupling constraint* — it's the reason you cannot solve each commodity independently. If commodity "v₁→v₂" and commodity "v₃→v₄" both want to use the same link, they compete for capacity. The routing decision for one commodity affects what's left for others.

---

**Constraint 3: Delay (max path delay ≤ D_max)**

The problem requires that **every packet** of commodity k arrives within D_max — this is a *maximum* path delay constraint, not an average.

**Why a simple linear formula like `Σ d(a)·f^k(a) ≤ D_max·d_k` is NOT correct:**

That would only constrain the *weighted average* delay across all paths used. Example with D_max = 30 ms:

| Path used | Delay | Flow |
|-----------|-------|------|
| v₁→v₇→v₃ | 10 ms | 1 Gbit/s |
| v₁→v₂→v₃ | 50 ms | 1 Gbit/s |

Average = (1×10 + 1×50) / 2 = 30 ms ≤ 30 ✓ — but path 2 has delay 50 ms > D_max. Real-time packets on that path arrive late. The average constraint is too weak.

**The correct formulation — pre-processing the graph:**

Before writing the LP, remove any arc a = (u→v) that *cannot* be part of any delay-feasible path for commodity k. Specifically, keep arc a for commodity k only if:
```
min_delay(s_k → u)  +  d(a)  +  min_delay(v → t_k)  ≤  D_max
```
(compute both shortest-delay distances with Dijkstra on d(a) weights). Then set:
```
∀a not satisfying the above:   f^k(a)  =  0
```

After this preprocessing, any flow that satisfies flow conservation in the reduced graph can be decomposed into paths — and every such path has total delay ≤ D_max by construction. The LP then implicitly enforces the maximum delay constraint through the arc restriction, without needing an explicit linear delay inequality.

---

**Constraint 4: Non-negativity**
```
∀a∈A, ∀k∈K:   f^k(a)  ≥  0
```

Traffic flows can only be non-negative — you can't send "negative" traffic.

---

##### Full Formulation at a Glance

```
GIVEN:   Network G=(V,A), costs c(a), capacities u(a), delays d(a)
         Demands d_{ij} for all (i,j), delay limit D_max

DECIDE:  f^k(a) ≥ 0   for all arcs a ∈ A and all commodities k ∈ K

MINIMISE:
    Total cost  =  Σ_{a∈A}  c(a) · [total traffic on arc a]
                =  Σ_{a∈A}  c(a) · Σ_{k∈K} f^k(a)

SUBJECT TO:
    [C1] Flow conservation at every node, for every commodity
    [C2] Total traffic on every arc ≤ arc capacity
    [C3] f^k(a) = 0 for arcs not on any delay-feasible path for k  (max path delay ≤ D_max)
    [C4] All flows non-negative
```

This is a **Linear Program (LP)**: objective and all constraints are linear in f^k(a), and all variables are continuous (real-valued Gbit/s amounts).

---

##### (a) Single or Multi-Commodity?

**Multi-commodity.** In the demand matrix there are 9 non-zero entries — that means 9 different (source, destination) pairs that need traffic simultaneously. Each is a separate commodity because:
- Traffic for v₁→v₂ and traffic for v₃→v₄ are different "goods" — they come from different places and go to different places
- You must track them separately — otherwise you couldn't enforce that "3 Gbit/s actually arrives at v₂ from v₁" vs "5 Gbit/s arrives at v₄ from v₁"
- They share capacity on common links → they interact

A **single-commodity** problem would mean every unit of traffic is identical (same origin and destination for all traffic). That reduces to a simple min-cost flow problem solvable with a graph algorithm.

---

##### (c) Four Special Cases — Why Each One Simplifies

**Case DelCapUnl — No delay limit, no capacity limit (D_max=∞, u(a)=∞):**

If there is no capacity constraint [C2], then the routing decisions for different commodities do not affect each other at all. No link can be overloaded, so commodity k's routing cannot "block" commodity j's routing.

With no delay constraint [C3] either, each commodity k just needs to minimise its own cost: route all d_k Gbit/s along the cheapest possible path from s_k to t_k.

→ **This is exactly what Dijkstra's shortest-path algorithm does** (with arc costs c(a) as weights). Run Dijkstra once per commodity.

→ **YES, standard graph problem ✓**

---

**Case DelUnl — No delay limit, capacity constraints remain (D_max=∞):**

Remove constraint [C3] from the formulation. Now you have the classic **Multi-Commodity Min-Cost Flow (MCMF)** problem.

The coupling through capacity [C2] still exists: commodities share links, so you can't optimise them independently. You can't use Dijkstra. You need the full LP.

For single-commodity this would be solvable with a standard min-cost flow graph algorithm, but with multiple commodities sharing capacity the problem must be solved as a system.

→ **NO, use LP (Simplex) ✗**

---

**Case CapUnl — Capacity unlimited, delay constraints remain (u(a)=∞):**

Remove constraint [C2]. Now no link can overflow, so again the commodities don't interact — each can be solved independently.

But constraint [C3] remains: each commodity must arrive within D_max. For each commodity k independently, the problem is: "find the minimum-cost routing from s_k to t_k such that the average delay ≤ D_max."

This is called the **Constrained Shortest Path (CSP)** problem — find the cheapest path that also meets a delay budget. This is **NP-hard** in general (no known polynomial algorithm). You can't just run Dijkstra, because minimising cost and minimising delay are two conflicting objectives and you need a specific trade-off between them.

→ **NO, requires special algorithms or LP relaxation ✗**

---

**Case Orig — Both constraints active:**

This is the full problem. Capacity couples the commodities, and delay constrains each one. It remains an LP (all variables continuous), solvable with Simplex — but not decomposable into independent graph problems.

→ **NO, use LP (Simplex) ✗**

---

**Summary table:**

| Case | Constraints active | Commodities independent? | Standard graph? | Method |
|------|--------------------|-------------------------|----------------|--------|
| DelCapUnl | neither | YES | **YES** | Dijkstra per commodity |
| DelUnl | only capacity | NO (coupled by capacity) | NO | LP — Simplex |
| CapUnl | only delay | YES | NO (CSP is NP-hard) | LP per commodity / heuristic |
| Orig | both | NO | NO | LP — Simplex |

**The key pattern:** Capacity constraint = coupling between commodities = cannot solve independently. No capacity = each commodity is its own sub-problem. Delay on top = each sub-problem is harder than a plain shortest path.

---

#### C.3 Exercise 3: Clustering in MANET (DS and CDS)

**Definitions:**
- **Dominating Set (DS):** Subset S ⊆ V such that every node is either in S or has a neighbour in S
- **Connected Dominating Set (CDS):** DS where the induced subgraph G[S] is connected

**(a) MILP formulation for minimal DS:**

**Decision variables:** xᵥ ∈ {0,1} for each v ∈ V  
(xᵥ = 1 if node v ∈ S, 0 otherwise)

**Objective:**
```
min   Σ_{v∈V} xᵥ
```

**Constraints** (every node must be dominated):
```
∀v ∈ V:   xᵥ  +  Σ_{u ∈ N(v)} xᵤ  ≥  1
```
(node v ∈ S, OR at least one neighbour of v is in S)

**Variable type:** xᵥ ∈ {0,1} → **BIP problem**

**(b) MILP formulation for minimal CDS:**

Same DS variables xᵥ ∈ {0,1}, plus **flow variables** to enforce connectivity.

Let r ∈ V be an arbitrary root node (fixed in S: x_r = 1).  
For connectivity: route 1 unit of flow from r to every other node v with xᵥ=1.

**Additional variables:** fᵤᵥ ≥ 0 for each directed arc (u,v) (flow on edge u→v)

**Additional constraints:**

*Flow conservation* (each non-root S-node receives 1 unit):
```
∀v ∈ V\{r}:   Σ_{u: (u,v)∈E} fᵤᵥ  −  Σ_{u: (v,u)∈E} fᵥᵤ  =  xᵥ
```

*Root sends flow to all S-members:*
```
Σ_{u: (r,u)∈E} f_{ru}  −  Σ_{u: (u,r)∈E} f_{ur}  =  Σ_{v≠r} xᵥ
```

*Flow only on edges within S (big-M formulation):*
```
∀(u,v) ∈ E:   fᵤᵥ  ≤  |V| · xᵤ
               fᵤᵥ  ≤  |V| · xᵥ
```

**(c) Minimal DS → standard graph problem?**

**NO.** Minimum Dominating Set is **NP-hard** (NP-complete decision version). No efficient polynomial algorithm is known for general graphs.

**Greedy heuristic for minimal DS:**
1. Initialise: S = ∅, uncovered = V
2. While uncovered ≠ ∅:
   a. For each candidate node v ∉ S, compute coverage: |{v} ∪ N(v) ∩ uncovered|
   b. Select v* with **maximum coverage**
   c. Add v* to S
   d. Remove v* and all N(v*) from uncovered
3. Return S

**Scalability:** O(|V|²) — scales well for large MANETs  
**Optimality:** Not guaranteed; greedy approximation within O(log Δ) of optimal (where Δ = max degree)

**(d) Minimal CDS → standard graph problem?**

**NO.** Minimum Connected Dominating Set is also **NP-hard**. Even harder than DS due to connectivity requirement.

**(e) Greedy heuristic for minimal DS (detailed):**

```
Algorithm: Greedy_DS(G=(V,E))
Input: Undirected graph G
Output: Dominating set S

S = ∅
dominated = ∅

While dominated ≠ V:
    For each v ∈ V:
        gain(v) = |({v} ∪ N(v)) \ dominated|   // nodes v would newly dominate
    v* = argmax_{v∉S} gain(v)
    S = S ∪ {v*}
    dominated = dominated ∪ {v*} ∪ N(v*)

Return S
```

**Discussion:**
- **Scalability:** O(|V|²) — each iteration O(|V|) with |V| iterations. Scales to large MANETs.
- **Optimality:** Sub-optimal in general. For the example in the slides (6 nodes), the greedy may find a non-minimal DS. Example in (c) of the figure: optimal CDS has 2 nodes (the two connected red nodes), but greedy DS might find 3 nodes.

**(f) Greedy heuristic for minimal CDS:**

```
Algorithm: Greedy_CDS(G=(V,E))
1. Run Greedy_DS above → initial DS S_DS (may not be connected)
2. Connect S_DS:
   While the induced subgraph G[S_DS] is not connected:
       Find connected components C₁, C₂, … of G[S_DS]
       For each pair (Cᵢ, Cⱼ):
           Find the shortest path P between Cᵢ and Cⱼ in G
       Add all nodes of the shortest inter-component path to S_DS
3. Return S_DS (now a CDS)
```

Alternative: use a **spanning-tree-based** approach:
1. Find a spanning tree T of G
2. All **internal nodes** of T (degree ≥ 2 in T) form a connected dominating set
3. Remove unnecessary nodes (iterative reduction)

**Discussion:**
- **Scalability:** O(|V|² log |V|) — scalable
- **Optimality:** Not optimal in general; typically yields a CDS within a small constant factor of optimal in practice

---

## PART 3: QUICK REFERENCE CARDS

---

### Simplex Cheat Sheet

```
Standard form:   max z = cᵀx,  Ax ≤ b,  x ≥ 0

1. Add slack variables → Ax + Is = b
2. Initial BFS: x=0, s=b (all slacks basic)
3. Entering variable: highest positive coefficient in z-row
4. Leaving variable: minimum ratio bᵢ/aᵢⱼ (only for aᵢⱼ > 0)
5. Pivot: row operations to express z and basics in terms of non-basics
6. Repeat until no positive z-coefficients → OPTIMAL
   Or: entering variable has no positive aᵢⱼ → UNBOUNDED
```

---

### Branch-and-Bound Cheat Sheet

```
For max ILP:
1. Solve LP relaxation → z_LP*, x_LP*
2. If x_LP* is all-integer: DONE (z_ILP* = z_LP*)
3. If not: branch on fractional variable xᵢ = f
   → SP_left:  add xᵢ ≤ ⌊f⌋
   → SP_right: add xᵢ ≥ ⌈f⌉
4. For each subproblem:
   FATHOM if:  (a) infeasible
               (b) z_UB ≤ z_LB* (can't beat incumbent)
               (c) z_UB > z_LB* AND solution integer → new incumbent
5. STOP when no active subproblems remain
```

---

### Heuristic Selection Guide

```
Problem type                           → Recommended approach
─────────────────────────────────────────────────────────────
LP (continuous, linear)                → Simplex (exact)
ILP/MIP, small-medium                  → Branch-and-Bound (exact)
ILP/MIP, too large for B&B             → Heuristics
Nonlinear / non-convex                 → Heuristics

Heuristic choice:
  Small neighbourhood, deterministic   → Steepest Descent (simple)
  Can define tabu + full N(x)          → Tabu Search
  Large N(x), need randomness          → Simulated Annealing
  Solution = "building blocks"         → Genetic Algorithm
  Have domain knowledge                → Dedicated problem-specific heuristic
```

---

### MILP Formulation Recipe

```
Step 1: Identify decision variables
   - What are you deciding? (yes/no → binary, count → integer, amount → real)
   - Name them clearly with their meaning and domain

Step 2: Write objective function
   - What to maximise/minimise?
   - Express as linear combination of decision variables

Step 3: Write constraints
   - For each restriction: express as linear (in)equality
   - Check: are all variables/indices in the right range?
   - Capacity constraints: typically Σ resource_usage ≤ limit
   - Coverage constraints: typically Σ covers ≥ 1
   - Flow conservation: Σ in = Σ out ± demand

Step 4: Specify variable types and bounds
   - xᵢ ∈ {0,1} for binary
   - xᵢ ∈ ℤ, xᵢ ≥ 0 for non-negative integer
   - xᵢ ∈ ℝ, xᵢ ≥ 0 for non-negative real
```

---

### Network Problem Classification

```
Is it a known graph problem?
├── YES: Shortest path (one commodity, no capacity) → Dijkstra
├── YES: Min-cost flow (one commodity, with capacity) → SSP algorithm
├── YES: MST → Kruskal / Prim
│
└── NO: Need optimisation techniques
    ├── Linear? → LP or (MI)LP
    │   ├── All continuous → LP → Simplex
    │   └── Some integer → (MI)LP → Branch-and-Bound
    │
    └── Too complex / nonlinear → Heuristics
        ├── Deterministic, full N(x): Tabu Search
        ├── Stochastic, partial N(x): Simulated Annealing
        └── Population-based: Genetic Algorithms
```

---

*Study guide covers: Chapter 4 — MOBAN 2025-2026 (Prof. Mario Pickavet, Ghent University/IDLab)*

# JUSTICE Model Glossary — Assignments 4–7

A reference table of keywords, parameters, and concepts encountered in assignments 4 (Problem Formulation), 5 (MOEA Local), 6 (MOEA Convergence), and 7 (Pareto Visualisation).

Two explanations per term:
- **Technical (MSc level)** — precise definition with units, dimensions, and role in the model.
- **ELI5** — same idea, but in plain words a five-year-old can follow.

---

## 1. Model Levers (the things we *decide*)

| Term | In | Technical Explanation (MSc) | ELI5 (Kid version) |
|---|---|---|---|
| **Emission Control Rate (ECR)** | A4–A7 | Decision variable in `[0, 1]` per region per timestep. `0` = no abatement, `1` = full decarbonisation. Controls how much CO₂ each of the 57 regions emits at each year. | How hard each country tries to stop making smoke from cars and factories. 0 means "don't try," 1 means "try as hard as possible." |
| **RBF Adaptive Policy** | A4, A5, A7 | A 244-dimensional decision vector parameterising a Radial Basis Function network that maps climate state → ECR. This is the *policy* the MOEA searches over. | A little recipe book the policy uses: "if the world looks like *this*, do *that*." We have 244 knobs to set up the recipe. |
| **RBF Centers `c_{i,j}`** | A4, A5, A7 | 8 parameters in `[-1, 1]` specifying where each basis function is centred in the 2-D normalised state space (temperature, ΔT). | The "favourite spots" of each rule — where in climate-world each rule pays the most attention. |
| **RBF Radii `r_{i,j}`** | A4, A5, A7 | 8 parameters in `[0, 1]` controlling the width / response breadth of each basis function (Gaussian-like kernel). | How "wide" each rule's attention is — does it care only when things are exactly this way, or also when they're a bit off? |
| **RBF Weights `w_{k,i}`** | A4, A5, A7 | 228 parameters (4 RBFs × 57 regions) determining each basis function's contribution to each region's ECR. | How loudly each rule shouts at each country. Big weight = "really do something here." |
| **Radial Basis Functions (RBFs)** | A4, A5, A7 | Non-linear mapping network with `N_RBFS = 4`, `N_INPUTS = 2`. Outputs a smooth function of state used to determine regional ECR each step. | Four little brains that each look at the weather and shout "more!" or "less!" to every country. |

---

## 2. Exogenous Uncertainties (the things we *can't* decide)

| Term | In | Technical Explanation (MSc) | ELI5 (Kid version) |
|---|---|---|---|
| **SSP-RCP Scenario** | A4, A5 | Shared Socioeconomic Pathway crossed with a Representative Concentration Pathway. 8 indexed options (0–7) describing population, GDP growth, and emissions trajectories. | Different "movie scripts" for how the future could go — lots of people, few people, rich, poor, dirty, clean. |
| **SSP3-7.0** | A4, A5 | Scenario index 5 — "regional rivalry": slow tech diffusion, high emissions, fragmented cooperation. Used as the reference baseline. | The "everyone-fights-and-no-one-shares-toys" future. We use this as the worst-case sandbox. |
| **Equilibrium Climate Sensitivity (ECS)** | A4, A5 | Long-run global temperature rise from doubled atmospheric CO₂. Major source of climate uncertainty; sampled via the FaIR ensemble. | How hot the Earth gets if we double the CO₂. We don't know exactly, so we try lots of guesses. |
| **FaIR Ensemble Members** | A4, A5, A6 | 1001 climate model realisations with varying physics parameters (including ECS). For local optimisation we sub-sample 10. | A thousand pretend Earths, each with a slightly different "weather brain." We test our plan on each one. |
| **Damage Function Type** | A4, A5 | Choice between Kalkuhl (used) and DICE specification. Maps temperature anomaly → fractional GDP loss per region. | Two ways of guessing how much heat *hurts* the economy. We picked one. |
| **Abatement Cost Curve** | A4, A5 | Choice between Enerdata (used) and DICE. Maps ECR → fraction of GDP spent on mitigation. | Two ways of guessing how *expensive* it is to be clean. We picked one. |
| **Pure Rate of Time Preference** | A4 | Parameter `ρ` in inter-temporal welfare aggregation — how much future generations' utility is discounted. | How much we care about future kids compared to today's kids. |

---

## 3. Objectives / Outcomes (the things we *measure*)

| Term | In | Technical Explanation (MSc) | ELI5 (Kid version) |
|---|---|---|---|
| **Welfare** | A4–A7 | Aggregate social welfare (scalar). MINIMIZE; ε = 10.0. Sign and aggregation depend on the chosen welfare function. | The big "are people doing OK?" score for the whole world. |
| **Fraction Above Threshold** | A4–A7 | Fraction of FaIR ensemble where global T > 2°C in 2100. MINIMIZE; ε = 0.05. A risk metric over climate uncertainty. | Out of all the pretend Earths, how many got too hot? We want this number small. |
| **Welfare Loss from Damage** | A4–A7 | Welfare burden attributable to climate damages. MAXIMIZE (handled via sign inversion); ε = 10.0. | How much hurt comes from the heat itself. |
| **Welfare Loss from Abatement** | A4–A7 | Welfare burden attributable to mitigation spending. MAXIMIZE; ε = 10.0. | How much hurt comes from paying to clean up. |
| **Years Above Temperature Threshold** | A4 | Count of simulation years where global T exceeds the 2°C threshold. | How many years in a row the Earth had a fever. |

---

## 4. Social Welfare Functions (whose pain *counts how much*)

| Term | In | Technical Explanation (MSc) | ELI5 (Kid version) |
|---|---|---|---|
| **Utilitarian** | A4, A5 | `W = Σ U_i`. Sums utilities across regions/time. No explicit inequality concern. Index 0. | Add up everyone's happiness, no matter who they are. |
| **Prioritarian** | A4, A5 | Concave transform of utility — places greater weight on lower utilities. Index 1. | Help the saddest kids first. |
| **Sufficientarian** | A4, A5, A6 | Penalises consumption below a floor (~$0.456k/cap/yr). Used in this project to centre LDC concerns. | Make sure *no one* falls below "enough." |
| **Egalitarian** | A4 | Max-min welfare — maximise the worst-off region's utility (Rawlsian MaxiMin). | Make the worst-off kid as happy as possible. |

---

## 5. JUSTICE Model Parameters & Constants

| Term | In | Technical Explanation (MSc) | ELI5 (Kid version) |
|---|---|---|---|
| **N_REGIONS = 57** | A4–A7 | 57 RICE50 aggregate regions covering the globe. | The world is cut into 57 pieces. |
| **N_TIMESTEPS = 286** | A4, A5, A7 | Annual integration steps from 2015 to 2300. | 286 years of pretend future. |
| **N_RBFS = 4 / N_INPUTS = 2** | A4, A5, A7 | RBF network shape: 4 basis functions, 2-dim input (T, ΔT). | 4 little rule-brains, each looking at 2 things. |
| **Start / End Year (2015 / 2300)** | A4, A5, A7 | Simulation horizon. | We play the game from 2015 to year 2300. |
| **Emission Control Start Year (2025)** | A4, A5 | First year ECR > 0 is allowed; before that, regions emit BAU. | We can't start cleaning up until 2025. |
| **Consumption Floor (~$0.456k/cap/yr)** | A4 | Minimum consumption target used in Sufficientarian welfare. | The least amount of money a person needs to be "OK." |
| **RICE50 Regions (e.g. `rsas`, `rsaf`)** | A4, A5, A7 | South Asia and Sub-Saharan Africa — focal regions for LDC mandate. Low per-capita consumption → high sufficiency-floor risk. | The poorest neighbourhoods of the world — we care about them the most. |

---

## 6. EMA Workbench Concepts

| Term | In | Technical Explanation (MSc) | ELI5 (Kid version) |
|---|---|---|---|
| **XLRM Framework** | A4 | Problem framing: e**X**ogenous uncertainties, **L**evers, **R**elationships, **M**easures (outcomes). | Four boxes: things-we-don't-control, things-we-do-control, the-rules, and what-we-watch. |
| **`Model`** | A4, A5 | EMA container declaring uncertainties, levers, outcomes around a Python function. | A box that holds our whole game. |
| **`RealParameter`** | A4, A5 | Continuous lever or uncertainty (float range). | A knob that turns smoothly. |
| **`IntegerParameter`** | A4, A5 | Integer-valued uncertainty (e.g. FaIR ensemble index). | A knob that clicks to whole numbers. |
| **`CategoricalParameter`** | A4, A5 | Discrete choice uncertainty (scenario index, damage function type). | A button you press to pick A, B, or C. |
| **`ScalarOutcome`** | A4, A5, A6 | Single number objective for optimisation. | One number that tells us how the game went. |
| **`ArrayOutcome`** | A4 | Multi-dimensional output (e.g. trajectory). | A whole row of numbers, not just one. |
| **`SequentialEvaluator`** | A4, A5 | Runs experiments one at a time. | Play one game at a time. |
| **`MultiprocessingEvaluator`** | A4, A5 | Parallel execution across worker processes (CPU count − 1). | Play lots of games at the same time on many brains. |
| **`Scenario` / `Policy`** | A5 | Bundles of uncertainty / lever values for a single model run. | A "what-if" card for the world / for our plan. |

---

## 7. MOEA (Multi-Objective Evolutionary Algorithm) Concepts

| Term | In | Technical Explanation (MSc) | ELI5 (Kid version) |
|---|---|---|---|
| **GenerationalBorg** | A5, A6 | Self-adaptive MOEA over a population, with 6 variation operators whose selection probabilities adapt online based on archive improvement. | A smart robot that breeds the best plans over and over, and learns which mixing trick works best. |
| **Population Size (= 100)** | A5, A6 | Number of candidate policies kept per generation. | 100 plans alive at the same time. |
| **NFE (Number of Function Evaluations)** | A5, A6 | Compute budget. Local run: 2000; smoke test: ~200. | How many tries the robot gets before we stop it. |
| **Random Seed (1–5)** | A5, A6 | Stochastic seed controlling initial population and randomised operators; 5 seeds → 5 independent runs. | A "lucky number" so we can replay the same game — different seeds = different play-throughs. |
| **Variation operators (SBX, PCX, DE, UNDX, SPX, UM)** | A5 | Six different ways Borg combines / mutates parents to create offspring policies. Probabilities adapt based on success. | Six different ways to mix two recipes to invent a new one. |
| **Operator Adaptation** | A5, A6 | Online adjustment of operator probabilities proportional to recent contribution to the archive. | The robot learns which mixing trick works best lately and uses it more. |
| **Epsilon-Archive** | A5, A6 | Non-dominated archive that admits a solution only if it improves on at least one objective by at least ε. | A trophy shelf — only really-better plans get a spot. |
| **Pareto-Optimal Solutions** | A5, A6, A7 | Final non-dominated set: no other solution is better on all objectives simultaneously. | The "best of the best" — you can't make one thing better without making another worse. |
| **Non-Dominated** | A5, A6, A7 | A solution that is not strictly worse on every objective than some other solution. | No other plan beats this one *everywhere*. |
| **Many-Objective Optimisation** | A5, A6 | Optimisation with ≥ 4 objectives. Pareto front becomes a surface rather than a curve; classical methods struggle. | Trying to win at 4 games at once. |
| **EMODPS** | A4 | Evolutionary Multi-Objective Direct Policy Search — the overall framework combining RBF policies with MOEA. | "Use evolution to invent the best recipe book." |
| **Smoke Test** | A5 | Tiny run (e.g. NFE = 500, 1 seed) used to validate plumbing before the full optimisation. | A quick taste-test before cooking the big meal. |

---

## 8. Convergence & Quality Metrics (Assignment 6 territory)

| Term | In | Technical Explanation (MSc) | ELI5 (Kid version) |
|---|---|---|---|
| **Hypervolume (HV)** | A6, A7 | Volume in objective space dominated by the archive, bounded by a reference (nadir) point. Higher → better coverage. Computed via `moocore`. | The size of the "all the good plans" cloud — bigger is better. |
| **Epsilon-Progress** | A5, A6 | Count of ε-archive improvements per checkpoint. Trending to 0 signals convergence. | How often the robot found something new. When it stops finding new stuff, it's done. |
| **Generational Distance (GD)** | A6, A7 | Mean L2 distance from each archive point to its nearest reference-set point (Veldhuizen, p = 1). Lower → closer to optimum. | How far away our plans are from the very best plans. |
| **Additive Epsilon Indicator (I_eps+)** | A6, A7 | `max_r min_a max_j (a_j − r_j)` in pure-minimisation space. Smallest ε such that the archive ε-dominates the reference set. | The biggest "gap" between our best and the very best. |
| **Reference Set** | A6, A7 | Pareto-filtered union of all seeds' archives at a given NFE. Surrogate for the true Pareto front. | The class's combined best work — used as the answer key. |
| **Grand Reference Set** | A6, A7 | Reference set across all NFE budgets — the strongest available approximation. | The "ultimate" answer key, made from everyone's everything. |
| **Epsilon-Dominance / Laumanns ε-Box Archive** | A6 | Partition objective space into boxes of width ε per objective; keep one winner per box; then Pareto-filter. Used to assemble reference sets. | Cut objective-world into a grid of LEGO bricks and keep only one champion per brick. |
| **Pareto Filter** | A6 | Removes dominated solutions in pure-minimisation space. | Throw away any plan that another plan beats everywhere. |
| **Reference (Nadir) Point** | A6 | A point strictly worse than the archive on every axis — anchors hypervolume. | The "worst corner" of the room — we measure the good cloud from there. |
| **Maximise Mask** | A6 | Per-objective boolean: `True` = MAXIMIZE, `False` = MINIMIZE. Used to flip signs into pure-min space. | A little switch per goal that says "bigger is better" or "smaller is better." |
| **Convergence Plateau** | A6 | HV / GD reach an asymptote before NFE budget is exhausted — diminishing returns. | The robot stops getting better even though we let it keep trying. |
| **Seed Consistency** | A6 | Cross-seed agreement on final HV/GD. Low variance ⇒ trustworthy. | Do different "lucky numbers" all give us roughly the same answer? |
| **Archive Snapshot / Checkpoint NFE** | A6 | `ArchiveLogger` saves archive state at regular NFE intervals for trajectory analysis. | Take a photo of the trophy shelf every so often. |
| **`moocore` library** | A6 | Modern hypervolume / indicator computation; replaces deprecated `deap._hypervolume`. | The new calculator we use instead of the broken old one. |

---

## 9. Climate & Economic Variables

| Term | In | Technical Explanation (MSc) | ELI5 (Kid version) |
|---|---|---|---|
| **Global Temperature** | A4, A5, A7 | Annual global mean surface temperature anomaly from FaIR. | How hot the whole Earth is. |
| **Scaled Temperature** | A7 | Normalised to `[0, 1]` via `(T − 0) / (16 − 0)` — RBF input #1. | Earth's "fever" squished into a 0–1 score. |
| **Temperature Rate of Change (Diff)** | A7 | 5-yearly ΔT — second RBF input. | How fast the Earth is heating up right now. |
| **FaIR Climate Module** | A4, A5 | Reduced-complexity climate model mapping emissions → concentrations → temperature. | The pretend weather machine. |
| **CO₂ Emissions** | A4, A5 | Annual global CO₂ from the chosen SSP-RCP, reduced by ECR via the abatement function. | The smoke we make. |
| **Damage Function** | A4, A5 | Maps temperature anomaly → fractional GDP loss per region. | How much money the heat eats up. |
| **Abatement Cost Function** | A4, A5 | Maps ECR → fraction of GDP spent on mitigation. | How much money cleaning up costs. |
| **Regional Production / Capital / Consumption** | A4 | Per-region economic state co-evolving with damages and abatement. | Each country's stuff, savings, and shopping. |

---

## 10. Policy Evaluation & Visualisation (Assignment 7)

| Term | In | Technical Explanation (MSc) | ELI5 (Kid version) |
|---|---|---|---|
| **Parallel-Coordinates Plot** | A7 | One vertical axis per objective; each policy is a polyline. Crossings between adjacent axes indicate trade-offs. | A spaghetti picture — each noodle is a plan, and crossing noodles mean "you can't have both." |
| **Trade-off Crossing** | A7 | Edge crossing between axes ⇒ objectives are in conflict locally. | When lines cross, you have to pick one or the other. |
| **Anchor Policies** | A7 | 4 hand-picked Pareto solutions highlighted in colour for narrative. | Four favourite plans we tell stories about. |
| **End-of-Horizon ECR Map** | A7 | Choropleth of regional ECR at final timestep (~2300). | A world map coloured by "how hard each country was cleaning up at the end." |
| **RBF Re-application** | A7 | Re-run the RBF using stored centres, radii, weights to reconstruct the regional ECR pathway from a stored policy vector. | Re-bake the recipe to see exactly what each country did each year. |
| **`EmissionControlConstraint`** | A7 | Bounds growth: `max_annual_growth_rate = 0.04`, `min_emission_control_rate = 0.01`. Prevents physically implausible jumps. | Rules so countries can't *suddenly* go from dirty to spotless overnight. |
| **Choropleth Coloring (YlOrRd)** | A7 | Sequential colormap encoding ECR; shared `vmin`/`vmax` for comparability across policies. | Yellow = doing a little, red = doing a lot. Same colours mean same thing on every map. |
| **RICE50 ISO-3166 Mapping** | A7 | Lookup `rice50_regions_dict.json`: region name → list of ISO country codes. Used to dissolve country polygons into 57 regional shapes. | A list saying "these countries together count as one neighbourhood." |
| **GeoDataFrame Dissolve** | A7 | `geopandas` operation merging polygons sharing a region key. | Glue countries together to make the 57 neighbourhoods. |

---

## 11. Policy / Ethical Concepts

| Term | In | Technical Explanation (MSc) | ELI5 (Kid version) |
|---|---|---|---|
| **LDC Mandate** | A4, A7 | Project framing: prioritise outcomes for Least Developed Countries (notably `rsas`, `rsaf`). | We're on Team Poor-Kids — they matter most. |
| **Sufficiency Floor** | A4 | Per-capita consumption threshold below which Sufficientarian welfare incurs heavy penalty. | The "nobody falls below this" line. |
| **Front-Loaded ECR Profile** | A4 | Aggressive early abatement: high near-term cost, lower climate overshoot risk. | Clean up *now*, even if it hurts today. |
| **Back-Loaded ECR Profile** | A4 | Delayed action: lower short-term cost, higher overshoot risk. | Clean up later — cheaper now but risky. |
| **Business As Usual (BAU)** | A4, A5, A6 | Reference run with ECR ≡ 0. Baseline for comparison. | What happens if we do nothing. |
| **Regional Burden Analysis** | A7 | Which regions must abate most under each Pareto-optimal policy. | Which neighbourhoods have to do the most chores. |

---

## 12. Files & Outputs

| Term | In | Technical Explanation (MSc) | ELI5 (Kid version) |
|---|---|---|---|
| **`run_optimization_local.py`** | A5 | Driver script: 5 seeds × NFE evaluations of `GenerationalBorg` over the JUSTICE problem. | The "press play" script for the robot. |
| **`config_student.json`** | A4, A5, A6 | Config: welfare function, scenario, epsilons, NFE, seeds. | The settings page for the game. |
| **Output Directory `<welfare>_<nfe>_<seed>/`** | A5, A6 | Per-run output tree. | A labelled folder for each play-through. |
| **`pareto_front_<seed>.csv`** | A5, A6, A7 | Final archive: 244 lever columns + 4 objective columns per row. | The trophy list at the end of one run. |
| **`convergence_<seed>.csv`** | A5, A6 | EpsilonProgress + operator probabilities per checkpoint. | A diary of how the robot improved. |
| **Convergence Archive `.tar.gz`** | A6 | `ArchiveLogger` snapshots — archive state over NFE for trajectory plots. | A photo album of the trophy shelf over time. |

---

*Generated for self-study reference across assignments 4–7. Page references and code symbols use the actual identifiers in the notebooks (`ECR`, `N_RBFS`, `rsas`, `rsaf`, etc.) so you can grep the source.*

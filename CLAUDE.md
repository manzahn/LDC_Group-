# JUSTICE Project — Claude Code Notes

## What's wrong with the current A5/A6 results and what to fix

### The core problem: NFE is far too low

The runs in `assignments_ema/results/` were done with **NFE = 2000**
(visible from the directory names `SUFFICIENTARIAN_2000_*`).
The run script default — and the minimum for meaningful results — is **50,000 NFE**.

With 2000 NFE and population = 100, Borg gets only **20 generations** to evolve
a **244-dimensional** policy vector. That is a smoke test, not a real optimisation.

---

### What the A6 convergence graphs show

File: `assignments_ema/results/convergence_metrics_SUFFICIENTARIAN_2000.png`

| Metric | Expected at convergence | Actual at NFE = 2000 |
|--------|------------------------|----------------------|
| Hypervolume | flat plateau | still rising steeply |
| Generational Distance | flat plateau near 0 | still falling |
| Epsilon Indicator | flat near 0 | still falling |
| Epsilon-progress | ~0 | 4–8 (still finding new archive members) |

Every metric says the same thing: **the algorithm had not converged when it was stopped.**

The archive snapshots confirm it: only **2 checkpoints** per run (~NFE 1003 and ~2008),
so the convergence "curves" are just two-point lines with no shape to read.

---

### What to change — Assignment 5 re-run

```bash
# From the repo root
cd assignments_ema

# Quick test first (30–60 min on 8 cores, 1 seed):
python run_optimization_local.py --nfe 10000 --seeds 9845531

# Full run (1–3 h per seed, ~8 h total for 5 seeds — run overnight or with nohup):
nohup python run_optimization_local.py --nfe 50000 > opt_log.txt 2>&1 &
# or with screen:
screen -S justice
python run_optimization_local.py --nfe 50000
# Ctrl-A D to detach; screen -r justice to reattach
```

The config (`config/config_student.json`) looks correct — no changes needed there:
- `welfare_function`: `"SUFFICIENTARIAN"` ✓
- `reference_ssp_rcp_scenario_index`: `5` (SSP3-7.0, the reference baseline) ✓
- `epsilons`: `[10.0, 0.05, 10.0, 10.0]` ✓

---

### What to change — Assignment 4

Assignment 4 sets up the XLRM model definition. The current config parameters are fine.
Nothing needs changing in A4 unless you change the welfare function or scenario.

If you do change the welfare function in `config_student.json`, re-run A5 and then
re-run A6 (notebook `assignment_06_moea_convergence.ipynb`) to regenerate everything.

---

### After the A5 re-run

With 50,000 NFE you will get:
- ~50 archive snapshots per seed (one every ~1000 NFE by default)
- Pareto fronts with 20–50+ solutions instead of 5–8
- Convergence curves with real shape — you can answer all A6 reflection questions

Then in A6: just re-run all cells from top to bottom.
The fixed `_epsilon_nondominated` / `_generational_distance` / `_epsilon_indicator`
helpers in the setup cell handle the "center 0"-style lever names without errors.

---

## Known ema_workbench fix (do not revert)

`rebuild_platypus_population` crashes on JUSTICE lever names like `"center 0"` because
`df.itertuples()` renames columns with spaces to positional `_N` names and
`getattr(row, "center 0")` fails. Three cells in A6 use local numpy/pandas replacements:

- `_epsilon_nondominated` (Laumanns ε-box archive)
- `_generational_distance` (Veldhuizen GD, p=1)
- `_epsilon_indicator` (additive I_eps+)

Same algorithms, same outputs — just work on the 4 objective columns directly.

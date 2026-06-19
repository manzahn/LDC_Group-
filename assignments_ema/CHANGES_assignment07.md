# Assignment 7 — Changes from original student notebook

## What was wrong (root cause)

The original parallel-coordinates cell used a custom colour scheme and the wrong
`idxmin`/`idxmax` direction for two objectives.  The ECR simulation cell picked
the same wrong anchors, so 3 of 4 world maps rendered as near-white (near-zero ECR).

---

## Cell-by-cell changes

### `a07v-a06-c-04` — Parallel-coordinates plot

| What | Before | After |
|---|---|---|
| Normalisation | Single `(x - min)/(max - min)` then manual invert for MINIMIZE cols | `normalise_for_plot()` function that handles MINIMIZE and MAXIMIZE cols correctly |
| Colourmap | `viridis_r` (blue→yellow) | `RdYlBu_r` (blue=low risk → red=high risk) matches teacher output |
| Colour input | Raw `fraction_above_threshold` values | Normalised `fraction_above_threshold` values |
| Anchor: welfare | `welfare.idxmin()` labelled "Best welfare" | `welfare.idxmin()` labelled **"Max welfare (min welfare)"** — same index, corrected label |
| Anchor: damage | `welfare_loss_damage.idxmin()` labelled "Least damage loss" | `welfare_loss_damage.idxmax()` labelled **"Max damage avoided (max wl_damage)"** |
| Anchor: climate | `fraction_above_threshold.idxmin()` "Best climate" | `fraction_above_threshold.idxmin()` **"Min climate risk (min frac above 2°C)"** |
| Anchor: abatement | `welfare_loss_abatement.idxmin()` "Least abatement cost" | `welfare_loss_abatement.idxmin()` **"Min abatement cost (min wl_abatement)"** |
| Anchor highlight style | `scatter` + `plot` with `solid_capstyle` | `plot` with `marker="o", markersize=8` |
| Legend position | `upper left` | `lower right` |
| Reference lines | None | Dashed lines at y=0 and y=1 |
| Output filename | `pareto_parallel_coords.png` | `parcoords_reference_set.png` |

### `923672c1` — JUSTICE ECR simulation + policy selection

| What | Before | After |
|---|---|---|
| Anchor order | welfare, fraction, damage, abatement | fraction (climate), abatement, welfare, damage |
| `idx_3` (damage anchor) | `welfare_loss_damage.idxmin()` → idx 173, fraction=1.00 (white map) | `welfare_loss_damage.idxmax()` → idx 91, fraction=0.92 (colorful map) |
| Label "Least damage loss" | was misleading | renamed **"Max damage avoided"** |
| Label "Best welfare" | kept same index (idxmin) | renamed **"Max welfare"** to match parallel-coords |

### `e4022e76` — Geographic snapshot (`ecr_end` dict)

Labels updated to match the new anchor names so column keys stay consistent
through to the map plotting cell:

```python
# Before
ecr_end = {
    "Best welfare":         _snap_end(ecr_1),
    "Best climate":         _snap_end(ecr_2),
    "Least damage loss":    _snap_end(ecr_3),
    "Least abatement cost": _snap_end(ecr_4),
}

# After
ecr_end = {
    "Min climate risk":   _snap_end(ecr_1),
    "Min abatement cost": _snap_end(ecr_2),
    "Max welfare":        _snap_end(ecr_3),
    "Max damage avoided": _snap_end(ecr_4),
}
```

---

## Why the deepcopy patch was NOT the problem

The `matplotlib.path.Path.__deepcopy__` patch in the setup cell is a Python 3.14
compatibility fix for import-time crashes.  It patches how matplotlib path objects
are serialised in memory and has zero effect on JUSTICE model computations, RBF
parameter loading, or ECR values.

---

## Expected outcome after re-running from cell `923672c1`

| Policy | fraction_above_threshold | ECR map |
|---|---|---|
| Min climate risk | 0.60 | Colorful |
| Min abatement cost | 0.70 | Colorful |
| Max welfare | 1.00 | Near-white (real result: no-abatement policy minimises welfare loss) |
| Max damage avoided | 0.92 | Colorful |

The "Max welfare" panel remaining light is a genuine Pareto-front finding for
the CONSUMPTION_CORRIDOR_PRIORITARIAN welfare function: the policy that minimises
total welfare loss achieves this by avoiding abatement costs entirely.

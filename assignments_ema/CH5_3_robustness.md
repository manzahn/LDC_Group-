# 5.3 Robustness of the advice

*(Draft. Two fill-ins are flagged: `‹P4_SAT›` = P4 joint satisficing score from
`sat_score[LDC_POLICY]`; and the real-world-examples sentence in ¶5.)*

We re-evaluated all 173 Pareto policies from Chapter 4 across 50 members of the
FAIR climate ensemble, a full factorial of 8,650 experiments, and scored each
policy two ways: a domain-criterion satisficing count and minimax regret. This
test varies one source of uncertainty only — the climate response, which is
ECS-like. The socio-economic pathway (SSP) and the welfare parameters
(δ, ρ, η) were held fixed. It is therefore a first-order robustness check on
climate-response uncertainty, not a full sweep, and we read the rest of the
uncertainty space qualitatively against the reasoning in 5.1.

The two metrics agree on one thing: the policies that survive the climate
ensemble are interior compromise policies, not the objective-extreme anchors
that Chapter 4 selected from. Satisficing thresholds were set at the median for
welfare and the two welfare-loss objectives, and the 25th percentile for years
above 2°C. Across the 173 policies the mean joint satisficing score is 25.8%,
and 93 policies never satisfice all four objectives in any scenario. Years above
2°C is met by 100% of policies and carries a regret of about zero everywhere, so
it does not separate the policies at all; the contest is decided on welfare and
the two welfare-loss objectives, each of which is met in roughly 50% of cases.
On minimax regret the most robust policy is P72, a non-anchor compromise with a
worst-case regret of 0.0184 that satisfices all four objectives in all 50
futures. The worst policy is P0 (the min-damage-cost anchor) at 3.00. All four
Chapter-4 anchors sit in the fragile tail: P68 (min abatement) at about 2.1,
P39 (max welfare) at about 2.88, P0 (min damage) at 3.00, and the LDC's chosen
policy P4 (min climate risk) at 2.8815 — rank 47 of 173, with a joint
satisficing score of ‹P4_SAT›. None of the anchors examined in Chapter 4 is
robust to the climate ensemble.

P4 specifically does not gain a worst-case robustness advantage from its design.
In its worst climate future the regret splits as 1.00 on welfare, 0.00 on years
above 2°C, 1.00 on welfare-loss-damage, and 0.88 on welfare-loss-abatement. So
in that scenario P4 reaches the maximum possible regret on both welfare and
damage. The only objective it protects is years above 2°C, and that objective is
saturated for every policy in this ensemble, so protecting it carries no
information. We therefore do not claim P4 is robust on damage or welfare. Under
the climate ensemble, choosing the policy that minimises climate risk did not
translate into worst-case robustness on the welfare or damage objectives.

The uncertainties we did not vary still point in a consistent direction. Climate
sensitivity is already spanned by the FAIR spread, so ECS is covered
empirically. Higher-emission SSPs raise tail risk on the climate-risk and damage
objectives, which strengthens rather than weakens the case for early mitigation
and loss-and-damage support; this is untested here and we flag it against 5.1.
The welfare parameters work the same way under a prioritarian-plus-sufficiency
framing: lower discounting or higher inequality aversion only add weight to the
downside in low-capacity regions, so the direction of the advice is stable
across the plausible range of value choices. These are arguments about
direction, not measured results.

The metrics do not pick a policy for us. They expose a trade-off — aggregate
worst-case regret favours compromise policies like P72, while the LDC's P4 is an
extreme on a single objective and pays for it in worst-case regret. Resolving
that trade-off is a normative call, not a numerical one. The LDC mandate
resolves it by precaution toward the regions with the least adaptive capacity:
when the worst futures cannot be ruled out, you insure the most vulnerable
against them. That stance, not the specific policy point, is what is robust to
deeper uncertainty — more uncertainty makes the case for the grants and the
standalone loss-and-damage red lines stronger, because the downside is
concentrated where capacity to absorb it is lowest. *‹strong sentence: we chose
this direction because real-world precedent points the same way — name the
examples here.›*

One caveat bounds all of the above. Assignment 8 varied the climate response
only; the economic inputs, the damage function, and the welfare framing were not
tested, and we defer those to 5.1. The advice stays conditional. Its direction,
though, holds across the uncertainty we did test and across the uncertainty we
reasoned about qualitatively.

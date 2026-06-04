# Bias, Fairness & Non-Discrimination

> A deep-dive reference for auditors on what fairness means, how to measure it, why you can't have all of it at once, and how the technical and legal pictures connect. Companion to the [ML/AI Auditing roadmap](./README.md).

Fairness is not a single property you can compute and tick off. It is a contested concept with at least three lenses that an auditor has to hold at the same time:

- **Statistical** — measurable parity in a model's predictions or errors across groups.
- **Legal** — compliance with anti-discrimination law (disparate treatment, disparate impact, fair lending).
- **Ethical / sociotechnical** — whether the system produces just outcomes in its real-world context.

These lenses often disagree. The job of a bias audit is to make the disagreements explicit, document which definition was chosen and why, and measure the residual harm that remains.

> [!IMPORTANT]
> There is no "fair" setting you can turn on. Choosing a fairness definition is a value judgment, not a calculation. An auditor's deliverable is a *defensible, documented choice* plus evidence — not a binary pass/fail.

---

## Table of Contents

1. [Group vs. Individual Fairness, and Intersectional Bias](#1-group-vs-individual-fairness-and-intersectional-bias)
2. [Fairness Metrics](#2-fairness-metrics)
3. [The Impossibility Results](#3-the-impossibility-results)
4. [Disparate Treatment vs. Disparate Impact](#4-disparate-treatment-vs-disparate-impact)
5. [Mitigation: Pre-, In-, and Post-Processing](#5-mitigation-pre--in--and-post-processing)
6. [Proxy Discrimination and Redlining-by-Algorithm](#6-proxy-discrimination-and-redlining-by-algorithm)
7. [Fair Lending & Adverse-Action Context](#7-fair-lending--adverse-action-context)
8. [How to Audit for Bias and Fairness](#how-to-audit-for-bias-and-fairness)
9. [Common Pitfalls](#common-pitfalls)
10. [Tooling](#tooling)
11. [Further Reading](#further-reading)

### Notation
Throughout, let $Y$ be the true outcome (e.g., $Y=1$ = "repaid loan"), $\hat{Y}$ the model's binary decision, $R$ or $S$ a continuous risk score, and $A$ a protected attribute (e.g., race, sex). "Privileged" and "unprivileged" denote the advantaged and disadvantaged groups under $A$.

---

## 1. Group vs. Individual Fairness, and Intersectional Bias

### Group fairness
**Group (statistical) fairness** asks for parity of some statistic across groups defined by $A$ — equal selection rates, equal error rates, equal calibration. It is what most metrics and most law operate on, and it is easy to measure. Its weakness is that satisfying a group average can still treat specific individuals unjustly, and it says nothing about *why* a disparity exists.

### Individual fairness
**Individual fairness** (Dwork et al., 2012) takes the principle that *similar individuals should be treated similarly* and formalizes it as a Lipschitz condition: the difference in outcomes for two people should be bounded by how dissimilar they are under a task-relevant distance metric $d$. The hard part is defining $d$ — it requires a notion of "relevant similarity" that itself can encode bias, which is why individual fairness is conceptually clean but rarely operationalized in production audits.

### Intersectional bias and fairness gerrymandering
A model can look fair on each protected attribute *marginally* (by race, by sex) yet badly mistreat an **intersection** (e.g., Black women). Optimizing for marginal group fairness while ignoring subgroups is called **fairness gerrymandering** (Kearns et al., 2018). The number of intersectional subgroups grows combinatorially, so auditors must (a) prioritize legally and ethically salient intersections and (b) watch for small-sample noise that makes subgroup metrics unstable.

### Where bias comes from
A useful taxonomy (Suresh & Guttag) locates bias at distinct points in the pipeline:

| Source | Where it enters | Example |
|--------|-----------------|---------|
| **Historical bias** | The world, before any data is collected | Past lending discrimination baked into "who repaid" |
| **Representation bias** | Sampling the population | Under-sampling a minority group |
| **Measurement bias** | Choosing features/labels/proxies | Using "arrests" as a proxy for "crime" |
| **Aggregation bias** | One model for distinct groups | A single risk cutoff that fits the majority |
| **Learning / evaluation bias** | Objective and benchmarks | Optimizing accuracy on an unrepresentative test set |
| **Deployment bias** | Use differs from design intent | A triage tool used as a final decision |

Naming the *source* matters: a representation problem is fixed with data; a measurement problem is fixed by redefining the target; a historical problem may not be fixable inside the model at all.

---

## 2. Fairness Metrics

Most group-fairness metrics fall into **three families** (Barocas, Hardt & Narayanan). Understanding the families is more durable than memorizing metric names, because the families are exactly what the impossibility results pit against each other.

### Independence — "decisions are independent of group"
The prediction $\hat{Y}$ is statistically independent of $A$. This is **demographic / statistical parity**:

$$P(\hat{Y}=1 \mid A=a) = P(\hat{Y}=1 \mid A=b) \quad \forall a,b$$

In legal/audit practice this is operationalized as the **disparate impact ratio** (and the four-fifths rule, §4):

$$\text{DI ratio} = \frac{P(\hat{Y}=1 \mid A=\text{unprivileged})}{P(\hat{Y}=1 \mid A=\text{privileged})} \geq 0.8$$

*Limitation:* it ignores the ground truth $Y$, so enforcing it can force equal selection rates even when base rates genuinely differ, harming accuracy and sometimes the protected group itself.

### Separation — "errors are equal across groups"
Predictions are independent of $A$ *conditional on the true label*. The strong form is **equalized odds** (equal true-positive and false-positive rates):

$$P(\hat{Y}=1 \mid Y=y, A=a) = P(\hat{Y}=1 \mid Y=y, A=b) \quad \forall y \in \{0,1\}$$

The relaxed form is **equal opportunity** (equal true-positive rate only — equal chance of a favorable outcome among those who qualify; Hardt, Price & Srebro, 2016):

$$P(\hat{Y}=1 \mid Y=1, A=a) = P(\hat{Y}=1 \mid Y=1, A=b)$$

### Sufficiency — "a score means the same thing for everyone"
The outcome is independent of $A$ *conditional on the score*. This is **calibration within groups** (and its threshold form, **predictive parity**):

$$P(Y=1 \mid R=r, A=a) = P(Y=1 \mid R=r, A=b) \quad \forall r$$

A model is well-calibrated by group if, among everyone it scores at 0.7, roughly 70% are positives — *in each group*. Risk scores used in credit and criminal justice are typically built to satisfy sufficiency.

### Quick reference
| Metric | Family | Condition | Use when |
|--------|--------|-----------|----------|
| Demographic parity | Independence | Equal positive-prediction rate across groups | Equal *access*/representation is the goal (e.g., outreach, ads) |
| Disparate impact ratio | Independence | Ratio of selection rates $\geq 0.8$ | Legal adverse-impact screening |
| Equalized odds | Separation | Equal TPR *and* FPR across groups | Errors in both directions are costly |
| Equal opportunity | Separation | Equal TPR across groups | Missing a qualified person is the main harm |
| Predictive parity | Sufficiency | Equal precision / PPV across groups | A positive decision must carry equal meaning |
| Calibration | Sufficiency | Scores mean the same risk per group | Scores drive downstream decisions |

> [!TIP]
> Match the metric to the *harm*. A hiring screen that wrongly rejects qualified candidates is an **equal-opportunity** question. A credit score that overstates risk for one group is a **calibration/predictive-parity** question. An ad-delivery system steering opportunity away from a group is a **demographic-parity** question.

---

## 3. The Impossibility Results

You generally cannot satisfy independence, separation, and sufficiency at the same time. The sharpest version (Kleinberg, Mullainathan & Raghavan, 2016; Chouldechova, 2017):

> When **base rates differ between groups** and the classifier is not perfect, no model can simultaneously achieve **calibration** (sufficiency) and **balanced error rates** (separation — equal FPR and FNR). You can have at most a subset.

This is the mathematical core of the **COMPAS debate**: the tool was *calibrated* across race (a given score meant a similar reoffense rate for everyone), yet it had *unequal false-positive rates* (more non-reoffending Black defendants were flagged high-risk). Both observations were correct; they were measuring different fairness families that cannot both be satisfied when arrest base rates differ.

**What this means for an auditor:**
- Fairness is a **trade-off to be chosen**, not a box to be checked. Demanding "all metrics green" is a sign of an immature program.
- The choice of definition encodes a **value judgment** about which errors matter most and to whom. Document the rationale, the stakeholders consulted, and the alternatives rejected.
- Closing a metric gap on one family will usually open or widen a gap on another. Report the *full* picture, not the one metric that happens to pass.

---

## 4. Disparate Treatment vs. Disparate Impact

US anti-discrimination law (and most analogues) recognizes two theories of liability. Auditors must test for both, because a model can avoid one and commit the other.

### Disparate treatment
**Intentional** or facially discriminatory differential treatment — using a protected attribute (or an obvious stand-in) directly in a decision. In ML terms: feeding $A$ into the model, or applying different rules/thresholds by group. Easy to *describe*, harder to detect when intent is hidden inside features.

### Disparate impact
A **facially neutral** practice that falls more harshly on a protected group and is not justified by necessity. Established for employment in *Griggs v. Duke Power Co.* (1971) and applied in fair-lending enforcement. This is the dominant risk for ML systems, because models can produce impact without anyone intending it. The standard burden-shifting framework:

1. **Prima facie** — the challenger shows a disparity (often via the four-fifths rule or a statistical-significance test).
2. **Business necessity** — the operator must show the practice is job-related / predictive of a legitimate objective.
3. **Less discriminatory alternative (LDA)** — even if justified, the challenger can prevail by showing an equally effective, less discriminatory option exists. *Searching for an LDA is a core audit task for ML models.*

### The four-fifths (80%) rule
From the EEOC's **Uniform Guidelines on Employee Selection Procedures** (29 CFR Part 1607): a selection rate for any group that is **less than 80% of the rate of the highest group** is generally treated as evidence of adverse impact.

$$\text{adverse impact if} \quad \frac{\text{selection rate of group}}{\text{selection rate of highest group}} < 0.8$$

Caveats every auditor should state:
- It is a **rule of thumb, not a safe harbor** — passing it does not prove non-discrimination, and failing it does not prove discrimination.
- It is **unreliable on small samples**; agencies and courts also consider **statistical significance** and **practical significance**.
- Passing the ratio while having a statistically significant disparity (large $N$) can still draw scrutiny.

### The legal–technical tension
"Fairness through unawareness" (just drop $A$) does **not** defeat disparate impact, because proxies remain (§6). Conversely, some technical fixes — applying **group-specific thresholds** to equalize outcomes — can themselves look like **disparate treatment** because they make the decision turn on group membership. This live tension between *fairness through awareness* and *anti-classification* is unresolved and jurisdiction-dependent; flag it rather than assuming a fix is legally safe.

---

## 5. Mitigation: Pre-, In-, and Post-Processing

Interventions are categorized by *where in the pipeline* they act. Each trades accuracy, transparency, and legal exposure differently.

### Pre-processing — fix the data
Act on the training data before the model sees it.
- **Reweighing** — reweight examples so $(A, Y)$ combinations are balanced.
- **Resampling** — over/under-sample to correct representation.
- **Disparate impact remover** — transform features to reduce their correlation with $A$ while preserving rank within groups (Feldman et al.).
- **Learning fair representations** — encode data into a representation that obscures $A$ but retains task signal.

*Pros:* model-agnostic, transparent, addresses root data issues. *Cons:* can degrade utility; cannot fix bias introduced later.

### In-processing — fix the training
Build fairness into the optimization.
- **Fairness constraints / regularizers** — add a penalty for a fairness violation to the loss.
- **Constrained optimization / reductions** — reduce fair classification to a sequence of weighted problems (the approach behind Fairlearn's `ExponentiatedGradient`).
- **Adversarial debiasing** — train a predictor while an adversary tries to recover $A$ from its outputs; the predictor wins by removing group signal (Zhang et al.).

*Pros:* often the best accuracy–fairness frontier. *Cons:* couples fairness to the model internals; harder to explain and re-validate.

### Post-processing — fix the outputs
Adjust a trained model's decisions.
- **Group-specific thresholds** — choose per-group decision thresholds to equalize a chosen metric (Hardt et al.).
- **Reject-option classification** — flip uncertain decisions near the boundary in favor of the unprivileged group.
- **Calibrated equalized odds** — post-hoc adjustment to trade calibration against equalized odds.

*Pros:* works on black-box / vendor models you can't retrain; simple. *Cons:* **group-specific thresholds raise a disparate-treatment concern** (see §4); can leave the underlying model unfair.

| Stage | Acts on | Strengths | Watch-outs |
|-------|---------|-----------|------------|
| Pre-processing | Data | Transparent, root-cause, model-agnostic | Utility loss; downstream bias survives |
| In-processing | Training objective | Best accuracy–fairness trade-off | Opaque; tied to model; re-validation cost |
| Post-processing | Outputs/thresholds | Works on black-box models; simple | Group thresholds may be unlawful; model still biased |

> [!NOTE]
> Mitigation is not complete until you re-measure **all** affected metrics, confirm utility on the protected group did not collapse, and document why the residual disparity is acceptable. A mitigation that improves one metric and silently worsens another is a finding, not a fix.

---

## 6. Proxy Discrimination and Redlining-by-Algorithm

**Proxy discrimination** occurs when a model relies on features that are *correlated with* a protected attribute, reproducing its effect without naming it. Classic proxies: **ZIP code** and geography (correlated with race), **names**, language, education institution, device/browser, and shopping or social-graph patterns.

This is the digital descendant of **redlining** — the historical practice of denying services to neighborhoods by race. "**Algorithmic redlining**" reaches the same outcome through learned geographic and behavioral correlates, which is precisely why "fairness through unawareness" fails: a sufficiently rich feature set lets the model **reconstruct** $A$ even when $A$ is excluded.

**Detecting proxies in an audit:**
- Train a model to **predict $A$ from the features**; high accuracy means redundant encoding exists.
- Inspect **feature attributions** (SHAP) for features that move predictions and correlate with group membership.
- Test **drop / permutation** of suspect features and measure the change in the disparity.
- Use **conditional** analyses (does the disparity persist after controlling for a legitimate factor?).

Real-world patterns to probe: lending and insurance pricing driven by geography; **ad-delivery** systems that skew the audience for housing/employment ads even when the advertiser targets neutrally; pricing that varies with proxies for income or protected status.

> [!CAUTION]
> Removing the obvious proxy is rarely enough — models route around it through *other* correlated features. Treat proxy analysis as iterative, and prefer testing the *outcome disparity* over chasing individual features.

---

## 7. Fair Lending & Adverse-Action Context

Credit is the most mature regulatory arena for algorithmic fairness, and a frequent audit target.

### ECOA and Regulation B
The **Equal Credit Opportunity Act (ECOA)**, implemented by **Regulation B**, prohibits credit discrimination on bases including race, color, religion, national origin, sex, marital status, age, and receipt of public assistance. Both **disparate treatment** and **disparate impact** theories apply to credit. Two operational consequences for ML:

- **Adverse-action notices.** When credit is denied or terms are worsened, the creditor must provide the **specific, principal reasons** for the decision. Regulators (CFPB) have made clear that using a **complex or "black-box" model is not an excuse** — if the model's actual reasons aren't captured by standard checklist forms, the creditor must still disclose accurate, specific reasons. This makes **explainability (§ XAI in the roadmap) a compliance requirement**, not a nicety. (The **FCRA** imposes its own adverse-action and disclosure duties when consumer reports are used.)
- **Proxy methodology.** Because ECOA generally restricts collecting applicant race/ethnicity for non-mortgage credit, fair-lending testing typically *infers* group membership using **BISG (Bayesian Improved Surname Geocoding)** to estimate demographics from surname and geography, then measures disparities on the inferred groups.

### Disparate-impact testing in lending
The interagency fair-lending framework expects creditors to monitor for disparate impact, justify any disparity by **business necessity**, and search for a **less discriminatory alternative** (§4). For models, the LDA search is concrete: can a model with similar predictive performance be found that reduces the disparity (different features, different objective, different thresholds)?

### SR 11-7 and model risk management
The Federal Reserve / OCC **SR 11-7** (and OCC Bulletin 2011-12), *Supervisory Guidance on Model Risk Management*, governs the lifecycle around any model — including credit and fair-lending models. Its three pillars:

1. **Robust model development, implementation, and use** — sound design, data, testing, and documentation.
2. **Model validation** — independent review with **"effective challenge"**: critical scrutiny by parties with the standing and incentive to push back, covering conceptual soundness, ongoing monitoring, and outcomes analysis (including backtesting and benchmarking).
3. **Governance, policies, and controls** — roles, model inventory, and oversight.

SR 11-7 does **not itself prescribe fairness metrics**, but fair-lending and bias testing sit squarely inside *validation* and *ongoing monitoring*. For an auditor, SR 11-7 supplies the governance scaffolding (effective challenge, validation evidence, inventory) into which the fairness analysis above must be slotted and documented.

> [!TIP]
> In a regulated-lending audit, tie the threads together: **ECOA/Reg B** sets the prohibited bases and the adverse-action duty; **disparate-impact doctrine** sets the burden-shifting test and the LDA obligation; **explainability** makes adverse-action reasons accurate; and **SR 11-7** governs the validation and monitoring lifecycle that holds all of it.

---

## How to Audit for Bias and Fairness

A repeatable workflow. Document a decision and its rationale at every step — the documentation *is* the deliverable.

1. **Define the context and protected groups.** Identify the legal regime (employment, credit, housing, etc.), the protected attributes that apply, and the real-world harm in scope.
2. **Choose the fairness definition(s) — and justify them.** Map the harm to a metric family (§2). Record why this definition was selected and which were rejected, given the impossibility results (§3).
3. **Obtain or infer group membership.** Use collected attributes where lawful; otherwise apply a proxy method such as **BISG** for lending, and document its error.
4. **Measure.** Compute selection rates and the **disparate-impact ratio**, error-rate gaps (TPR/FPR), and **calibration by group**. Run a **statistical-significance** test, not just the four-fifths ratio. Repeat for salient **intersections**.
5. **Investigate proxies and root cause.** Predict $A$ from features; review SHAP attributions; perform drop/permutation tests (§6). Locate the *source* of bias (§1).
6. **Evaluate mitigation and the less-discriminatory alternative.** Try pre/in/post interventions (§5); for each, re-measure **all** metrics and group utility. Demonstrate whether an LDA exists.
7. **Assess the residual.** State the disparity that remains after mitigation and why it is (or is not) acceptable, with the business-necessity argument where relevant.
8. **Report and set up monitoring.** Findings with risk ratings, the metric trade-off chosen, evidence, and a **continuous-monitoring** plan with thresholds and triggers for re-audit (drift erodes fairness over time).

### Audit checklist
- [ ] Legal regime and protected groups identified and documented.
- [ ] Fairness definition chosen, justified, and trade-offs acknowledged.
- [ ] Group membership obtained or proxied (method + error documented).
- [ ] Selection rates, DI ratio, and error-rate/calibration gaps computed.
- [ ] Statistical **and** practical significance tested (not just 4/5ths).
- [ ] Intersectional subgroups checked for fairness gerrymandering.
- [ ] Proxy / redundant-encoding analysis performed.
- [ ] Mitigation tested with full re-measurement of all metrics.
- [ ] Less-discriminatory-alternative search documented.
- [ ] Adverse-action reasons accurate and specific (if credit/FCRA).
- [ ] Residual disparity, rationale, and monitoring plan recorded.

---

## Common Pitfalls

- **Treating fairness as binary.** "The model is fair" is not an auditable claim; "the model satisfies equal opportunity within 3 points across groups A and B, at the cost of a calibration gap" is.
- **Fairness through unawareness.** Dropping the protected attribute while leaving its proxies in place.
- **One-metric reporting.** Showing the single metric that passes and omitting the family it trades against.
- **Ignoring base rates.** Demanding demographic parity where base rates genuinely differ can harm utility and sometimes the protected group.
- **Small-sample subgroup noise.** Reading deep intersections as signal when they are statistical noise.
- **Group thresholds without legal review.** A technically tidy fix that can constitute disparate treatment.
- **Point-in-time only.** Skipping ongoing monitoring; fairness drifts as data and populations shift.
- **Black-box adverse-action notices.** Assuming model complexity excuses vague denial reasons — it does not.

---

## Tooling

From the roadmap's [Tools](./README.md#tools) section, the bias-and-fairness workhorses:

- **AI Fairness 360 (AIF360)** — broad metrics + pre/in/post mitigation: https://github.com/Trusted-AI/AIF360
- **Fairlearn** — group metrics and the reductions/threshold mitigators: https://github.com/fairlearn/fairlearn
- **Aequitas** — audit-oriented bias report and disparity tables: https://github.com/dssg/aequitas
- **What-If Tool** — interactive fairness/threshold exploration: https://github.com/PAIR-code/what-if-tool
- **SHAP** — feature attribution for proxy investigation: https://github.com/shap/shap

---

## Further Reading

**Foundational papers**
- Dwork, Hardt, Pitassi, Reingold & Zemel — *Fairness Through Awareness* (2012) — individual fairness.
- Hardt, Price & Srebro — *Equality of Opportunity in Supervised Learning* (2016) — equalized odds / equal opportunity / post-processing.
- Kleinberg, Mullainathan & Raghavan — *Inherent Trade-Offs in the Fair Determination of Risk Scores* (2016) — impossibility.
- Chouldechova — *Fair Prediction with Disparate Impact* (2017) — the COMPAS / calibration vs. error-balance result.
- Kearns, Neel, Roth & Wu — *Preventing Fairness Gerrymandering* (2018) — subgroup/intersectional fairness.
- Feldman, Friedler, Moeller, Scheidegger & Venkatasubramanian — *Certifying and Removing Disparate Impact* (2015).
- Suresh & Guttag — *A Framework for Understanding Sources of Harm Throughout the ML Life Cycle* (2021).

**Books**
- Barocas, Hardt & Narayanan — *Fairness and Machine Learning* — free at https://fairmlbook.org (the independence/separation/sufficiency framing used above).
- Cathy O'Neil — *Weapons of Math Destruction*.

**Legal & regulatory primary sources**
- *Griggs v. Duke Power Co.*, 401 U.S. 424 (1971) — disparate impact.
- EEOC **Uniform Guidelines on Employee Selection Procedures**, 29 CFR Part 1607 — the four-fifths rule.
- **Equal Credit Opportunity Act (ECOA)** and **Regulation B** — fair lending and adverse-action notices.
- **SR 11-7** / OCC Bulletin 2011-12 — *Supervisory Guidance on Model Risk Management*.
- CFPB guidance on adverse-action notices for credit decisions made with complex algorithms.

---

*Part of the [ML/AI Auditing roadmap](./README.md). Corrections and additions welcome via pull request.*

# Foundations of AI/ML

> The vocabulary and mechanics you need *before* you can audit a model. Companion to the [ML/AI Auditing roadmap](./README.md).

You do not need to build models to audit them, but you do need to read them. This page covers the minimum working knowledge of how machine learning systems are trained and evaluated — framed throughout for the **auditor's job**: knowing what artifacts to expect, what questions to ask, and where things quietly go wrong. Each section pairs *what it is* with *what an auditor checks*.

> [!IMPORTANT]
> Most consequential audit findings at this layer are mundane, not exotic: a test set that wasn't really held out, a headline accuracy that hides failure on a minority class, a feature that leaks the answer, or an objective function that optimizes the wrong thing. Fluency in the basics is what lets you catch them.

---

## Table of Contents

1. [Learning Paradigms: Supervised, Unsupervised, Reinforcement](#1-learning-paradigms-supervised-unsupervised-reinforcement)
2. [Classification vs. Regression; Loss & Optimization](#2-classification-vs-regression-loss--optimization)
3. [Splits, Cross-Validation, Over/Underfitting](#3-splits-cross-validation-overunderfitting)
4. [Performance Metrics](#4-performance-metrics)
5. [Deep Learning, Transformers & Attention](#5-deep-learning-transformers--attention)
6. [Foundation Models, Fine-Tuning, RAG & Prompting](#6-foundation-models-fine-tuning-rag--prompting)
7. [Feature Engineering, Data Leakage & Proxy Variables](#7-feature-engineering-data-leakage--proxy-variables)
8. [Reading ML Artifacts as an Auditor](#reading-ml-artifacts-as-an-auditor)
9. [Further Reading](#further-reading)

### Notation
$X$ = input features, $Y$ = true label/target, $\hat{Y}$ = prediction, $\hat{p}$ = predicted probability, $\theta$ = model parameters, $L$ = loss, $\eta$ = learning rate.

---

## 1. Learning Paradigms: Supervised, Unsupervised, Reinforcement

How a model learns determines how you validate it.

**Supervised learning** maps inputs to known labels ($X \rightarrow Y$) using labeled examples. It covers most high-stakes audited systems — credit scoring, hiring screens, fraud detection. Because ground truth exists, evaluation is comparatively straightforward (you can measure error against labels).

**Unsupervised learning** finds structure in *unlabeled* data: clustering, dimensionality reduction, anomaly detection, and representation learning. There is no label to score against, so "correctness" is harder to define and validation leans on downstream usefulness, stability, and human review.

**Reinforcement learning (RL)** trains an agent to choose actions in an environment to maximize a cumulative **reward**, via a **policy**. The central risk is **reward misspecification** ("reward hacking") — the agent optimizes the stated reward in ways that violate the intended goal. RL also underpins **RLHF** (reinforcement learning from human feedback), used to align large language models.

**Self-/semi-supervised learning** uses the data's own structure to create training signal (e.g., predicting the next token). This is how **foundation models** are pretrained (§6).

| Paradigm | Data | Typical tasks | Primary audit concern |
|----------|------|---------------|------------------------|
| Supervised | Labeled $X, Y$ | Classification, regression | Label quality; representativeness; metric choice |
| Unsupervised | Unlabeled $X$ | Clustering, anomaly detection | No ground truth → how is "good" defined and validated? |
| Reinforcement | Reward signal | Sequential decisions, control, RLHF | Reward misspecification; exploration risk; safety |
| Self-supervised | Unlabeled at scale | Pretraining foundation models | Provenance and composition of pretraining data |

---

## 2. Classification vs. Regression; Loss & Optimization

**Classification** predicts a discrete class (binary, multiclass, or multilabel — e.g., approve/deny). **Regression** predicts a continuous value (e.g., expected loss, price). The task type dictates the metrics (§4) and the loss.

**The loss function defines what the model optimizes — and therefore what it ignores.** Common choices:

- Regression — **Mean Squared Error** (penalizes large errors heavily) or **Mean Absolute Error**:

$$\text{MSE} = \frac{1}{n}\sum_{i=1}^{n}(y_i - \hat{y}_i)^2$$

- Classification — **cross-entropy / log loss** (and hinge loss for SVMs):

$$L = -\frac{1}{n}\sum_{i=1}^{n}\big[\,y_i \log \hat{p}_i + (1-y_i)\log(1-\hat{p}_i)\,\big]$$

**Optimization** finds the parameters that minimize the loss, almost always by **gradient descent** and its stochastic variants (SGD, Adam):

$$\theta \leftarrow \theta - \eta\,\nabla_\theta L(\theta)$$

Key knobs (**hyperparameters**): learning rate $\eta$, batch size, number of epochs, and regularization strength. These are tuned on the validation set (§3), never the test set.

> [!TIP]
> **Audit lens — the objective is a value choice.** A model optimizing average loss does *not* automatically care about worst-case errors, rare classes, or fairness. Check that the loss reflects the real-world **cost asymmetry**: if a false negative (e.g., missing fraud, or denying a qualified applicant) is far costlier than a false positive, the objective and thresholds should say so. A mismatch between the optimized objective and the business/ethical goal is a finding in itself.

---

## 3. Splits, Cross-Validation, Over/Underfitting

### The three splits
- **Training set** — the model learns from it.
- **Validation set** — used to tune hyperparameters and select models.
- **Test set** — touched **once**, at the end, to estimate generalization. If it influences any decision, it is no longer a test set.

**Cross-validation** (e.g., $k$-fold) rotates the validation role across folds to use data efficiently and reduce variance in the estimate. Use **stratified** folds to preserve class balance, and **time-based** splits for temporal data so the model never trains on the future.

### Overfitting, underfitting, and the bias–variance trade-off
- **Overfitting** — the model memorizes training noise; great train accuracy, poor test accuracy (high variance).
- **Underfitting** — the model is too simple to capture the signal (high bias).

Expected test error decomposes (conceptually) as:

$$\text{Error} \approx \text{Bias}^2 + \text{Variance} + \text{Irreducible noise}$$

**Regularization** (L1/L2 penalties, dropout, early stopping) and more data combat overfitting. The gap between training and test performance is the **generalization gap**.

> [!CAUTION]
> **Audit lens — data hygiene is where audits live.** Verify, with evidence:
> - Was the test set **truly held out** and never used for tuning or model selection?
> - Is reported performance from the **test** set, or quietly from the validation set?
> - For temporal problems, was the split **time-ordered** (no training on future data)?
> - Could records overlap across splits (the same customer in train *and* test)?
> "Test-set contamination" and look-ahead leakage are among the most common reasons production performance collapses below reported numbers.

---

## 4. Performance Metrics

All classification metrics derive from the **confusion matrix** (true/false × positive/negative). Pick metrics that match the **harm** and the **class balance** — a single headline number rarely tells the truth.

**Accuracy** is the share of correct predictions:

$$\text{Accuracy} = \frac{TP + TN}{TP + TN + FP + FN}$$

It is **misleading on imbalanced data**: a model that always predicts "not fraud" can score 99% accuracy while catching zero fraud (the base-rate trap).

**Precision** (of those flagged, how many were right) and **recall / sensitivity** (of the true positives, how many were caught) trade off against each other; **F1** is their harmonic mean:

$$\text{Precision} = \frac{TP}{TP + FP}, \qquad \text{Recall} = \frac{TP}{TP + FN}, \qquad F_1 = 2\cdot\frac{\text{Precision}\cdot\text{Recall}}{\text{Precision} + \text{Recall}}$$

**ROC-AUC** summarizes the true-positive vs. false-positive trade-off across all thresholds; it is **threshold-independent**, but can look optimistic on heavy imbalance, where a **Precision–Recall curve** is more informative.

**Calibration** asks whether predicted probabilities match observed frequencies — among cases scored 0.7, about 70% should be positive. It matters whenever **scores drive decisions** (pricing, risk tiers) rather than just rankings. (This is the *sufficiency* family in the [bias & fairness](./bias-fairness-non-discrimination.md) page.)

For **regression**: RMSE and MAE (error magnitude) and $R^2$ (variance explained).

| Metric | Reads as | Threshold-dependent? | Best when |
|--------|----------|----------------------|-----------|
| Accuracy | Overall correctness | Yes | Classes are balanced |
| Precision | Trustworthiness of a positive flag | Yes | False positives are costly |
| Recall | Coverage of true positives | Yes | False negatives are costly |
| F1 | Balance of precision & recall | Yes | Imbalanced classes |
| ROC-AUC | Ranking quality across thresholds | No | Comparing models, moderate balance |
| PR-AUC | Precision across recall levels | No | Severe class imbalance |
| Calibration | Probabilities mean what they say | No (per-score) | Scores drive downstream decisions |

> [!TIP]
> **Audit lens.** Never accept a lone accuracy figure on imbalanced data. Confirm the metric matches the cost asymmetry, ask for the **confusion matrix at the operating threshold**, check **calibration** if probabilities are used, and require metrics **disaggregated by subgroup** — an aggregate that looks fine can hide failure on a protected group.

---

## 5. Deep Learning, Transformers & Attention

**Neural networks** stack layers of weighted connections and non-linear **activation functions**; they learn by **backpropagation** (gradient descent through the layers). Their strength is **representation learning** — discovering useful features automatically rather than hand-crafting them — which pays off at scale.

**Transformers** are the architecture behind modern language and multimodal models. Conceptually:
- Text is split into **tokens** and mapped to **embeddings** (vectors).
- **Self-attention** lets each token weigh the relevance of every other token, capturing context. The canonical operation:

$$\text{Attention}(Q, K, V) = \text{softmax}\!\left(\frac{QK^\top}{\sqrt{d_k}}\right)V$$

where queries $Q$, keys $K$, and values $V$ are learned projections of the input. **Multi-head attention** runs several such operations in parallel, and **positional encodings** inject word order.

You don't need to derive this to audit it — but you should recognize that these models are **highly opaque**, exhibit behavior that emerges with scale, and resist simple feature-level explanation.

> [!NOTE]
> **Audit lens.** As models get deeper, weight-level interpretation becomes impractical, so you shift to auditing **behavior and process**: systematic evaluation, red-teaming, documentation, and the governance around the model. The explainability burden rises sharply — see the roadmap's XAI topic and the [bias & fairness](./bias-fairness-non-discrimination.md) page for how explanation connects to compliance (e.g., adverse-action notices).

---

## 6. Foundation Models, Fine-Tuning, RAG & Prompting

Modern practice follows a **pretrain → adapt** paradigm.

**Foundation models** are large models pretrained (usually self-supervised) on broad data, then adapted to many tasks. Most organizations **consume** these rather than train them.

**Adaptation methods:**
- **Fine-tuning** — continue training on task/domain data. **Full** fine-tuning updates all weights; **parameter-efficient** methods (**LoRA**, adapters) update a small subset cheaply.
- **Instruction tuning / RLHF** — align the model to follow instructions and human preferences.

**Retrieval-Augmented Generation (RAG)** grounds a model's output in **external documents** retrieved at query time. It reduces (but does not eliminate) hallucination and keeps answers current, but the output is only as good as the **retrieval quality and the source corpus**.

**Prompting / in-context learning** steers a model via the input (including a **system prompt**) with no weight changes. Outputs can be **sensitive** to small prompt wording changes.

> [!CAUTION]
> **Audit lens — you are usually auditing someone else's model plus your thin layer.** This makes foundations a **third-party / supply-chain** problem (roadmap §14). Probe:
> - **Provenance** of the base model and what is known about its training data, license, and documented limitations (its model/system card).
> - **Fine-tuning data** governance — where it came from, consent, and whether it embeds bias or leaks sensitive content.
> - **RAG source governance** — corpus accuracy, freshness, access controls, and whether retrieval can surface stale or unauthorized content.
> - **Prompt-injection surface** (direct and indirect) — a security concern when prompts incorporate untrusted text or tool outputs.
> - **Non-determinism and version drift** — vendor model updates can silently change behavior; pin versions and re-test.

---

## 7. Feature Engineering, Data Leakage & Proxy Variables

**Feature engineering** prepares inputs the model can use: encoding categoricals, scaling/normalizing, deriving new features, and selecting which to keep. Done well it improves performance; done carelessly it introduces bias and leakage.

**Data leakage** is information in the features that would *not* legitimately be available at decision time, inflating performance that then collapses in production. Two flavors:
- **Target leakage** — a feature encodes the answer or future information (e.g., "number of late-payment reminders sent" when predicting default; the reminders happen *after* trouble starts).
- **Train–test leakage** — preprocessing (scaling, imputation, feature selection) fit on the full dataset before splitting, so test information bleeds into training.

Leakage is the most common cause of "too good to be true" results. The fix is discipline: **fit every transformation on the training fold only**, and ask of each feature, *was this knowable at the moment of decision?*

**Proxy variables** carry two distinct meanings — keep them separate in your report:
1. **Proxies for the target** → a leakage problem (a feature that stands in for the answer).
2. **Proxies for a protected attribute** → a fairness problem: a neutral-looking feature (ZIP code, name, device) that reconstructs race/sex/etc. and produces disparate impact even when the protected attribute is excluded. This is covered in depth in the [bias & fairness](./bias-fairness-non-discrimination.md#6-proxy-discrimination-and-redlining-by-algorithm) page.

> [!TIP]
> **Audit lens.** Trace every feature's **provenance and availability-at-decision-time**; hunt for **temporal leakage** specifically; flag features that proxy **protected attributes**; and check **feature stability/drift** over time, since a feature's meaning can shift after deployment.

---

## Reading ML Artifacts as an Auditor

Foundations come together in the documents you should expect to receive — and the cross-cutting questions you ask of them.

**Artifacts to request:**
- **Model card** — intended use, performance (disaggregated), limitations, and ethical considerations.
- **Datasheet** for the dataset — provenance, composition, collection, and known biases.
- **Split methodology** — how train/validation/test were created (and, for temporal data, the time boundary).
- **Training & evaluation logs** — hyperparameters, metrics, and the evaluation protocol.
- **Monitoring plan** — drift detection, retraining triggers, and ownership.

**Cross-cutting questions:**
- Does the **objective** match the real-world goal and its cost asymmetry?
- Is reported performance from a **clean, held-out** test set, with no leakage?
- Are the **metrics** appropriate for the class balance and the harm, and **disaggregated by group**?
- Are **probabilities calibrated** if they drive decisions?
- For foundation models: what is the **base model's provenance**, and who owns **version/drift** risk?
- Which features are **proxies** — for the target (leakage) or for protected attributes (fairness)?

### Quick checklist
- [ ] Learning paradigm identified; validation approach fits it.
- [ ] Loss/objective matches the intended goal and cost asymmetry.
- [ ] Test set verifiably held out; splits are leakage-free (temporal where relevant).
- [ ] Metrics match class balance and harm; subgroup-disaggregated.
- [ ] Calibration checked when scores drive decisions.
- [ ] Foundation-model provenance, fine-tuning data, and RAG sources documented.
- [ ] Version-drift and non-determinism risk addressed.
- [ ] Every feature traced for provenance, availability, leakage, and proxy status.

---

## Further Reading

**Courses (free)**
- Google **Machine Learning Crash Course** — https://developers.google.com/machine-learning/crash-course
- **fast.ai** — *Practical Deep Learning for Coders* — https://course.fast.ai
- Andrew Ng — *Machine Learning Specialization* (Coursera).

**Books (several free online)**
- James, Witten, Hastie & Tibshirani — *An Introduction to Statistical Learning* — free at https://www.statlearning.com
- Goodfellow, Bengio & Courville — *Deep Learning* — free at https://www.deeplearningbook.org
- Zhang et al. — *Dive into Deep Learning* — free at https://d2l.ai
- Aurélien Géron — *Hands-On Machine Learning with Scikit-Learn, Keras & TensorFlow*.
- Christoph Molnar — *Interpretable Machine Learning* — free at https://christophm.github.io/interpretable-ml-book/

**Transformers & foundation models**
- Vaswani et al. — *Attention Is All You Need* (2017) — the transformer paper.
- Jay Alammar — *The Illustrated Transformer* — https://jalammar.github.io/illustrated-transformer/
- Bommasani et al. — *On the Opportunities and Risks of Foundation Models* (2021).

**Pitfalls**
- Kapoor & Narayanan — *Leakage and the Reproducibility Crisis in ML-based Science* (2023) — essential on data leakage.

---

*Part of the [ML/AI Auditing roadmap](./README.md). Corrections and additions welcome via pull request.*

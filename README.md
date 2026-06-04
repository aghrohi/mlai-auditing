# ML / AI Auditing — A Learning Roadmap

> A curated, self-study roadmap for learning how to **audit machine learning and AI systems** — from foundations and governance through hands-on bias, robustness, and security testing, to certifications, standards, and the people worth following.

**AI/ML auditing** is the independent, evidence-based assessment of an AI/ML system *and the governance around it* against defined criteria — covering governance and accountability, data quality, model behavior (bias/fairness, robustness, explainability, privacy, security), documentation, human oversight, and regulatory compliance, across the full lifecycle.

**Who this is for:** internal/IT auditors, GRC and risk/compliance professionals, ML engineers and data scientists, AI governance leads, lawyers and policy folks, and anyone preparing for credentials like **AIGP**, **AAIA**, **FHCA**, or **ISO/IEC 42001 Lead Auditor**.

> [!NOTE]
> This is a living document. Links and details (especially certifications, standards, and tools) change quickly — verify against the official source before relying on anything here. Corrections and additions via PR are welcome.

---

## Table of Contents

1. [All Topics](#all-topics)
2. [Subtopics](#subtopics)
3. [Mastering](#mastering)
4. [Tools](#tools)
5. [Certifications](#certifications)
6. [Standards](#standards)
7. [References](#references)
8. [YouTube Videos](#youtube-videos)
9. [Blogs](#blogs)
10. [Podcasts](#podcasts)

---

## All Topics

The discipline organized into its major pillars. Each maps to a body of knowledge you can study and test against.

| # | Pillar | What it covers |
|---|--------|----------------|
| 1 | **Foundations of AI/ML** | How models are built and behave; the vocabulary you need before you can audit one |
| 2 | **AI Governance & Accountability** | Policies, roles, oversight bodies, RACI, and decision rights for AI |
| 3 | **AI Risk Management** | Identifying, assessing, treating, and monitoring AI risk (NIST AI RMF, ISO/IEC 23894) |
| 4 | **The AI/ML Lifecycle & MLOps** | Data → training → validation → deployment → monitoring → retirement |
| 5 | **Audit Methodology & Assurance** | How to plan, scope, test, and report an audit; first/second/third-line assurance |
| 6 | **Data Governance & Quality** | Provenance, lineage, representativeness, labeling, drift, and documentation |
| 7 | **Bias, Fairness & Non-Discrimination** | Measuring disparate impact and mitigating it across protected groups |
| 8 | **Transparency & Explainability (XAI)** | Making model decisions inspectable and contestable |
| 9 | **Robustness, Safety & Reliability** | Performance under stress, edge cases, distribution shift, and failure modes |
| 10 | **Privacy & Data Protection** | PII handling, differential privacy, membership inference, GDPR/PIPEDA exposure |
| 11 | **AI Security & Adversarial ML** | Threats to and through models; red-teaming AI systems |
| 12 | **Generative AI & LLM Risks** | Hallucination, prompt injection, jailbreaks, toxicity, IP, and eval |
| 13 | **Agentic AI** | Autonomy, tool use, action-space risk, and oversight of multi-step agents |
| 14 | **Third-Party / Supply-Chain Risk** | Auditing foundation-model vendors and embedded AI you didn't build |
| 15 | **Human Oversight & Sociotechnical Factors** | Human-in-the-loop design, automation bias, and real-world impact |
| 16 | **Documentation & Evidence** | Model cards, datasheets, system cards, audit trails, and the evidence set |
| 17 | **Regulatory & Legal Landscape** | EU AI Act, US state laws, sectoral rules, and global frameworks |
| 18 | **Sector-Specific Auditing** | Finance/fair-lending, employment/HR, healthcare, insurance, public sector |
| 19 | **Continuous Monitoring & Post-Deployment** | Production drift, incident detection, and ongoing conformity |
| 20 | **Incident Response, Redress & Reporting** | What happens when an AI system causes harm |

---

## Subtopics

A deeper breakdown of each pillar. Use this as a study checklist.

### 1. Foundations of AI/ML
- Supervised, unsupervised, and reinforcement learning
- Classification vs. regression; loss functions and optimization
- Train/validation/test splits, cross-validation, overfitting/underfitting
- Performance metrics (accuracy, precision/recall, F1, ROC-AUC, calibration)
- Deep learning basics; transformers and attention at a conceptual level
- Foundation models, fine-tuning, RAG, and prompting
- Feature engineering, data leakage, and proxy variables

### 2. AI Governance & Accountability
- AI policy, standards, and operating models (centralized vs. federated)
- Roles and decision rights (AI ethics board, model owners, risk committee)
- AI inventories and model registries
- Three-lines model applied to AI
- Risk appetite and tolerance for AI
- Procurement, vendor due diligence, and contractual controls
- Board reporting and executive accountability

### 3. AI Risk Management
- Risk taxonomies (harms to individuals, groups, society, the organization)
- NIST AI RMF functions: **Govern, Map, Measure, Manage**
- ISO/IEC 23894 AI risk management process
- AI impact assessments (ISO/IEC 42005, DPIAs, FRIAs/EU AI Act)
- Inherent vs. residual risk; control design and operating effectiveness
- Risk registers and treatment plans for AI

### 4. The AI/ML Lifecycle & MLOps
- Problem framing and intended use
- Data collection, labeling, and pipelines
- Model development, experiment tracking, and versioning
- Validation, challenger models, and sign-off gates
- Deployment patterns (batch, online, edge), CI/CD for ML
- Model and data versioning, reproducibility, and rollback
- Decommissioning and model retirement

### 5. Audit Methodology & Assurance
- Audit objectives, criteria, scope, and the **Target of Evaluation**
- Internal vs. external (independent third-party) audit
- Pre-audit / readiness vs. certification audit
- Evidence gathering: interviews, documentation review, technical testing
- Sampling, testing of design (TOD) and testing of operating effectiveness (TOE)
- Findings, materiality, and risk rating
- Audit reporting, opinions, and management response
- TEVV (Test, Evaluation, Validation & Verification)

### 6. Data Governance & Quality
- Data provenance, lineage, and chain of custody
- Representativeness, coverage gaps, and historical bias in data
- Labeling quality, inter-annotator agreement, and label leakage
- Data minimization and purpose limitation
- Data drift and concept drift detection
- Datasheets for datasets; data documentation standards

### 7. Bias, Fairness & Non-Discrimination
- Group vs. individual fairness; intersectional bias
- Fairness metrics: demographic parity, equalized odds, equal opportunity, calibration
- The impossibility results (you can't satisfy all metrics at once)
- Disparate treatment vs. disparate impact (and the legal four-fifths rule)
- Pre-, in-, and post-processing mitigation techniques
- Proxy discrimination and redlining-by-algorithm
- Fair lending and adverse-action context (ECOA, SR 11-7)

### 8. Transparency & Explainability (XAI)
- Global vs. local explanations
- Feature attribution: SHAP, LIME, Integrated Gradients
- Surrogate models, counterfactual explanations, and saliency maps
- Inherently interpretable models vs. post-hoc explanation
- Explainability for regulators, users, and adverse-action notices
- Limits and pitfalls of explanation methods

### 9. Robustness, Safety & Reliability
- Distribution shift and out-of-distribution detection
- Stress testing, edge cases, and corner-case discovery
- Performance stability and degradation over time
- Uncertainty quantification and confidence calibration
- Safety cases and assurance arguments
- Failure mode and effects analysis (FMEA) for ML

### 10. Privacy & Data Protection
- PII identification and minimization
- Differential privacy and privacy budgets
- Membership inference, model inversion, and reconstruction attacks
- Federated learning and privacy-preserving ML
- GDPR Article 22 (automated decision-making), PIPEDA, and consent
- Data retention, deletion, and the right to explanation

### 11. AI Security & Adversarial ML
- Adversarial examples, evasion, and perturbation attacks
- Data poisoning and backdoor/trojan attacks
- Model extraction and model stealing
- MITRE ATLAS threat tactics for ML
- OWASP Top 10 for LLM Applications
- Securing the ML supply chain (models, weights, dependencies)
- AI red-teaming methodology

### 12. Generative AI & LLM Risks
- Hallucination and factual reliability
- Prompt injection (direct and indirect) and jailbreaks
- Toxicity, harmful content, and safety guardrails
- Data leakage and training-data memorization
- Copyright, IP, and provenance of outputs
- LLM evaluation: benchmarks, rubrics, and human eval
- Guardrail testing and attack-success-rate measurement

### 13. Agentic AI
- Autonomy levels and the expanding action space
- Tool use, function calling, and external side effects
- Multi-agent coordination and emergent behavior
- Sandboxing, permissions, and least-privilege design
- Oversight and interruptibility of long-running agents
- Auditing agent decision logs and traces

### 14. Third-Party / Supply-Chain Risk
- Auditing what you didn't build (APIs, foundation models, embedded AI)
- Vendor model cards, system cards, and transparency reports
- Contractual controls, SLAs, and right-to-audit clauses
- Shared-responsibility models for AI
- Evaluating provider bias/safety documentation
- Fourth-party and sub-processor risk

### 15. Human Oversight & Sociotechnical Factors
- Human-in-the-loop, on-the-loop, and out-of-the-loop designs
- Automation bias and over-reliance
- Meaningful human control and override mechanisms
- Stakeholder and affected-community engagement
- Sociotechnical context and real-world deployment effects
- Usability and contestability for end users

### 16. Documentation & Evidence
- Model cards (Mitchell et al.) and datasheets (Gebru et al.)
- System cards and transparency reports
- Intended-use and limitations statements
- Decision and audit logs; immutable trails
- Statement of Applicability (SoA) and control mappings
- Evidence set for certification (e.g., ISO/IEC 42001 Annex A)

### 17. Regulatory & Legal Landscape
- EU AI Act (risk tiers, obligations, timelines, GPAI rules)
- US state laws (Colorado AI Act, Texas TRAIGA, California SB 53, NYC Local Law 144)
- GDPR Article 22 and automated decision-making
- US federal guidance and the (rescinded/evolving) AI Bill of Rights Blueprint
- OECD AI Principles and UNESCO Recommendation
- Cross-framework crosswalks and multi-jurisdiction compliance

### 18. Sector-Specific Auditing
- **Finance:** model risk (SR 11-7 / SR 21-8), credit and fair-lending audits
- **Employment/HR:** automated hiring tools, NYC LL 144 bias audits
- **Healthcare:** clinical AI validation, safety, and equity
- **Insurance:** underwriting and pricing fairness
- **Public sector:** algorithmic accountability and procurement
- **Marketing/content:** recommendation and targeting harms

### 19. Continuous Monitoring & Post-Deployment
- Production performance and drift dashboards
- Ongoing fairness and bias monitoring
- Alerting, thresholds, and escalation
- Post-market surveillance (EU AI Act style)
- Periodic re-audit and conformity maintenance
- Champion/challenger and shadow deployments

### 20. Incident Response, Redress & Reporting
- AI incident definition, classification, and severity
- Root-cause analysis for model failures
- Affected-party notification and redress mechanisms
- Regulatory reporting obligations
- AI incident databases and shared learning
- Kill-switch and rollback procedures

---

## Mastering

A staged path from "I can spell ML" to "I lead independent third-party AI audits." Pair every stage with **hands-on work** — auditing is a craft, not a reading list.

### Stage 1 — Foundations (0–3 months)
**Goal:** speak the language and understand what you'll be auditing.
- Complete an intro ML course (Andrew Ng / fast.ai / Google ML Crash Course).
- Read *Fairness and Machine Learning* (free) and *Interpretable Machine Learning* (free).
- Learn the **NIST AI RMF** four functions cold; skim **ISO/IEC 42001** structure.
- Build comfort in Python + Jupyter.
- **Milestone:** train a simple classifier and write a one-page model card for it.

### Stage 2 — Core Audit Skills (3–6 months)
**Goal:** run the standard technical tests.
- Run a **bias/fairness audit** on a public dataset with AIF360, Fairlearn, and Aequitas.
- Generate **explainability** reports with SHAP and LIME and interpret them critically.
- Map an organization's AI controls to **ISO/IEC 42001 Annex A** and **NIST AI RMF**.
- Learn audit fundamentals (scoping, evidence, testing, reporting) — borrow from IT audit (CISA-style thinking).
- **Milestone:** produce a mock audit report on a real open-source model, with findings and risk ratings.

### Stage 3 — Specialization & Frameworks (6–12 months)
**Goal:** go deep on a domain and a regulatory regime.
- Pick a depth area: **LLM red-teaming**, **fair lending**, **healthcare AI**, or **EU AI Act conformity**.
- Conduct an **AI impact assessment / FRIA** end-to-end.
- Red-team an LLM with **Garak** and **PyRIT**; measure attack success rate against guardrails.
- Study **MITRE ATLAS** and the **OWASP Top 10 for LLMs**.
- Pursue a credential: **AIGP** (governance), **AAIA** (audit), **ISO/IEC 42001 Lead Auditor**, or **BABL AI / ForHumanity (FHCA)**.
- **Milestone:** complete a full conformity-style assessment against one regulation.

### Stage 4 — Independent Practice & Leadership (12+ months)
**Goal:** lead engagements and shape practice.
- Define a **Target of Evaluation**, audit criteria, and assurance argument from scratch.
- Run continuous-monitoring and post-deployment audits.
- Contribute to standards bodies or auditor communities (IAAA, ForHumanity, ISO/IEC SC 42).
- Mentor, publish, and build repeatable audit playbooks.
- **Milestone:** deliver (or shadow) a real independent third-party AI audit.

### An audit engagement, end-to-end
A practical loop you'll repeat (adapted from internal-audit frameworks like Raji et al.'s *Closing the AI Accountability Gap*):

1. **Scoping** — define intended use, criteria, the Target of Evaluation, and what "good" looks like.
2. **Mapping** — inventory the system, data, stakeholders, dependencies, and risk surface.
3. **Artifact collection** — gather model cards, datasheets, logs, design docs, and the evidence set.
4. **Testing** — run fairness, robustness, explainability, privacy, and security tests; check controls.
5. **Reflection & reporting** — rate findings by risk/materiality, write the report, agree remediation.
6. **Monitoring** — verify fixes and watch the system in production; schedule re-audit.

### Self-assessment checklist
- [ ] I can read a confusion matrix and explain why "accuracy" can hide discrimination.
- [ ] I can compute and interpret demographic parity and equalized odds.
- [ ] I can run AIF360/Fairlearn and SHAP and critique their output.
- [ ] I can map controls across ISO/IEC 42001 and NIST AI RMF.
- [ ] I can classify a system under the EU AI Act risk tiers.
- [ ] I can red-team an LLM and report attack success rate.
- [ ] I can write a model card and an AI impact assessment.
- [ ] I can scope and report an end-to-end audit engagement.

---

## Tools

Open-source first, with notable commercial platforms flagged. No single tool covers everything — auditors combine fairness libraries, explainability and robustness tools, and monitoring platforms across the lifecycle.

### Bias & Fairness
| Tool | What it does | Link |
|------|--------------|------|
| **AI Fairness 360 (AIF360)** | IBM toolkit; 70+ fairness metrics and 10+ mitigation algorithms | https://github.com/Trusted-AI/AIF360 |
| **Fairlearn** | Microsoft; group-fairness metrics and mitigation, Python-native | https://github.com/fairlearn/fairlearn |
| **Aequitas** | Bias and fairness audit toolkit (CLI, library, web) from UChicago | https://github.com/dssg/aequitas |
| **What-If Tool** | Google/PAIR; no-code model probing for fairness and performance | https://github.com/PAIR-code/what-if-tool |
| **Fairlens** | Bias detection and fairness analysis for tabular data | https://github.com/synthesized-io/fairlens |

### Explainability (XAI)
| Tool | What it does | Link |
|------|--------------|------|
| **SHAP** | Shapley-value feature attribution; the XAI workhorse | https://github.com/shap/shap |
| **LIME** | Local interpretable model-agnostic explanations | https://github.com/marcotcr/lime |
| **InterpretML** | Microsoft; glassbox models + blackbox explanations | https://github.com/interpretml/interpret |
| **Captum** | PyTorch model interpretability (Integrated Gradients, etc.) | https://github.com/pytorch/captum |
| **Alibi** | Explanations + drift/outlier detection for ML | https://github.com/SeldonIO/alibi |
| **Responsible AI Toolbox** | Microsoft RAI dashboard (fairness + interpretability + error analysis) | https://github.com/microsoft/responsible-ai-toolbox |

### Robustness, Security & Adversarial ML
| Tool | What it does | Link |
|------|--------------|------|
| **Adversarial Robustness Toolbox (ART)** | Attacks + defenses across frameworks; broad threat coverage | https://github.com/Trusted-AI/adversarial-robustness-toolbox |
| **Foolbox** | Fast adversarial-attack library for testing robustness | https://github.com/bethgelab/foolbox |
| **CleverHans** | Classic adversarial-examples benchmarking library | https://github.com/cleverhans-lab/cleverhans |
| **TextAttack** | Adversarial attacks and data augmentation for NLP | https://github.com/QData/TextAttack |
| **Counterfit** | Microsoft; automation layer for assessing ML security | https://github.com/Azure/counterfit |

### LLM Evaluation, Red-Teaming & Guardrails
| Tool | What it does | Link |
|------|--------------|------|
| **Garak** | LLM vulnerability scanner / red-teaming probes (NVIDIA) | https://github.com/NVIDIA/garak |
| **PyRIT** | Python Risk Identification Toolkit for generative AI (Microsoft) | https://github.com/Azure/PyRIT |
| **Giskard** | Testing for ML and LLMs; scans for bias, robustness, and safety | https://github.com/Giskard-AI/giskard |
| **DeepEval** | Unit-testing-style evaluation framework for LLMs | https://github.com/confident-ai/deepeval |
| **promptfoo** | Prompt/LLM eval and red-teaming with assertions | https://github.com/promptfoo/promptfoo |
| **lm-evaluation-harness** | EleutherAI; standardized LLM benchmarking | https://github.com/EleutherAI/lm-evaluation-harness |
| **Inspect** | UK AI Safety Institute's LLM evaluation framework | https://github.com/UKGovernmentBEIS/inspect_ai |
| **HELM** | Stanford CRFM; holistic evaluation of language models | https://github.com/stanford-crfm/helm |

### Data Quality, Testing & Validation
| Tool | What it does | Link |
|------|--------------|------|
| **Deepchecks** | Validation and testing suite for data and models | https://github.com/deepchecks/deepchecks |
| **Great Expectations** | Data quality, validation, and documentation | https://github.com/great-expectations/great_expectations |
| **Evidently** | Data/model drift, quality, and performance monitoring | https://github.com/evidentlyai/evidently |

### Privacy
| Tool | What it does | Link |
|------|--------------|------|
| **Opacus** | Differential privacy for PyTorch training | https://github.com/pytorch/opacus |
| **TensorFlow Privacy** | DP optimizers and privacy accounting for TF | https://github.com/tensorflow/privacy |
| **Diffprivlib** | IBM differential-privacy library | https://github.com/IBM/differential-privacy-library |
| **Presidio** | Microsoft; PII detection and de-identification | https://github.com/microsoft/presidio |

### Documentation & Monitoring
| Tool | What it does | Link |
|------|--------------|------|
| **whylogs** | Data logging and monitoring for ML pipelines | https://github.com/whylabs/whylogs |
| **Arize Phoenix** | Open-source observability for LLMs and ML | https://github.com/Arize-ai/phoenix |
| **AI Verify** | Singapore IMDA testing framework + toolkit for trustworthy AI | https://github.com/aiverify-foundation/aiverify |

### Commercial / Enterprise Governance Platforms
Worth knowing even if you can't run them for free: **Credo AI**, **Holistic AI**, **IBM watsonx.governance** (formerly Watson OpenScale), **Fiddler AI**, **Arthur**, **Monitaur**, **Saidot**, **Enzai**, **ModelOp**, **OneTrust AI Governance**, and **TrustArc**. These generate governance artifacts, control mappings, and continuous-monitoring evidence at enterprise scale.

---

## Certifications

The AI-audit and governance credential landscape matured a lot in 2024–2025. Pick based on your starting point: governance vs. hands-on audit vs. security.

| Certification | Body | Focus | Prerequisites | Notes |
|---------------|------|-------|---------------|-------|
| **AIGP** — AI Governance Professional | IAPP | End-to-end AI governance | None | The first widely recognized AI-governance credential; Body of Knowledge updated regularly (v2.1, 2026); 4 domains spanning foundations, law/standards, development, and deployment |
| **AAIA** — Advanced in AI Audit | ISACA | AI-focused auditing | CISA / CIA / CPA / ACCA / equivalent | First advanced audit-specific AI credential (launched 2025); 90-question exam; domains: AI governance & risk, AI operations, AI auditing tools & techniques |
| **AAISM** — Advanced in AI Security Management | ISACA | Securing/managing AI | CISM / CISSP | For security leaders adding AI-specific scope |
| **ISO/IEC 42001 Lead Implementer / Lead Auditor** | PECB and others | AIMS implementation & auditing | Varies by provider | Maps to the certifiable AI management system; auditors of certification bodies must meet **ISO/IEC 42006:2025** |
| **AI & Algorithm Auditor Certification** | BABL AI | Hands-on algorithm auditing | None (open enrollment) | 5-course program with exam + exit interview; led by Shea Brown; strong on practical TEVV and assurance |
| **AI Governance for Business Professionals** | BABL AI | Governance for non-technical roles | None | Foundations-level governance credential |
| **ForHumanity Certified Auditor (FHCA)** | ForHumanity | Independent third-party AI audit | Course-based | "Gold standard" for AAA-systems auditing; schemes for CORE governance, EU AI Act, cybersecurity, and more |
| **IEEE CertifAIEd** | IEEE | AI ethics assessment/certification | Training-based | Assessor program tied to IEEE's ethics ontologies (transparency, accountability, privacy, bias) |
| **CAIP / CEET** | CertNexus | AI practitioner / ethical tech | None | Vendor-neutral practitioner and ethics credentials |

> [!TIP]
> Common pairing for an auditor in 2026: **AIGP** (to speak governance) + **AAIA** *or* **ISO/IEC 42001 Lead Auditor** (to do the audit) + a hands-on track like **BABL AI** or **ForHumanity FHCA** for technical TEVV depth.

---

## Standards

The frameworks and laws you'll audit against. International standards establish *how* to govern and audit; regulations establish *what's required*.

### International Standards & Frameworks
| Standard | Issuer | Scope |
|----------|--------|-------|
| **NIST AI RMF 1.0** (AI 100-1) | NIST (US) | Voluntary risk framework: Govern, Map, Measure, Manage |
| **NIST AI RMF Playbook** | NIST | Companion actions for the RMF's subcategories |
| **NIST GenAI Profile** (AI 600-1) | NIST | 12 GenAI risks and 400+ suggested actions |
| **ISO/IEC 42001:2023** | ISO/IEC | AI Management System (AIMS) — certifiable; 38 Annex A controls |
| **ISO/IEC 42006:2025** | ISO/IEC | Requirements for bodies auditing & certifying AIMS (the auditor-qualification standard) |
| **ISO/IEC 23894:2023** | ISO/IEC | AI risk management guidance |
| **ISO/IEC 42005** | ISO/IEC | AI system impact assessment |
| **ISO/IEC TR 24027** | ISO/IEC | Bias in AI systems and AI-aided decision-making |
| **ISO/IEC TR 24028** | ISO/IEC | Overview of trustworthiness in AI |
| **ISO/IEC 23053** | ISO/IEC | Framework for AI systems using ML |
| **ISO/IEC 25059** | ISO/IEC | Quality model for AI systems |
| **OECD AI Principles** | OECD | Values-based principles for trustworthy AI |
| **UNESCO Recommendation on the Ethics of AI** | UNESCO | Global ethics instrument |
| **IEEE 7000-series** | IEEE | Ethics-by-design: 7000, 7001 (transparency), 7002 (privacy), 7003 (algorithmic bias) |

### Regulations & Laws
| Regulation | Jurisdiction | Relevance |
|------------|--------------|-----------|
| **EU AI Act** (Reg. 2024/1689) | EU | Risk-tiered obligations; phased enforcement; GPAI rules |
| **GDPR Article 22** | EU | Rights around automated decision-making |
| **Colorado AI Act** (SB 205) | US — Colorado | Duties for high-risk AI developers/deployers |
| **Texas TRAIGA** | US — Texas | Responsible AI governance act |
| **California SB 53 / SB 1001** | US — California | Frontier-AI transparency / bot-disclosure |
| **NYC Local Law 144** | US — NYC | Mandatory bias audits for automated employment tools |

### Sector & Model-Risk Frameworks
- **SR 11-7 / SR 21-8** — US Federal Reserve / OCC model risk management guidance (foundational for financial-model and AI audits).
- **FS AI RMF** — sector-specific GenAI risk framework for financial services.
- **Singapore Model AI Governance Framework** — plus the **AI Verify** testing toolkit.
- **Equal Credit Opportunity Act (ECOA) / Reg B** — fair-lending and adverse-action context for credit models.

---

## References

### Foundational papers
- **Model Cards for Model Reporting** — Mitchell et al. (2019) — the standard for model documentation.
- **Datasheets for Datasets** — Gebru et al. (2018) — documenting dataset provenance and limitations.
- **Closing the AI Accountability Gap: Defining an End-to-End Framework for Internal Algorithmic Auditing** — Raji et al. (2020) — the canonical internal-audit framework.
- **A Unified Approach to Interpreting Model Predictions (SHAP)** — Lundberg & Lee (2017).
- **"Why Should I Trust You?" (LIME)** — Ribeiro et al. (2016).
- **Equality of Opportunity in Supervised Learning** — Hardt, Price & Srebro (2016).
- **Aequitas: A Bias and Fairness Audit Toolkit** — Saleiro et al. (2018).

### Key frameworks & primary documents
- **NIST AI 100-1** (AI RMF 1.0) and the **AI RMF Playbook** — nist.gov / airc.nist.gov
- **NIST AI 600-1** — Generative AI Profile.
- **EU AI Act** full text — https://artificialintelligenceact.eu
- **OECD AI Principles** — https://oecd.ai
- **ISO/IEC 42001** overview — https://www.iso.org/standard/81230.html
- **NIST → ISO/IEC 42001 crosswalk** — https://airc.nist.gov

### Books (several free online)
- **Fairness and Machine Learning** — Barocas, Hardt & Narayanan — free at https://fairmlbook.org
- **Interpretable Machine Learning** — Christoph Molnar — free at https://christophm.github.io/interpretable-ml-book/
- **Trustworthy Machine Learning** — Kush R. Varshney — free at http://www.trustworthymachinelearning.com
- **Weapons of Math Destruction** — Cathy O'Neil.
- **The Alignment Problem** — Brian Christian.
- **The Ethical Algorithm** — Kearns & Roth.
- **Atlas of AI** — Kate Crawford.
- **Race After Technology** — Ruha Benjamin.

### Curated hubs & communities
- **NIST Trustworthy & Responsible AI Resource Center** — https://airc.nist.gov
- **OECD.AI Policy Observatory** — https://oecd.ai
- **Partnership on AI** — https://partnershiponai.org
- **AI Incident Database** — https://incidentdatabase.ai
- **International Association of Algorithmic Auditors (IAAA)** — community of practice for the profession.
- **ForHumanity** — https://forhumanity.center

---

## YouTube Videos

Channels and series worth subscribing to (search the channel name on YouTube; these are durable, high-signal sources rather than transient hype channels).

- **Stanford Online / Stanford HAI** — lectures on responsible AI, governance, and ML foundations.
- **MIT** (MIT OpenCourseWare / CSAIL) — rigorous ML and AI-ethics lectures.
- **University of Oxford — Institute for Ethics in AI** — talks bridging philosophy and technical AI.
- **IBM Technology** — accessible explainers on AI governance, responsible AI, and bias detection.
- **Anthropic** — discussions on AI safety, alignment, and responsible development.
- **ACM FAccT** (Fairness, Accountability & Transparency conference) — recorded talks at the research frontier of algorithmic auditing.
- **The Alan Turing Institute** — public lectures on data ethics and AI assurance.
- **BABL AI** — practical talks on algorithm auditing and TEVV.
- **CXOTalk** — executive-level conversations on trustworthy and ethical AI leadership.
- **Class Central's "AI Ethics" playlists** — aggregated free university lectures (UNESCO/OECD principles, governance) — https://www.classcentral.com

> [!TIP]
> For structured learning, prioritize conference recordings (FAccT, NeurIPS workshops on fairness/safety) and university lectures over news-cycle commentary — they age far better.

---

## Blogs

- **NIST AI** (airc.nist.gov) — authoritative framework updates and crosswalks.
- **IAPP Resource Center** (iapp.org) — governance, regulation, and AIGP-aligned content.
- **BABL AI Blog** (babl.ai/blog) — hands-on algorithm auditing and assurance.
- **Holistic AI Blog** (holisticai.com/blog) — auditing methods, regulation, and tooling.
- **Credo AI Blog** (credo.ai/blog) — operationalizing AI governance.
- **Montreal AI Ethics Institute** (montrealethics.ai) — the *State of AI Ethics* reports and digests.
- **Ada Lovelace Institute** (adalovelaceinstitute.org) — research on AI assurance and accountability.
- **AI Now Institute** (ainowinstitute.org) — critical, policy-oriented analysis.
- **AlgorithmWatch** (algorithmwatch.org) — investigative algorithmic-accountability work.
- **Brookings — AI & Emerging Tech** (brookings.edu) — policy and governance analysis.
- **The Gradient** (thegradient.pub) — thoughtful ML/AI essays.
- **Google PAIR** (pair.withgoogle.com) — human-centered ML and explainability.
- **Distill** (distill.pub) — archived but gold for visual ML explanations.
- **Stanford HAI** (hai.stanford.edu) — the annual **AI Index** and policy research.
- **Trail of Bits** (blog.trailofbits.com) — ML/AI security from an offensive-security lens.

---

## Podcasts

- **The Road to Accountable AI** — Kevin Werbach (Wharton); deep interviews with auditing, governance, and policy leaders.
- **Trustworthy AI** — Pamela Gupta; de-risking AI adoption for enterprises and boards.
- **In AI We Trust** — Miriam Vogel & the World Economic Forum; responsible AI and policy.
- **The AI Governance Podcast** — Enzai; practical conversations with global governance leaders (EU AI Act enforcement, procurement, accountability).
- **Good Decisions** — ModelOp; enterprise AI governance, frameworks, and regulation.
- **The TWIML AI Podcast** (This Week in Machine Learning & AI) — Sam Charrington; technical depth across ML, including fairness and safety episodes.
- **Last Week in AI** — weekly news roundup to stay current on the landscape.
- **Your Undivided Attention** — Center for Humane Technology; sociotechnical risk and harms.
- **The Gradient Podcast** — long-form interviews with ML researchers.
- **PwC Responsible AI** — practitioner perspectives on building trust and shaping policy.

---

## Contributing

Found a dead link, a renamed certification, a new standard, or a tool worth adding? Open an issue or a pull request. Keep entries vendor-neutral where possible, prefer primary/official sources, and add a one-line description so readers know *why* it's here.

## License

Consider adding a license file (e.g., **CC0** or **CC BY 4.0** for a curated list, or **MIT** if you include code) so others know how they can reuse this roadmap.

# Evaluation and Governance Pipeline (NIST AI RMF Measure/Manage Aligned)

## Purpose
Define a repeatable evaluation and governance structure for offline-first model validation that aligns to the **NIST AI RMF Measure** and **Manage** functions.

## 1) Evaluation Stages (Input -> Model -> Output -> Scoring)

| Stage | Objective | Core Activities | Primary Artifacts |
|---|---|---|---|
| Input | Validate request and data quality before inference | Input policy checks, schema validation, classification/label checks, prompt-safety prefilters | `prompt_run.json`, `telemetry.jsonl` |
| Model | Execute model path under governed runtime | Adapter invocation, model/version capture, retrieval trace capture, guardrail invocation | `prompt_run.json`, `lineage_report.md` |
| Output | Evaluate response safety, quality, and traceability | Citation checks, refusal/unsafe-output checks, red-team case execution, reasoning report generation | `reasoning_report.md`, `redteam_findings.json`, `redteam_summary.json` |
| Scoring | Aggregate metrics and make governance decision | Metric threshold evaluation, stage-gate synthesis, risk-tier gate check, go/no-go derivation | `eval_results.json`, `scorecard.md`, `scorecard.html`, `scorecard.json` |

## 2) Proposed Control Layer Structure

| Control Layer | Scope | Example Controls | Owner |
|---|---|---|---|
| Policy and Risk | Governance policy and risk acceptance | Intended-use constraints, risk-tier mapping, severity thresholds, human sign-off requirement | AI Governance Lead |
| Data and Input | Input quality and prompt safety | Schema validation, PII handling, allowed-source constraints, prompt-injection pre-checks | Data Steward / App Team |
| Model and Runtime | Model invocation and runtime integrity | Model/version pinning, adapter contract checks, deterministic fallback behavior, trace capture | ML Platform / Engineering |
| Output and Assurance | Post-generation quality and safety assurance | Evaluation suite execution, fairness checks, red-team validation, reasoning/lineage artifacts | Evaluation + Red Team |
| Operational Management | Continuous oversight and response | Monitoring summaries, incident workflow triggers, remediation tracking, approval logs | Operations / Risk Committee |

## 3) Metrics Definition (Minimum Required Set)

| Metric Family | Metric | Definition | Example Measure | Suggested Gate |
|---|---|---|---|---|
| Accuracy | Task accuracy | Correctness against expected outcomes on benchmark cases | `% correct` across evaluation suite | >= 0.75 (tier-adjustable) |
| Robustness | Adversarial robustness | Resistance to malicious, ambiguous, or out-of-distribution prompts | Attack success rate; unsafe response rate | `high+critical` findings = 0 for release |
| Fairness | Group fairness indicators | Differential performance/outcomes across groups | SPD, DIR, EOD, AOD snapshots | `abs(SPD/EOD/AOD) <= threshold`; `DIR >= 0.8` |
| Reliability | Consistency and stability | Repeatability and dependable behavior over repeated runs | Output consistency score, groundedness/retrieval alignment | >= 0.80 reliability, >= 0.65 groundedness |

### Supporting Metric Signals
- Refusal correctness (safe refusal when required).
- Unanswerable handling (graceful handling when evidence is insufficient).
- Evidence completeness (% required artifacts produced by risk tier).

## 4) NIST AI RMF Mapping (Measure + Manage)

### Measure Function Alignment

| Pipeline Element | Measure Alignment |
|---|---|
| Input-stage validation and test case coverage | Structured measurement design for risk-relevant conditions and failure modes |
| Accuracy, fairness, robustness, reliability metrics | Quantitative and qualitative measurement of system performance and risk |
| Red-team execution and severity scoring | Measurement of security/safety exposure under realistic adversarial stress |
| Reasoning report + lineage artifacts | Measurement evidence for explainability and traceability claims |
| Scorecard thresholding and stage-gate outcomes | Documented measurement outcomes supporting risk posture assessment |

### Manage Function Alignment

| Pipeline Element | Manage Alignment |
|---|---|
| Stage gates (evaluation/red-team/documentation/monitoring/human sign-off) | Risk treatment decisions and release control actions |
| Go/No-Go decision logic | Operational risk response and acceptance/escalation path |
| Required actions list in scorecard | Actionable remediation tracking and accountability |
| Monitoring summary and incident generation | Ongoing risk management and incident response lifecycle |
| Artifact manifest and governance records | Auditability, communication, and repeatable governance operations |

## 5) Operating Workflow

1. Run input + model + output workflows using approved configuration and risk tier.
2. Execute evaluation and red-team suites.
3. Generate reasoning, scorecard, and documentation artifacts.
4. Apply stage-gate logic and produce go/no-go recommendation.
5. Route failures/needs-review items to remediation and re-run before release.

## 6) Definition of Done Check

- Pipeline documented: **Complete** (this document).
- Metrics clearly defined: **Complete** (accuracy, robustness, fairness, reliability + gates).
- Committed to repo: **Complete** (tracked in git history).

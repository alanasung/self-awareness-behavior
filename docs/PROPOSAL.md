# Behavioral Tests for Self-Knowledge Claims

## Question

Elicit self-reports about task-specific limits, measure actual performance on the same tasks, and score calibration between the two. Self-report is only meaningful against measured behaviour, so the behavioural arm is collected first and the report is scored against it.

## Why it is worth measuring

The question is answerable at small scale with a locally runnable pilot, and it has a
clean falsification condition: the measurement is built so a negative result with adequate
power is reportable rather than a dead end. Most of the design effort goes into the
controls, because the easy version of this measurement would produce a number that looks
like an answer and is really an artifact of how the stimuli were built.

## Objectives

1. Elicit self-reports that are specific enough to be checked against behaviour.
2. Measure actual capability on exactly the tasks the reports are about.
3. Score calibration with a proper scoring rule rather than agreement counting.
4. Test whether reports track behaviour or track prompt framing, using a framing manipulation.

## Method

The repository implements a five-stage pipeline. Stimuli are constructed locally so their
ground truth is known rather than assumed. Model-side collection is measured against a
revision-pinned small open-weight model and fails closed when weights are absent. The core
measurement runs with its controls in the same pass, so a result and the arm that would
undermine it are produced together rather than in separate sessions.

Domain code lives in `src/selfaware/selfaware/`. The shared infrastructure — typed Hydra
configuration, versioned artifact cache, hooks and generation, metrics, ablation,
reporting and CI — is separate from it, so the science is reviewable without reading the
plumbing.

## Plan

| ID | Workstream | Size | Description |
|---|---|---|---|
| WS-01 | Task set with measurable variation | M | Tasks whose difficulty varies enough that accurate self-prediction is distinguishable from a constant answer. |
| WS-02 | Independent behavioural measurement | M | Measure actual per-task performance first, with no self-report in context. |
| WS-03 | Checkable self-report elicitation | M | Elicit per-task predictions in a schema that can be scored, with the parser validated against hand labels. |
| WS-04 | Calibration scoring | L | Score reports against behaviour with a proper scoring rule, against a constant-prediction baseline. Carries the headline claim. |
| WS-05 | Framing manipulation and identity consistency | M | Vary framing to test whether reports track behaviour or wording, and measure self-reference consistency across contexts. |
| WS-06 | Documentation, presets and figures | M | Calibration figure against the constant baseline, domain presets, and documentation to the standard's floor. |

## Confounds

| Risk | Control |
|---|---|
| A model predicting the base rate scores as well calibrated | The constant-prediction baseline is a required comparison; calibration claims nothing unless it beats it |
| Self-reports are too vague to check, so scoring is subjective | Reports must fit a machine-checkable schema; unparseable reports are counted as failures rather than discarded |
| Framing effects are mistaken for self-knowledge | The framing manipulation is a required arm and both framings are reported |
| Synthetic smoke output is mistaken for a measured result | `is_synthetic` is set at production and survives aggregation; `claim_ok` is false whenever any input was synthetic |
| Pilot n too small to separate a true null from an underpowered test | Report minimum detectable effect beside every interval and run an equivalence test (TOST) before claiming a null (X12) |
| Small open-weight models do not exhibit the phenomenon at all | State a falsification threshold before running; a clean negative with adequate power is a reportable result |

## What would make this credible

- Behavioural performance is measured with no self-report in context.
- Self-reports fit a machine-checkable schema; unparseable reports are counted, not dropped.
- Calibration is scored with a proper scoring rule against a constant-prediction baseline.
- The framing manipulation is run and both framings reported.
- Task difficulty varies enough for self-prediction to be distinguishable from a constant answer.

## Honesty commitments

Synthetic output is labelled where it is produced and the label survives into the report.
A claim whose gate fails is suppressed and its block reason named, rather than restated
with hedging. No number in this repository is presented as measured unless it came from a
run against real weights, and the run directory carries the seed, the commit and the model
revision that produced it.

## Compute

The pilot runs on an Apple M4 with no CUDA and no API keys. Model forward passes use MPS
where available; the statistics run on CPU and are documented as such.

## Current status

Infrastructure and the domain measurement are implemented and unit tested. No measured
result is reported yet. The design document at [`TECHNICAL.md`](TECHNICAL.md) states the
artifact contract and the open technical decisions; the program plan under
[`programs/`](programs/) carries the workstream detail and acceptance criteria.

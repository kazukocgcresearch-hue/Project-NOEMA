# Project NOEMA — Stage 1 Complete 2026-09-02

## Qwen3-14B Reasoning Training Reaches 29/29 Final Decisions

**Date:** 2026-09-02

Project NOEMA is an experimental research project exploring whether the reasoning behavior of a language model can be stabilized by training the model on semantic relationships rather than simply increasing dataset size or teaching answer patterns.

In the latest experiment, a Qwen3-14B base model was trained with a small, curated NOEMA dataset using LoRA.

The current best adapter achieved:

* 29 / 29 correct final judgments
* 0 final-decision failures
* Some remaining local imperfections in explanation wording

The evaluation set contains several classes of reasoning problems, including:

* state transitions over time
* incomplete evidence
* complete evidence
* conflicting evidence
* identity / actor binding
* preservation of uncertainty
* distinction between relevant and irrelevant actions
* persistence of a state after an update

## What changed

An earlier version of the model had nearly reached full accuracy, but two reasoning boundaries remained unstable.

One involved positive identity closure: the model could correctly recognize all of the relevant facts but sometimes failed to conclude that an official event executed through an exclusive identifier belonged to the person bound to that identifier.

A minimal additional training example strengthened this behavior.

However, that change exposed another weakness in temporal reasoning: the model recognized a state-transition event correctly, but sometimes failed to connect the updated state with a later “no further changes” interval.

Instead of adding more examples, I inspected the existing training set and found that both temporal patterns were already represented.

Two existing teacher responses were therefore rewritten to express the temporal relationship more clearly:

**state-changing event → updated state → no later change → updated state persists at target time**

No new examples were added at this stage.

The dataset size remained unchanged.

After retraining from the fresh Qwen3-14B base model and evaluating the saved adapter after a fresh reload, all 29 evaluation cases reached the correct final judgment.

## Why this is interesting

The result suggests that the number of examples is not necessarily the dominant variable.

In this experiment, a reasoning failure was corrected not by adding more data, but by clarifying how an existing teacher example represented the relationship between two semantic states.

The model already appeared capable of recognizing the individual elements:

* a suspension event
* a later restoration event
* a target time
* a period with no subsequent changes

The failure occurred in how those pieces were connected.

This supports a working hypothesis behind Project NOEMA:

> Reasoning training may depend strongly on teaching the model how semantic elements intersect and constrain one another, rather than only teaching more examples or more explicit answer labels.

## Evaluation protocol

One important methodological finding also emerged.

The behavior of the model immediately after training was not always identical to the behavior of the saved adapter after reloading it onto a fresh base model.

For this reason, the current NOEMA evaluation protocol is:

1. Train from a fresh base model.
2. Save the adapter.
3. Destroy the training runtime.
4. Reload a fresh base model.
5. Load the frozen adapter.
6. Evaluate with deterministic decoding.

The reloaded adapter is treated as the actual experimental artifact.

## Current status

The current Qwen3-14B NOEMA adapter is the strongest result so far.

All 29 evaluation cases now reach the correct final decision.

Some explanation-level wording still requires refinement, so this is not being treated as a claim that the reasoning system is “perfect.”

The important result is narrower:

**The tested reasoning boundaries can currently coexist in one trained model without producing a final-decision error across this evaluation set.**

Detailed datasets, prompts, training examples, and internal evaluation structures are intentionally not being released at this stage.

Project NOEMA remains an active independent research project.

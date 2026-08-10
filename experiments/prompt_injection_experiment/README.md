# Prompt-Injection Experiment

This folder contains the prompt-injection / prompt-formatting follow-up
experiment for the Qwen2.5-14B hosted model tamper-detection project.

The experiment fixes the underlying checkpoint and loading mode, then changes
only the prompt-formatting variant. This tests whether the estimator can flag an
endpoint as non-identical when the provider silently injects or modifies the
actual served prompt formatting before the user context.

## Files

- `qwen2_5_colab_prompt_selection_prompt_injection_experiment.ipynb`
  generates the logit payloads. One successful run creates 18 payloads total:
  3 user-defined prompt-formatting variants times 6 prompts.
- `qwen2_5_theoretical_limit_heatmaps_prompt_injection_experiment.ipynb`
  computes theoretical Naive Method I and Naive Method II limits and plots
  heatmaps.
- `context_token_tracker.md` records the prompt inventory, canary mapping, and
  final prompt-formatting variants.
- `experiment_plan.md` records the intended protocol.
- `logit_payloads/` is where the `.pt` payloads should live after generation.

## Prompt-Formatting Variants

The prompt-selection notebook is designed so the prompt-formatting variants can
be edited directly before generation. The final recorded experiment uses exactly
three variants for the downstream `3 x 3` heatmap:

- `PI Variant 1`: baseline Qwen chat formatting with Qwen's automatic default
  system prompt.
- `PI Variant 2`: explicit system prompt `You are a helpful assistant`.
- `PI Variant 3`: explicit system prompt `Amadeus`.

Each variant has:

- a stable key used in filenames
- a short label used for plots
- a `system_prompt`, where `None` means no explicit extra system message

## Expected Result

The diagonal compares each prompt-formatting variant against itself and should
be near zero whenever the estimator is defined. Off-diagonal entries compare
different prompt-formatting variants and should move away from zero if the
prompt injection changes the relevant next-token geometry.

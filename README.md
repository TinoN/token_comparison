# Claude Sonnet 4.6 — Token Usage by Effort & Thinking State

**Model:** `claude-sonnet-4-6`  
**Date:** 2026-06-16  
**Test prompt:** *"Explain the key differences between deductive and inductive reasoning, give one concrete example of each, and identify a common logical fallacy that can arise in each type of reasoning."*

---

## Results

| State | Input | Output | Think | Total | Stop |
|---|---:|---:|---:|---:|---|
| Low / Thinking OFF | 45 | 503 | 0 | 548 | end_turn |
| Low / Thinking ON | 45 | 452 | 8 | 505 | end_turn |
| Med / Thinking OFF | 45 | 664 | 0 | 709 | end_turn |
| Med / Thinking ON | 45 | 561 | 27 | 633 | end_turn |
| High / Thinking OFF | 45 | 870 | 0 | 915 | end_turn |
| High / Thinking ON | 45 | 773 | 34 | 852 | end_turn |
| Max / Thinking OFF | 45 | 669 | 0 | 714 | end_turn |
| Max / Thinking ON | 45 | 1003 | 357 | 1405 | end_turn |

*All token counts from the Anthropic API usage object. Think = 0 means the model skipped internal reasoning at that effort level.*

---

## Relative Token Cost

Baseline: **Low / Thinking OFF** (1.00×)

| State | Multiplier |
|---|---:|
| Low / Thinking OFF | 1.00× |
| Low / Thinking ON | 0.92× |
| Med / Thinking OFF | 1.29× |
| Med / Thinking ON | 1.16× |
| High / Thinking OFF | 1.67× |
| High / Thinking ON | 1.55× |
| Max / Thinking OFF | 1.30× |
| Max / Thinking ON | **2.56×** |

---

## Observations

- **Thinking ON does not always cost more.** At Low and Medium effort, enabling Thinking slightly *reduces* total tokens — the model produces a more concise output after reasoning, more than offsetting the thinking tokens consumed.
- **Max / Thinking ON is the only state with a large jump** (2.56×), driven by 357 thinking tokens — roughly 10× more than any other Thinking ON state. This is where the reasoning budget is genuinely being used.
- **Max / Thinking OFF is cheaper than High / Thinking OFF** (1.30× vs 1.67×), which is counterintuitive. Effort controls scope and thoroughness, not just length — higher effort doesn't linearly increase output tokens.
- **The Think column is near-zero at Low, Med, and High** (8, 27, 34 tokens). Meaningful extended reasoning only activates at Max effort with Thinking ON.
- **Practical takeaway for this prompt type:** High / Thinking ON at 1.55× offers the best reasoning depth per token spent. Max / Thinking ON at 2.56× is the only state that actually engages the full reasoning pipeline, but costs 65% more than High / Thinking ON for this prompt.

---

## Method

Tested using the [Anthropic Python SDK](https://github.com/anthropic/anthropic-sdk-python) via direct API calls with `output_config: {effort: ...}` and `thinking: {type: "adaptive"}` where applicable. Each state was a fresh, independent API call with no conversation history. A 3-second delay was applied between calls to avoid rate limiting.

Script: [`token_comparison.py`](./token_comparison.py)

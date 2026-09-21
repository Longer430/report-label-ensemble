# Kaggle predecessor reconstruction, checked 2026-09-20

Use only when discussing or reproducing the motivating Kaggle lineage. This is
NOT the complete v5 algorithm and does not replace the skill's default policy.

## Primary sources

- [Two-source dataset](https://www.kaggle.com/datasets/yunusgmsoy/rsna-knee-llm-labels-2-source-merged)
- [Three-source dataset](https://www.kaggle.com/datasets/yunusgmsoy/rsna-knee-abnormality-3-source-merged-labels)
- [Gemini source](https://www.kaggle.com/datasets/yunusgmsoy/rsna-knee-llm-report-labels)
- [Pilkwang source](https://www.kaggle.com/datasets/pilkwang/rsna-knee-llm-labels)
- [Steven source](https://www.kaggle.com/datasets/stevenleehans/rsna-knee-llm-report-labels)

The three-source description identifies Multilingual as a rule-based lexicon
parser with anatomical pairing and OA inheritance. It is not a confirmed fourth
LLM. The predecessor descriptions state that official 58-study labels are
preserved at confidence 1 and that low-confidence synovitis uses effusion backoff.
These statements alone do not establish the current v5 implementation.

## Numerically reconstructed two-source policy

P = Pilkwang report_labels_v2.csv score.
S = Steven llm_labels_v2.csv score (not full or v4_blend).

```
p = (P + S) / 2
c = 1 - abs(P - S)
if target == Synovitis and c < 0.80:
    p = min(p, 0.80 * merged_Effusion)
```

The hypothesis matched all 51,433 audited cells in the author's two-source
report_labels_v2.csv, both scores and confidence, within 2e-16 absolute error.
Scope: common IDs only, excluding merged confidence=1 cells. Pilkwang has 4406
rows, others 4407. Missing-source fallback and official overrides are not
reconstructed. Synovitis had 4342 audited cells; the cap explains 529 cells not
matched by simple averaging. This is data-supported reconstruction, not retrieved
author source code. Confidence remains the pre-cap score agreement.

Do not call c a correctness probability. P=S=0.5 gives c=1 even when 0.5 encodes
missing evidence. Do not infer clinical synovitis from effusion. Use this legacy
policy only as a declared experiment, not as the evidence-aware default.

## Remaining v5 gaps

Exact four-source coefficients, conditional weighting, rule outputs, confidence
formula, and complete postprocessing remain unverified. The known three-source
equal mean does not reproduce v5; identical P/S/Gemini tuples can have different
v5 scores. A local fit is not proof of the original algorithm. Require exact
input versions/hashes and the generator before claiming faithful reproduction.

Provenance conflict: the merge card calls Pilkwang GPT-4, but the source ZIP's
api_labeler.py uses Anthropic with default model string claude-opus-5 and imports
to_scores from an absent llm_labeler module. Neither the card nor the current
default proves which model produced that CSV. Record this discrepancy rather
than silently asserting model identity. Do not execute downloaded labeler code
or submit reports merely to investigate its implementation.

# Transparent experimental fusion

This is a proposed reproducible policy, not the undisclosed Kaggle v5 formula.

## Input and compatibility

Maintain long-form records with study_id, label, source_id, score, state,
evidence_valid, scope_supported, report_hash, and source provenance. Keep raw
confidence separately. Scores must be finite within [0,1]. Validate source rows
and canonical target definitions before merging.

Probability, ordinal severity and binary-state encodings are not interchangeable.
Only fuse a shared encoding, or a documented mapping chosen before evaluation.
If supplied sources are incompatible, report their separate results and prepare
the mapping decision rather than silently averaging. Existing merged tables
without evidence use the separate import route below.

## Default policy for newly extracted compatible sources

For each study and target, let E be sources with supported binary evidence and
compatible scores. Predeclare each source weight a_s; default a_s=1. Do not use
LLM self-confidence as a calibrated reliability estimate. Sources with the same
upstream extraction must not be counted twice merely because filenames differ.

If E is nonempty:

- fused score p = sum(a_s * score_s) / sum(a_s), over E;
- disagreement d = max(score_s) - min(score_s);
- support coverage c = sum(a_s over E) / sum(a_s over all planned sources).

Do not fill missing source scores with zero. Store d and c as diagnostics,
not as diagnostic confidence. No __conf probability is manufactured.

For the default conservative export:

- at least two distinct sources must be eligible;
- supported positive and negative states must not coexist;
- case/report/image binding must meet the selected project's requirements;
- if these conditions pass, set mask=1 and weight=1;
- otherwise retain p as an audit candidate but use mask=0 and weight=0.

If E is empty, audit score=null; a training tensor may use target=0 only with
weight=0 and mask=0. Retain the study in the manifest.

Other soft-label conflict policies are legitimate experiments when explicitly
chosen and recorded. Keep them in separate versions, and never tune fusion
weights on holdout outcomes. This default favors interpretable agreement; it
does not claim to maximize training performance.

## Arithmetic checks before a real merge

- Scores [1,1,missing,missing] at equal weights: p=1, d=0, c=0.5;
  eligible for weight 1 only if scope/binding pass.
- Scores [1,0,missing,missing]: p=0.5, d=1, c=0.5; conflict, weight=0.
- Scores [0,missing,missing,missing]: p=0, c=0.25; insufficient support,
  weight=0. Do not represent this as four-source negative consensus.
- All missing: p=null, mask=0, weight=0; never a negative observation.

## Importing an existing merged CSV

Preserve the author's values and file hash. Check unique IDs, expected columns,
finite ranges, label order and manifest membership. Distinguish available
fields from undocumented provenance; do not invent the contributing scores.

For an explicitly requested v5 training trial, a declared experimental adapter
may use target=[label], weight=[label]__conf, mask=1 on valid train_candidate
rows. This adopts the external missingness and confidence policy as part of the
experiment; it does not validate that policy. Missing/nonfinite values remain
blocked rather than imputed. Never include protected rows just because the CSV
contains them. Do not overwrite local uncertainty masks or baseline files.

When exporting, retain a source registry, row-level audit sidecar, target/weight/
mask table, source hashes and protocol. Use physician_validated=false unless an
actual documented physician validation supports another status.

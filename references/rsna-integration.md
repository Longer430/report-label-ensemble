# RSNA knee project integration

These are project-relative pointers, not guaranteed current interfaces. Inspect
the selected checkout before reuse. Do not embed patient data or machine-specific
private paths in this skill.

- Read AGENTS.md, README.md, README_data.md and relevant .Codex context.
- src/rsna_knee/constants.py defines canonical label order.
- src/rsna_knee/supervision.py requires target, weight and mask together;
  all manifest train_candidate members must appear in the export.
- runs/data_v4/study_manifest.csv was the selected manifest in the motivating
  experiment. Select the user's current version rather than assuming this forever.
- scripts/train_label_comparison.py is the existing paired engine. Inspect its
  --pack, --out, --cache, --all-candidates and --shared-pretrained support.
- scripts/verify_training_comparison.py checks model replay and independent AUC;
  inspect --pack, --out and --canary before reuse.

The motivating v5 trial used local four_state as the control, v5 soft scores as
targets and __conf as loss weights. Both arms used all selected training members,
pretrained ResNet18, unchanged three-plane cache, 3 epochs and seeds 42/43/44.
These are comparison settings, not universal defaults or evidence of a particular
new run's performance. Prefer the actual current baseline and match both arms.

In that engine, filenames original_rules/four_state are two slots. When repurposed
for a trial, write an explicit variant mapping in the protocol and report; do not
infer source meaning from the slot name. Keep original baseline files unchanged.

The run_kaggle_v5_comparison.py and summarize_kaggle_v5_comparison.py scripts in
the motivating checkout contain fixed input/output paths. Inspect them rather
than blindly running or guessing arguments. A repeat needs a new output path and
a supported input adapter; do not monkeypatch global paths. Reuse engine logic
and add only the scoped interface the requested run needs.

Use the frozen development reference only for the declared development comparison.
It is provisional AI review and has sparse classes. Official holdout and independent
review groups remain protected. Check exact member/group lists, not only counts.
Follow the project's knee-review-board skill when evidence adjudication is needed;
this ensemble skill does not replace that adjudication workflow.

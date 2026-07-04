# Evals

`evals.json` is a trigger-accuracy and content-quality eval set for this skill — 12 cases: 7 positive (should trigger, load the right reference file(s), give the right kind of answer) and 5 negative (should NOT trigger — algorithm questions, CSS, single-function bugs, throwaway scripts).

## Running these

**With Claude Code / the `skill-creator` skill (recommended):**

```bash
python -m scripts.run_eval \
  --eval-set evals/evals.json \
  --skill-path . \
  --model <model-id>
```

This runs each prompt against the skill, grades outputs against the listed `expectations`, and reports a pass rate. Use `run_loop.py` instead of `run_eval.py` if you're iterating on `SKILL.md`'s description to fix a triggering problem — it splits train/test and proposes description edits automatically.

**Without the harness (manual / Claude.ai):**

Run each prompt yourself with the skill installed, check off the `expectations` by eye. This is less rigorous but catches obvious regressions — do this at minimum before merging any change to `SKILL.md`'s description or routing table, or to a reference file's core recommendations.

## Adding cases

When you add a new reference file or change routing/description, add at least:
- 2 positive cases that should load the new/changed content
- 1 negative case that's superficially adjacent but shouldn't trigger it (helps catch over-triggering)

Keep prompts realistic — actual phrasing a user would type, not a keyword-stuffed test string. Per skill-creator's guidance, prompts should be substantive enough that Claude would genuinely benefit from consulting the skill; trivial one-line asks won't reliably trigger it regardless of description quality.

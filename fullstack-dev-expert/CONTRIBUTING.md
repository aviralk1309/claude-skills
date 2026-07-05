# Contributing

Thanks for considering a contribution to `fullstack-dev-expert`.

## Before you open a PR

- **Open an issue first** for anything beyond a small fix — a new stack reference, a change to
  `SKILL.md`'s routing logic, or a restructure. This avoids duplicate work and lets us agree on
  scope before you invest time.
- Small fixes (typos, broken links, outdated version numbers, a missing edge case in an
  existing reference file) can go straight to a PR.

## Adding a new stack reference (e.g. Java/Spring, PHP/Laravel, Go)

1. Add `references/<stack-name>.md` following the structure of `python-django.md` or
   `backend.md` — cover project structure, framework specifics, ORM/data layer, auth, testing,
   deployment, and "when to pick this over the alternatives."
2. Update `SKILL.md`'s frontmatter `description` to mention the new stack's trigger terms, and
   add a routing note in "Before anything else: detect, don't assume" if the new stack needs
   its own detection logic.
3. Add at least 2 eval cases to `evals/evals.json` covering realistic tasks in the new stack.
4. Run the evals yourself against a Claude session with the updated skill loaded, and note the
   pass/fail results in your PR description.

## Adding eval cases

Follow the existing schema in `evals/evals.json`:
```json
{
  "id": <next number>,
  "prompt": "<realistic user prompt>",
  "expected_output": "<one-line summary of a good answer>",
  "expectations": ["<checkable criterion>", "..."]
}
```
Prompts should be realistic (things a real developer would actually ask), and expectations
should be specific enough that a reviewer can check pass/fail without ambiguity.

## Style for reference files

- Prose + short bullet lists, not walls of text.
- State the recommendation, then the reasoning, then the exception ("X by default; Y when Z").
- Avoid absolute claims about tool/framework versions that will go stale fast — prefer "verify
  against current docs" phrasing for anything likely to change within a year.

## Code of conduct

Be respectful, assume good faith, keep disagreements about the technical decision, not the
person. Maintainers may close PRs/issues that don't meet this bar.
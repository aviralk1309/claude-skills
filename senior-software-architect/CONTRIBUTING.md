# Contributing

Thanks for considering a contribution. This skill is a curated, opinionated reference set — the bar for merging is "does this reflect real production practice and hold up under staff-engineer-level scrutiny," not "is this technically true in some context."

## Ways to contribute

- **Fix an error** — factual mistake, outdated recommendation, broken cross-reference between files.
- **Add a gap** — a topic within scope (see README `Covers` list) that's missing or thin.
- **Improve triggering** — better positive/negative examples for `SKILL.md`'s description, based on real cases where the skill over- or under-triggered.
- **New reference file** — a new domain area (e.g. mobile architecture, cost/FinOps, observability-as-its-own-topic). Open an issue first to discuss scope before writing 3000 words nobody asked for.

## Style rules (enforced in review)

Match the existing reference files:

- **Tables over prose** for comparisons (options, tradeoffs, decision criteria). Prose for reasoning/context.
- **Name the actual principle/pattern**, don't just assert "best practice" — e.g. say "SRP violation," "N+1 problem," "CAP theorem tradeoff," not "this isn't great."
- **Every recommendation states its cost**, not just its benefit. No silver bullets. If you're not sure what the tradeoff is, that's a sign the section needs more research before it's ready to merge.
- **Concrete over abstract**: code shapes, folder structures, table schemas, interface signatures over descriptions of them.
- **Anti-patterns section at the end of each file**, ranked roughly by blast radius (data loss/security/correctness first, style last) — matches the persona's own review-ranking rule in `SKILL.md`.
- Keep each reference file self-contained but cross-link where another file covers a related angle (e.g. event-driven-architecture.md linking to database-design.md for the Saga pattern's DB-transaction boundary). Use `` `filename.md` `` inline references, not vague "see elsewhere."
- No vendor-specific pitches — name real tools as examples (Terraform, Kafka, Postgres) but keep the principle vendor-agnostic; don't write a section that only makes sense if the reader uses one specific SaaS product.

## Adding a new reference file — checklist

1. Open an issue describing scope and how it doesn't overlap an existing file (or, if it extends one, why it deserves its own file instead).
2. Add a row to the routing table in `SKILL.md` under `## Routing`.
3. Add it to `references/` folder listing in `README.md` and the `## Covers` bullet list.
4. Add an "Anti-patterns to flag" section at the end, consistent with other files.
5. Add at least 2 positive and 1 negative trigger example to `evals/evals.json` (see `evals/README.md`) covering the new topic.

## Testing changes

This skill doesn't have executable code, so "testing" means:

- **Content accuracy** — if you're changing a recommendation, cite why (a known standard, a documented failure mode, RFC/spec reference) in the PR description, not just personal experience.
- **Triggering accuracy** — if you touch `SKILL.md`'s description or routing table, run the relevant cases in `evals/evals.json` (manually, or via Claude Code's skill-creator eval harness if you have it) before and after your change and note the result in the PR.
- **Cross-reference integrity** — if you rename a file or section, grep the whole `references/` folder for other files pointing at it and update them in the same PR.

## PR review priorities

Reviewed in this order, same as the skill's own ranking philosophy:

1. Correctness — is the recommendation actually right, not just plausible-sounding
2. Overengineering risk — does this push readers toward more complexity than most cases need
3. Consistency — style, cross-links, routing table
4. Wording/polish

## Code of conduct

Be direct about technical disagreements, be kind about people. Assume good faith; this is a volunteer OSS project covering genuinely contested territory (architecture opinions vary) — cite reasoning instead of asserting authority.

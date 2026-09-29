# AGENTS.md

<!-- Template for the desk repo root. Repo facts and conventions only.
     Personal working rules live in the user-level Copilot instructions and
     are not repeated here. Pricing correctness rules live in
     .github/instructions/pricing.instructions.md and are not repeated here. -->

## Stack
<!-- WORK: language and runtime versions, key libraries -->

## Environment
<!-- WORK: environment, package manager, how to run -->

## Entry points
<!-- WORK: main scripts, services, scheduled jobs, notebooks -->

## Tests
- Run tests with: <!-- WORK: test command -->

## Checks
- <!-- WORK: check command --> validates changes. Run it before calling a change done.
- When adding a script or command sequence that will be run more than once, add a recipe for it to the repo's task runner in the same commit, if the repo has a task runner. <!-- WORK: task runner, if any -->
- When a recipe stops working or duplicates another, fix or delete it.

## CI and deployment
- Every push triggers CI and deploys to a live dev environment.
- Pipeline: <!-- WORK: CI system and stages -->
- Deployed environments: <!-- WORK: environments per branch -->

## Correctness rules
<!-- WORK: repo-specific correctness rules only. General pricing rules are in .github/instructions/pricing.instructions.md; do not copy them here. -->

## Decision records (convention to adopt)
Adopting this convention creates `docs/DECISIONS.md`. Until then, the file does not exist; do not assume it does.
- Decisions with a non-obvious mechanism go in `docs/DECISIONS.md`, newest first, as Decision / Mechanism / Reversal condition.
- Append, never edit. Supersede a decision with a new dated entry.
- Write an entry when an alternative was rejected for a reason that would have to be reconstructed later, not for every choice.
- When an entry is superseded, add a forward pointer at the top of the old entry.
- Factual corrections (paths, dates, typos) are edited in place. Only a changed decision gets a superseding entry.
- Past 15 entries, split into `docs/decisions/NNNN-kebab-title.md`, one file per entry, with a `README.md` index. Do not create the folder before then.

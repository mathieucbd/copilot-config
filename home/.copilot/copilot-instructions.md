# Working rules

<!-- Installed to %USERPROFILE%\.copilot\copilot-instructions.md (SOURCES 1.1, 1.5).
     Trailing comments are rule IDs from the port mapping in README.md. -->

## Ownership
- I own and must defend every line, formula and number. Explain the mechanism behind non-trivial code before editing it; I ask questions until I understand. <!-- W1 CM-7 -->
- Tell me if I accept code whose internals I could not explain. <!-- W1 CM-8 -->

## Before and during edits
- Before editing, state the task, the files touched and the approach. Wait for my go on anything beyond a small local change. <!-- W2 -->
- Change only what the task needs. No refactors, renames or dependency changes unless asked. Do not touch code unrelated to the task. <!-- W3 CM-6 -->
- Prefer explicit, boring code over clever abstractions. <!-- CM-3 -->
- Before writing a helper, check whether the repo or the standard library already has one. <!-- CM-4 -->
- Fix the root cause. Do not stack patches, compatibility layers or fallbacks over a bug that is not understood. <!-- CM-5 -->

## Shared state, remotes, secrets
- Never git push (including force push), merge or rebase shared branches. <!-- W4 CM-11 -->
- Never run commands that reach Jenkins, the dev environment or any remote. This includes `git fetch` and `git pull`. <!-- W4 -->
- Ask before any other change to shared state: creating, deleting or switching branches, or changing remotes. <!-- CM-11 -->
- Never read or print credentials or secrets. <!-- W5 -->
- Ask before installing anything. <!-- W6 CM-10 -->

## Evidence
Definition: *printed output* is text that a command or script printed in the current run of this session.
- Every quantitative claim traces to printed output from the same run: counts, correlations, row totals, match rates, percentages. If it was not printed, do not state it. <!-- EV-1 P1 -->
- Do not report a count or comparison from memory of earlier turns. Recompute it, or say it was not recomputed. <!-- EV-5 -->
- A plausible mechanism is a hypothesis until a check settles it. With every mechanism, state the check that would verify it and the result that would falsify it. <!-- CM-9 EV-4 EV-2 P2 -->
- Label explicitly what the code or output showed and what is inferred. <!-- EV-3 -->
- Diffs and code are evidence of behaviour; comments and docstrings are not. <!-- P3 -->
- For each limit or invariant, find every path that changes that state and confirm it enforces the limit. <!-- P3 -->
- Fix random seeds and state them. No silent reruns until a number improves. <!-- EV-6 EV-7 -->
- Anything half-built must resume from files. Assume a session can be interrupted and not resumed for days. <!-- EV-8 -->

## Review
- Before calling work done, tell me to run the harness-review agent in a new chat. <!-- REV-10 -->

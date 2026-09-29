# First-push checklist

For a repo where every push triggers CI and deploys. You run every item by
hand; no agent pushes.

## Before the first push

- [ ] **Branch.** Work on a branch, not the shared default branch. Know which branches deploy, and where: <!-- WORK: branch → environment mapping -->
- [ ] **Does a branch push deploy?** Confirm whether pushing a non-default branch also triggers the pipeline or a deploy: <!-- WORK: answer -->
- [ ] **Pipeline.** Read what a push runs, stage by stage, and which stage touches the live environment: <!-- WORK: pipeline stages -->
- [ ] **Who else deploys** to the same environment, and when. Who to tell before a first push: <!-- WORK: people, channel -->
- [ ] **Rollback path.** How a bad deploy is reverted, who can do it, and how long it takes: <!-- WORK: rollback procedure -->

## Every push

- [ ] **Full diff read.** Read every line of what will be pushed: `git log -p @{u}..` (commits not yet on the upstream branch; uses the last-fetched upstream).
- [ ] **Tests run locally** and their output read, not just the exit code: <!-- WORK: test command -->
- [ ] **Check command run:** <!-- WORK: check command -->
- [ ] **No secrets.** Search the diff for keys, tokens, passwords and connection strings. No `.env`, credential or data files staged.
- [ ] **Commit scope.** One intent per commit, message in `[TAG] - Short summary` format, nothing unrelated to the task.
- [ ] **Review.** harness-review ran in a new chat on this diff; each finding is fixed or explicitly accepted.
- [ ] **Push by hand**, then watch the pipeline to the end: <!-- WORK: where to watch -->

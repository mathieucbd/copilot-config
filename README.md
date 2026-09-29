# copilot-config

A port of my Claude Code harness to GitHub Copilot in VS Code, for the
**Copilot harness** (Session Target: *Copilot*, running on the Agent Host).
It covers Copilot's behaviour only: instructions, custom agents and Copilot
keys in VS Code **user** settings. It changes nothing in a work project's
build, CI, `pyproject` or `.vscode/`.

Folders mirror their destinations, so installing is a literal copy:
`home/.copilot/` → `%USERPROFILE%\.copilot\`, `repo/` → desk repo root,
`home/vscode/` → merged by hand into user `settings.json`.

Every format, path and key is sourced in [docs/SOURCES.md](docs/SOURCES.md);
bracketed references below (e.g. [§4], [2c.5]) point there.
[docs/claude-code-to-copilot.md](docs/claude-code-to-copilot.md) maps Claude
Code concepts to Copilot and explains how to organise a project.

## Contents

| File | Installs to (Windows) | Purpose |
|------|-----------------------|---------|
| `home/.copilot/agents/harness-audit.agent.md` | `%USERPROFILE%\.copilot\agents\` [2c.1] | Read-only inventory of existing customizations. Installed **first and alone**. |
| `home/.copilot/copilot-instructions.md` | `%USERPROFILE%\.copilot\copilot-instructions.md` [1.1, 1.5] | Always-on working and evidence rules. |
| `home/.copilot/agents/harness-defend.agent.md` | `%USERPROFILE%\.copilot\agents\` | `/defend`: oral examination. Opus 5.5, read-only. |
| `home/.copilot/agents/harness-review.agent.md` | `%USERPROFILE%\.copilot\agents\` | `/review`: outside review in a new chat. Opus 5.5, no edit tool. |
| `home/.copilot/agents/harness-commit.agent.md` | `%USERPROFILE%\.copilot\agents\` | `/commit`: split, draft, commit locally after my go. Sonnet 5. |
| `home/vscode/settings.snippet.jsonc` | merged into `%APPDATA%\Code\User\settings.json` [7.7] | Approval guards and commit-message text. |
| `repo/AGENTS.md` | desk repo root [1.7] | Repo facts and conventions, with WORK placeholders. |
| `repo/.github/instructions/pricing.instructions.md` | desk repo `.github/instructions/` [1.7, 7.2] | Pricing correctness rules, applied to `**/*.py`. |
| `docs/SOURCES.md` | — | Every fact with its doc URL. |
| `docs/first-push-checklist.md` | — | Checklist for a repo where every push deploys. |
| `docs/claude-code-to-copilot.md` | — | Claude Code → Copilot parallels; how to organise a project. |

`~` in the docs is the user home folder, so `~/.copilot` is
`%USERPROFILE%\.copilot` on Windows. No page spells out the Windows path; the
audit confirms it by what Copilot actually loads [1.5]. If `COPILOT_HOME` is
set, it replaces `%USERPROFILE%\.copilot` [1.4].

## Why custom agents

Custom agents are the only format that can pin a model (`model`) and restrict
tools (`tools`) [2c.5, 2c.6]. Skills can do neither [2b.5, 2b.6]. Prompt files
are not loaded by the Copilot harness [2a.1]. All four agents set
`disable-model-invocation: true`, so another agent cannot run them as a
subagent, and you select them from the agents dropdown.

## Versions

The work machine's VS Code is managed and may lag the docs (VS Code docs
dated 2026-09-16; release notes read up to 1.139). Minimum VS Code versions
[§6]:

| Feature / key | Minimum VS Code |
|---------------|-----------------|
| Copilot harness on the Agent Host | 1.129 as opt-in (`chat.agentHost.enabled`); the version where it no longer needs opt-in is **not documented** |
| `~/.copilot/copilot-instructions.md` read by VS Code | **not documented** |
| Custom agents (`.agent.md`) | 1.106 |
| `model` in agent frontmatter | 1.102 |
| `user-invocable`, `disable-model-invocation` | 1.109 |
| `github.copilot.chat.cli.customAgents.enabled` | 1.107 |
| `chat.tools.terminal.autoApprove` | ≤ 1.103 |
| `chat.tools.terminal.enableAutoApprove` | 1.104 |
| `chat.tools.edits.autoApprove` | ≤ 1.104 |
| `chat.tools.global.autoApprove` | ≤ 1.104 |
| `chat.tools.eligibleForAutoApproval` | ≤ 1.107 |
| `chat.permissions.default` | ≤ 1.124 |
| `chat.editing.autoAcceptDelay`, `github.copilot.chat.commitMessageGeneration.instructions` | not announced since 1.99; **not documented** |
| `chat.assistedPermissions.enabled`, `chat.agent.sandbox.allowAutoApprove`, `github.copilot.chat.claudeAgent.enabled`, `chat.agentHost.claudeAgent.enabled` | **not documented** |
| Claude Sonnet 5 | 1.124 |
| Claude Opus 5.5 | "TBD" in GitHub's table |

"≤ N" means the first release note that mentions the key is N; it may be
older. **Copilot Chat extension** minimum versions are not documented for any
feature. harness-audit records the installed VS Code and extension versions,
the active harness, and flags every row it cannot confirm.

## Models

| Agent | Model | Price per 1M tokens, input / output [5.1] |
|-------|-------|-------------------------------------------|
| harness-defend | Claude Opus 5.5 | $4.00 / $20.00 |
| harness-review | Claude Opus 5.5 | $4.00 / $20.00 |
| harness-audit | Claude Opus 5.5 | $4.00 / $20.00 |
| harness-commit | Claude Sonnet 5 | $2.00 / $10.00 |

**Allowed models** [5.1, 5.4]:
- Claude: Opus 5.5, Sonnet 5, Sonnet 5.5
- GPT: GPT-6 Sol, GPT-6.1 Sol, GPT-5.6 Terra

This list is the floor for the session model picker and for silent fallback.
If an agent's model cannot be used, Copilot falls back to the session model
without failing [2c.5]; with the session model on *Auto*, the agent inherits
whatever Auto resolved to, which can be a lightweight model [4.8].

Treating these GPT models as the same tier as Claude Sonnet 5 is based on
price and GitHub's category labels, not on a measured comparison.

Rule: keep a model from the allowed list selected in the chat model picker.
**Never select Auto.** No user setting pins the default model (`chat.defaultModel`
exists only as an admin policy [4.7]).

**Which model for what** (session picker; pinned agent models above stay as they are):

| Work | Model |
|------|-------|
| Understanding legacy code, pricing maths, defend, review, audit | Claude Opus 5.5 |
| Mechanical edits, commit | Claude Sonnet 5, GPT-6 Sol or GPT-6.1 Sol: the cheapest on the list, $2.00 input / $10.00 output per 1M tokens; GPT-6.1 Sol has cheaper cached input ($0.10 vs $0.20) [5.1] |

## Install

Never overwrite an existing file. Install nothing the audit did not cover.

### 1. Audit first (harness-audit only)

1. List the target folder by hand and confirm the file is not there:
   ```powershell
   Get-ChildItem "$env:USERPROFILE\.copilot\agents" -ErrorAction SilentlyContinue
   Test-Path "$env:USERPROFILE\.copilot\agents\harness-audit.agent.md"   # must print False
   ```
2. Copy only `harness-audit.agent.md`:
   ```powershell
   New-Item -ItemType Directory -Force "$env:USERPROFILE\.copilot\agents" | Out-Null
   Copy-Item home\.copilot\agents\harness-audit.agent.md "$env:USERPROFILE\.copilot\agents\"
   ```
   `New-Item -Force` on an existing directory leaves its contents alone.
3. Open the **desk repo alone** in VS Code. New chat, Session Target **Copilot**.
   Open the agents dropdown and check that **harness-audit** is listed.
   Only if it is missing, set `"github.copilot.chat.cli.customAgents.enabled": true`
   in user settings and reload the window; the docs conflict on whether this is
   needed [2c.8, 2c.14].
4. Select harness-audit, model Claude Opus 5.5 (or leave the picker on a
   model from the allowed list), and send: `Run the audit.` Approve each read-only command after
   reading it. Answer its question about the harness.
5. Cross-check: `Chat: Open Customizations` (Copilot harness selected). Compare
   the Instructions, Agents and Skills lists with the audit's inventory. The
   editor shows what Copilot **discovered**; the audit shows what **exists**.
   Anything in one list and not the other is a finding. For errors, run
   `Developer: Open Agent Debug Logs` [1.12].

The audit's report lists `harness-audit.agent.md` as a file you installed.
Stop here if it reports `ChatStrictPluginOnlyCustomization`: that policy blocks
every user-level file in this repo [H7].

### 2. Install the user-level files

Only for targets where the audit reported no clash. From the copilot-config
folder, this copies `home\.copilot\` into `%USERPROFILE%\.copilot\` file by
file and skips any file that already exists (including harness-audit from
step 1):

```powershell
$src = (Resolve-Path "home\.copilot").Path
$dst = "$env:USERPROFILE\.copilot"
Get-ChildItem $src -Recurse -File | ForEach-Object {
  $target = Join-Path $dst $_.FullName.Substring($src.Length + 1)
  if (Test-Path $target) { Write-Output "EXISTS, skipped: $target" }
  else {
    New-Item -ItemType Directory -Force (Split-Path $target) | Out-Null
    Copy-Item $_.FullName $target
    Write-Output "copied: $target"
  }
}
```

For a file reported as EXISTS, merge by hand following the audit's verdict.

### 3. Merge the settings snippet

1. `Preferences: Open User Settings (JSON)`. If you use a non-default profile,
   this opens that profile's file [7.7].
2. For each key in `home/vscode/settings.snippet.jsonc`, search the open file
   for the key name first. If it exists, edit its value in place. Never leave
   two copies of a key: which copy wins is not something to rely on.
3. For object values (`chat.tools.terminal.autoApprove`,
   `chat.tools.edits.autoApprove`, `chat.tools.eligibleForAutoApproval`), merge
   entries into the existing object rather than replacing it.
4. Save. VS Code marks errors in the file with red squiggles.
5. In the Settings UI, search each key and confirm the value took effect. Org
   policy overrides user values [§4]; note any key whose value you cannot
   change, and compare with the policies the audit found.
6. Run the tests below.

### 4. Desk repo files (after the audit, through the normal push path)

`repo/` mirrors the desk repo root: `repo/AGENTS.md` and
`repo/.github/instructions/pricing.instructions.md`. Copy them with the same
no-overwrite loop, from the copilot-config folder:

```powershell
$src = (Resolve-Path "repo").Path
$dst = "<!-- WORK: desk repo path -->"
Get-ChildItem $src -Recurse -File -Force | ForEach-Object {
  $target = Join-Path $dst $_.FullName.Substring($src.Length + 1)
  if (Test-Path $target) { Write-Output "EXISTS, skipped: $target" }
  else {
    New-Item -ItemType Directory -Force (Split-Path $target) | Out-Null
    Copy-Item $_.FullName $target
    Write-Output "copied: $target"
  }
}
```

`-Force` makes `Get-ChildItem` include the hidden `.github` folder. If the
audit found an existing `AGENTS.md`, merge by hand instead. Fill the WORK
placeholders. These are repo changes: they are shared with anyone else using
Copilot in the repo, and they go through
[docs/first-push-checklist.md](docs/first-push-checklist.md) like any other
commit. Pushing them deploys.

## Confirm each piece loaded

Methods from the docs [1.12]: the Customizations editor lists what was
discovered; **References** in a chat response shows which instructions were
used; `Developer: Open Agent Debug Logs` and Chat view right-click →
**Diagnostics** show loading errors.

| Piece | Check |
|-------|-------|
| `copilot-instructions.md` | New chat (Copilot harness, default agent). Ask: "What are your rules on git push?" Expand **References**: `copilot-instructions.md` is listed, and the answer matches the file. |
| `.github/instructions/pricing.instructions.md` (desk repo) | Attach a `.py` file and ask a question about it. **References** lists `pricing.instructions.md` (via `applyTo`). If it does not, ask a pricing question naming "pricing correctness": it can also load by `description` [1.3]. |
| Each agent | Listed in the agents dropdown; in the Customizations editor, hover shows the path under `%USERPROFILE%\.copilot\agents` [2c.1]. |
| Agent model | Select the agent and check the model picker and the Agent Debug Logs for the model used. If it shows a different model, the pin fell back [2c.5]; record it. |
| Agent tools | In the Customizations editor, the agent's tools list shows no `edit` tool for defend, review, commit and audit. Ask harness-defend to fix a typo: it has no edit tool and must decline. |

## Settings tests

Run in a scratch folder (`mkdir scratch; cd scratch; git init`), opened alone,
Copilot harness, default agent, Manual permissions.

| ID | Key | Test | Pass |
|----|-----|------|------|
| T1 | `chat.tools.global.autoApprove: false` | Ask: "Fetch https://code.visualstudio.com/updates." | An approval prompt appears before the fetch. |
| T2 | `chat.tools.terminal.enableAutoApprove: false` | Ask: "Run `git status`." (`git status` is a read-only command the default allow-list would auto-run.) | An approval prompt appears. |
| T3 | `chat.tools.terminal.autoApprove` (backstop) | Temporarily set `enableAutoApprove` to `true`. Ask: "Run `git merge --help`." then "Run `git status`." Set `enableAutoApprove` back to `false` and repeat T2. | `git merge --help` prompts; `git status` runs without a prompt (the rules are live); T2 passes again. |
| E1 | `chat.tools.edits.autoApprove: {"**/*": false}` (inference) | Ask: "Create `probe.txt` containing x." Reject the prompt. Repeat for `.probe` and `sub\deep\probe.py`. Then `Test-Path probe.txt, .probe, sub\deep\probe.py`. | A diff approval prompt appears **before** each write; all three `Test-Path` print `False`. If any file exists, the inference is wrong: record it as a gap. |
| T4 | `chat.editing.autoAcceptDelay: 0` | Copilot harness sessions have no pending edits [4.1], so there is nothing to auto-accept. Check the value is `0` in the Settings UI. If you ever use the Local harness: after an edit, wait 30 seconds. | Value is `0`; in Local, edits stay pending (keep/undo controls remain). |
| T5 | `chat.tools.eligibleForAutoApproval` | When the T2 prompt appears, open the dropdown next to **Allow**. | No option to allow for the session, workspace or always. If options appear, the tool ID does not match in this harness [7.3]: record it as a gap. |
| T6 | `chat.permissions.default: "default"` | Open a new chat. | The permissions picker shows **Manual permissions**. |
| T7 | `chat.assistedPermissions.enabled: false` | Open the permissions picker. | No **Assisted permissions** entry. |
| T8 | `chat.agent.sandbox.allowAutoApprove: false` | Sandboxing is off by default, so this is a backstop. Check the value in the Settings UI. | Value is `false`. |
| T9 | `github.copilot.chat.claudeAgent.enabled: false`, `chat.agentHost.claudeAgent.enabled: false` | Reload the window, open the Session Target control. | **Claude** is not listed. |
| T10 | `github.copilot.chat.commitMessageGeneration.instructions` | In the scratch repo, stage a file, then select the sparkle (generate) button in the Source Control commit box. | The message starts with `[TAG] - ` and has no trailer. |

Delete the scratch folder afterwards.

## Using the agents

| Command | How |
|---------|-----|
| /defend | New chat → agents dropdown → **harness-defend**. Name the code, or attach or paste the diff. |
| /review | **New chat** (`+`) → **harness-review**. Optional scope in the message. Never run it in the chat that wrote the code, or from a side chat or a handoff: those carry the conversation [§3]. |
| /commit | **harness-commit**. It shows the staged and unstaged summary, the proposed split and messages, and commits locally only after your go. It never pushes. |
| audit | **harness-audit**, in the repo you want to inventory. |

## Guard gaps

A guard covers the tools it names, not the outcome. For each guard, the
other paths that reach the same outcome:

**Terminal approval** (`enableAutoApprove: false`)
- Tasks, notebook cells, VS Code commands (for example Git: Push through
  `vscode/runCommand`), extension installs and web fetch are listed in
  `chat.tools.eligibleForAutoApproval`, but those tool IDs are **unverified**
  in the Copilot harness [7.3, 7.4]. Test T5 checks only the terminal ID.
- MCP server tools can act on remotes (a GitHub MCP server can push files or
  merge pull requests through the API). MCP approvals can be granted at
  workspace or user level and persist. Review them with
  `Chat: Manage Tool Approval`; clear them with `Chat: Reset Tool Confirmations` [4.4].
- Extension-contributed tools: same as MCP. Only admin policy
  `ChatAgentExtensionTools` disables them [§4].
- The prompt is only as good as the click. Remote control (`/remote on`)
  syncs approvals to GitHub.com and GitHub Mobile [7.10].
- Terminal auto-approval parsing is best-effort, not a security boundary [4.3].

**Edit approval** (`edits.autoApprove: {"**/*": false}`)
- Terminal commands and scripts can write files without the edit tool. Each
  command still prompts under T2.
- Notebook edits (`edit/editNotebook`): whether the glob covers them is **unverified**.
- The catch-all glob itself is an **inference**; test E1 settles it.

**Never push, merge, rebase or reach remotes**
- Enforced by the instructions, harness-commit's rules and the terminal
  prompt. There is no hard block; that needs a `PreToolUse` hook, which is
  out of scope [4.6].
- `/delegate` and the Cloud target hand work to the cloud agent, which pushes
  a branch and opens a pull request. No user setting disables them [§4
  controls table]. **Rule:** never use `/delegate` or the Cloud target.
  **Ask admin:** confirm the "Copilot cloud agent" policy is disabled for your
  org, or the desk repo is excluded under Repository access. It is disabled
  by default for org-assigned Enterprise licences [G10, G11].

**Manual permissions** (`permissions.default`, `global.autoApprove`)
- Allow all, Autopilot, `/yolo` and `/autoApprove` can still be chosen per
  session. No user setting hides them. **Rule:** never select them. **Ask
  admin:** the `ChatToolsAutoApprove` policy hides them [§4 controls table].
- Worktree sessions always run with Allow all [H6]. No setting disables them;
  they are offered only in the Agents window. **Rule:** start sessions from the
  Chat view, or leave **New Worktree** unticked. No admin control found.
- Org policy overrides user values for `chat.tools.global.autoApprove`,
  `chat.tools.terminal.enableAutoApprove`, `chat.tools.eligibleForAutoApproval`,
  `chat.agent.sandbox.allowAutoApprove` and both Claude keys [§4, 7.5]. The
  audit reports which policies are set.

**Other harnesses**
- Claude is removed by the snippet (T9). Codex is off by default
  (`chat.agentHost.codexAgent.enabled: false`). **Rule:** do not enable
  another harness at work.

**Fresh context for harness-review**
- Depends on you opening a new chat. Step 0 of the agent refuses if the chat
  has earlier messages, but that is an instruction, not a structural check.
- On the Agent Host, **built-in session-management tools** can read another
  session's recent context [§3]. The docs do not name these tools, so the
  agent's `tools` list cannot exclude them by name, and whether an explicit
  `tools` list excludes them anyway is **unverified**.

**Model floor**
- A pinned model that cannot be used falls back silently to the session
  model [2c.5]. Covered only by the allowed-list picker rule above.

**Tools restriction on the agents**
- harness-review, harness-commit and harness-audit have `execute`: any command
  is possible, and each one asks for approval. harness-defend has no terminal
  and no edit tool.
- Built-in subagents (for example `task`) run with the parent's permissions [G5].

**Instruction loading**
- Whether a selected custom agent receives **user-level** instructions is not
  documented [7.1]; the agents' own rules are in their bodies. harness-review
  reads `.github/instructions/pricing.instructions.md` with its file tools
  rather than relying on `applyTo` loading.
- `pricing.instructions.md` loads only when a matching file is worked on, or
  when its description matches the task [1.2, 1.3].

## Admin questions

1. Is `ChatStrictPluginOnlyCustomization` set? It blocks this whole harness [H7].
2. Is the "Copilot cloud agent" policy disabled for our org, or the desk repo excluded? [G10, G11]
3. Can `ChatToolsAutoApprove` be set to disable global auto-approval, Allow all and Autopilot? [§4]
4. Which VS Code and Copilot Chat versions are deployed, and when do they update? [§6]

## Unverified items

- Windows path `%USERPROFILE%\.copilot\...` for `~/.copilot` [1.5].
- VS Code version where the Copilot harness needs no opt-in; version reading `~/.copilot/copilot-instructions.md`; all Copilot Chat extension minimums [§6].
- Whether `github.copilot.chat.cli.customAgents.enabled` is needed [2c.8, 2c.14].
- `modelPolicy`, `reasoningEffort`, `include-custom-instructions` in VS Code (not used here) [2c.11].
- Whether VS Code accepts `model: Claude Opus 5.5` as written (display name, per the VS Code examples) [5.3, 2c.10].
- `{"**/*": false}` meaning "ask for every edit" (test E1) [§4].
- Tool IDs in `chat.tools.eligibleForAutoApproval` for the Copilot harness, and the key's value type (test T5) [7.3, 7.4].
- Whether a selected custom agent receives user-level instructions [7.1].
- Names of the session-management tools, and whether a `tools` list excludes them [§3].
- `chat.defaultModel` as a user setting (not used) [4.7].
- Whether `ChatAgentSandboxEnabled` governs the Windows sandbox key [§4].

## Rule mapping

Source files: `~/.claude/CLAUDE.md` (CM), `blocks/evidence.md` (EV),
`blocks/project-conventions.md` (PC), `skills/defend/SKILL.md` (DEF),
`skills/review/SKILL.md` (REV), `commands/commit.md` (COM),
`blocks/correctness-pricing.md` (PR). Work rules (W) and principles (P) come
from the build brief:

- W1: I own and must defend every line, formula and number; explain before editing; tell me if I accept code I could not explain.
- W2: Before editing, state task, files and approach; wait for my go beyond a small local change.
- W3: Change only what the task needs; no refactors, renames or dependency changes unless asked.
- W4: Never git push, merge or rebase shared branches; never reach Jenkins, the dev environment or any remote.
- W5: Never read or print credentials or secrets.
- W6: Ask before installing anything.
- P1: Every quantitative claim traces to printed output from the same run.
- P2: A plausible mechanism is a hypothesis until a check settles it.
- P3: Diffs and code are evidence of behaviour, comments and docstrings are not; for each limit or invariant, check every path that changes that state.

| ID | Destination |
|----|-------------|
| CM-1 | DROPPED: personal-machine fact (WSL, no display, plots to file). `repo/AGENTS.md` Environment placeholder. |
| CM-2 | DROPPED: personal-machine fact (uv, WSL terminal). `repo/AGENTS.md` Environment placeholder. |
| CM-3, CM-4, CM-5 | `home/.copilot/copilot-instructions.md` |
| CM-6 | `home/.copilot/copilot-instructions.md`, merged with W3 |
| CM-7, CM-8 | `home/.copilot/copilot-instructions.md`, merged with W1 |
| CM-9 | `home/.copilot/copilot-instructions.md`, merged with EV-2, EV-4, P2 |
| CM-10 | `home/.copilot/copilot-instructions.md`, merged with W6 |
| CM-11 | `home/.copilot/copilot-instructions.md`, merged with W4 (stricter: never, for push/merge/rebase) |
| CM-12, CM-13 | DROPPED: personal-machine log (`papercuts.md`). |
| EV-1 | `home/.copilot/copilot-instructions.md`, merged with P1 |
| EV-2, EV-4 | `home/.copilot/copilot-instructions.md`, merged with CM-9, P2 |
| EV-3, EV-5, EV-8 | `home/.copilot/copilot-instructions.md` |
| EV-6, EV-7 | `home/.copilot/copilot-instructions.md` (apply to Monte Carlo pricers) |
| PC-1 to PC-6 | `repo/AGENTS.md`, Decision records, as a convention to adopt |
| PC-7 | `repo/AGENTS.md`, Checks, with `<!-- WORK: check command -->` |
| PC-8, PC-9 | `repo/AGENTS.md`, Checks, reworded for the repo's task runner |
| DEF-1 to DEF-7 | `harness-defend.agent.md`; DEF-7 also structural (no edit or execute tool) |
| DEF-8 | NOT ENFORCEABLE as an auto-trigger: automatic selection by description applies only to subagents, which cannot hold an interview [2c.4, §3]. Manual selection; `disable-model-invocation: true`. |
| DEF-9 | Your message after selecting the agent (agents have no `$ARGUMENTS`); `argument-hint` prompts for it. |
| REV-1 | PARTIAL: new chat plus the agent's step 0 check; not structural [§3]. `disable-model-invocation: true` stops use as a subagent. |
| REV-2 to REV-4, REV-6 to REV-9 | `harness-review.agent.md` |
| REV-5 | `harness-review.agent.md`: "violations of the pricing correctness instructions and of any correctness rules in the repo's AGENTS.md"; the agent reads `.github/instructions/pricing.instructions.md` with its file tools [7.1]. |
| REV-10 | NOT ENFORCEABLE as an auto-trigger. `home/.copilot/copilot-instructions.md` tells the agent to remind me to run harness-review. |
| COM-1 to COM-6 | `harness-commit.agent.md`; "never push" explicit. COM-3 to COM-5 also in `github.copilot.chat.commitMessageGeneration.instructions` (message text only). |
| COM-7 | Your message after selecting the agent; `argument-hint`. |
| W1 to W6, P1 to P3 | `home/.copilot/copilot-instructions.md` (merged as above). P3 also in `harness-review.agent.md` stance. |
| PR-1 | `repo/.github/instructions/pricing.instructions.md`. The file's rationale sentence ("Pricing bugs return plausible numbers, not errors") is not ported: rules only. |
| PR-2 | `repo/.github/instructions/pricing.instructions.md`, with `<!-- WORK: bank validation standards and tolerances -->` |
| PR-3 to PR-12 | `repo/.github/instructions/pricing.instructions.md` |

No PR rule was dropped as research- or backtest-specific: all twelve apply to
pricing code.

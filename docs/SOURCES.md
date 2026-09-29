# Sources

Every format fact, file location, frontmatter field and setting key used in
this repo, with the page it came from. Pages fetched 2026-09-29; the VS Code
pages carry a "last updated" date of 2026-09-16 (overview: 2026-09-18).

Status markers: **[GA]** stable/documented without a label, **[Preview]**,
**[Experimental]**, **[Deprecated]**, **[CONFLICT]** docs disagree,
**[UNVERIFIED]** inferred, not stated by any page — verify on the machine.

## Pages

| ID  | URL |
|-----|-----|
| S1  | https://code.visualstudio.com/docs/agent-customization/custom-instructions |
| S2  | https://code.visualstudio.com/docs/agent-customization/overview |
| S3  | https://code.visualstudio.com/docs/agent-customization/agent-skills |
| S4  | https://code.visualstudio.com/docs/agent-customization/custom-agents |
| S5  | https://code.visualstudio.com/docs/agent-customization/prompt-files |
| S6  | https://code.visualstudio.com/docs/agents/run/agent-harnesses |
| S7  | https://code.visualstudio.com/docs/agents/run/subagents |
| S8  | https://code.visualstudio.com/docs/agents/concepts/sessions |
| S9  | https://code.visualstudio.com/docs/agents/run/approvals |
| S10 | https://code.visualstudio.com/docs/agents/run/security |
| S11 | https://code.visualstudio.com/docs/agents/run/review-code-edits |
| S12 | https://code.visualstudio.com/docs/agents/run/tools |
| S13 | https://code.visualstudio.com/docs/agents/reference/ai-settings |
| S14 | https://code.visualstudio.com/docs/enterprise/policies |
| G1  | https://docs.github.com/en/copilot/how-tos/copilot-cli/customize-copilot/add-custom-instructions |
| G2  | https://docs.github.com/en/copilot/how-tos/copilot-cli/customize-copilot/create-custom-agents-for-cli |
| G3  | https://docs.github.com/en/copilot/reference/copilot-cli-reference/cli-command-reference |
| G4  | https://docs.github.com/en/copilot/reference/custom-agents-configuration |
| G5  | https://docs.github.com/en/copilot/concepts/agents/copilot-cli/about-custom-agents |
| G6  | https://docs.github.com/en/copilot/concepts/billing-and-usage/organizations-and-enterprises/billing |
| G7  | https://docs.github.com/en/copilot/reference/copilot-billing/models-and-pricing |
| G8  | https://docs.github.com/en/copilot/reference/copilot-billing/request-based-billing-legacy/model-multipliers-for-annual-plans |
| G9  | https://docs.github.com/en/copilot/reference/ai-models/supported-models |
| G10 | https://docs.github.com/en/copilot/how-tos/administer-copilot/manage-for-enterprise/manage-agents/enable-copilot-cloud-agent |
| G11 | https://docs.github.com/en/copilot/how-tos/administer-copilot/manage-for-organization/add-copilot-cloud-agent |
| S15 | https://code.visualstudio.com/docs/agents/reference/tools-reference |
| S16 | https://code.visualstudio.com/docs/agents/run/sessions/manage-sessions |
| S17 | https://code.visualstudio.com/docs/configure/settings |
| S18 | https://code.visualstudio.com/docs/configure/command-line |
| C1  | https://code.claude.com/docs/en/memory |
| C2  | https://code.claude.com/docs/en/settings |
| C3  | https://code.claude.com/docs/en/skills |
| C4  | https://code.claude.com/docs/en/sub-agents |
| C5  | https://code.claude.com/docs/en/hooks |
| C6  | https://code.claude.com/docs/en/iam |
| RN  | https://code.visualstudio.com/updates/v1_NNN — release notes 1.99 to 1.140 (1.139 released; 1.140 is Insiders) |

## 0. Harnesses — read this first

| # | Fact | Source |
|---|------|--------|
| H1 | VS Code runs chat on a selectable *agent harness*: Local, Copilot, Claude, Codex (+ Cloud target). Chosen with the Session Target control in the chat input. | S6 |
| H2 | "Choose Copilot for general coding tasks." The Copilot harness runs on the Agent Host via the Copilot SDK. | S6 |
| H3 | **[Deprecated]** The Local harness "will be removed in a future release". | S2, S5, S13 |
| H4 | Agent Host sessions read user customizations from `~/.copilot` (Copilot) / `~/.claude` (Claude), not from VS Code profile user data. | S2, S4 |
| H5 | Customizations are scoped per harness; the Agent Customizations editor (`Chat: Open Customizations`) shows what the selected harness discovers. | S1, S2 |
| H6 | Worktree sessions always use Allow all; Folder sessions offer Manual permissions. | S6, S9 |
| H7 | Org policy `ChatStrictPluginOnlyCustomization` "blocks standalone user and workspace skills, agents, hooks, instructions, and MCP servers". If set, nothing in this repo loads. | S14 |
| H8 | Policy `ChatEditorPreferCopilotHarness` (setting `chat.editor.preferCopilotHarness`, **[Experimental]**) lets admins force the Copilot harness for new editor chats. | S6, S14 |

## 1. User-level custom instructions

| # | Fact | Key / path | Status | Source |
|---|------|-----------|--------|--------|
| 1.1 | Personal always-on instructions for Copilot Agent Host sessions. | `~/.copilot/copilot-instructions.md` | GA | S1, G1 |
| 1.2 | Modular user instructions (Copilot harness). Applied only when `applyTo` matches a file being worked on, or loaded on demand via `description`. | `~/.copilot/instructions/**/*.instructions.md` | GA | S1, G1 |
| 1.3 | `.instructions.md` frontmatter: `name` (display), `description` (on-demand discovery), `applyTo` (glob, relative to workspace root; `**` = all). All optional. No frontmatter is documented for `copilot-instructions.md`. | `name`, `description`, `applyTo` | GA | S1 |
| 1.4 | `COPILOT_HOME` env var replaces `$HOME/.copilot` for both user instruction locations. | `COPILOT_HOME` | GA | G1 |
| 1.5 | On Windows, `~` / `$HOME` resolves to the user home folder, i.e. `%USERPROFILE%\.copilot\copilot-instructions.md`. S3/S4 say `~` means "your environment user home folder"; no page spells out the Windows path. | — | **[UNVERIFIED]** | S3, S4 |
| 1.6 | Local harness user instructions live in "VS Code profile storage"; exact Windows path not documented. Local also reads `~/.copilot/instructions` and `~/.claude/rules` via the default of the deprecated `chat.instructionsFilesLocations`. | `chat.instructionsFilesLocations` | **[Deprecated]** | S1, S13 |
| 1.7 | Repo sources read by the Copilot harness: `.github/copilot-instructions.md`, `.github/instructions/**/*.instructions.md`, `AGENTS.md`, `CLAUDE.md` (+ `.claude/CLAUDE.md`), `GEMINI.md`. | — | GA | G1 |
| 1.8 | Combination: all applicable sources are **additive**; "Do not depend on a file order or precedence rule to resolve conflicts". CLI removes identical duplicate copies but defines no precedence. | — | GA | S1, G1 |
| 1.9 | Organization instructions are additive to user and repo instructions. | `github.copilot.chat.organizationInstructions.enabled` (default `true`) | GA | S1, S13 |
| 1.10 | Toggles — **documented as Local-harness settings**: | | | S1, S13 |
| | `.github/copilot-instructions.md` | `github.copilot.chat.codeGeneration.useInstructionFiles` (default `true`) | GA | |
| | `AGENTS.md` at root | `chat.useAgentsMdFile` (default `true`) | GA | |
| | nested `AGENTS.md` | `chat.useNestedAgentsMdFiles` (default `false`) | **[Experimental]** | |
| | `CLAUDE.md`, `.claude/CLAUDE.md`, `~/.claude/CLAUDE.md`, `CLAUDE.local.md` | `chat.useClaudeMdFile` (default `true`) | GA | |
| | `applyTo`-matched files | `chat.includeApplyingInstructions` (default `true`) | GA | |
| | Markdown-linked instruction files | `chat.includeReferencedInstructions` (default `false`) | GA | |
| 1.11 | Copilot harness per-file toggle: CLI `/instructions` command lists discovered files and enables/disables each. No VS Code setting documented for the Copilot harness. | `/instructions` | GA (CLI) | G1 |
| 1.12 | Verify loading: Customizations editor lists the file (discovery only); expand **References** in a response; `Developer: Open Agent Debug Logs`; Chat view right-click → **Diagnostics**. | — | GA | S1, S2, S4 |

## 2. Reusable commands

### 2a. Prompt files (`*.prompt.md`)

| # | Fact | Status | Source |
|---|------|--------|--------|
| 2a.1 | "Prompt files are deprecated for Agent Host sessions and aren't loaded by Agent Host." Local-only. | **[Deprecated]** | S5, S2 |
| 2a.2 | Locations (Local): workspace `.github/prompts`; user = VS Code profile user data. | Deprecated | S5, S13 (`chat.promptFilesLocations`) |
| 2a.3 | Can set `tools` and `agent` in frontmatter; prompt-file tools override the agent's. | Deprecated | S5 |

### 2b. Agent skills (`SKILL.md`)

| # | Fact | Key | Status | Source |
|---|------|-----|--------|--------|
| 2b.1 | Workspace: `.github/skills/`, `.claude/skills/`, `.agents/skills/`. User: `~/.copilot/skills/`, `~/.claude/skills/`, `~/.agents/skills/`. One folder per skill containing `SKILL.md`. | — | GA | S3, G3 |
| 2b.2 | Frontmatter: `name` (required; lowercase, digits, hyphens; must equal folder name; invalid → **silently not loaded**), `description` (required, ≤1024 chars), `argument-hint`, `user-invocable` (default `true`), `disable-model-invocation` (default `false`). | as listed | GA | S3 |
| 2b.3 | `context: fork` runs the skill in a dedicated subagent; requires `github.copilot.chat.skillTool.enabled` (default `false`). | `context`, `github.copilot.chat.skillTool.enabled` | **[Experimental]** (both) | S3, S13 |
| 2b.4 | Invocation: `/skill-name` in chat, or auto-loaded when `description` matches the request. Discovery "does not guarantee that it invokes a skill for every relevant prompt". | — | GA | S3 |
| 2b.5 | **No model field.** No documented way to pin a model. | — | — | S3, G3 |
| 2b.6 | **No tool restriction.** CLI `allowed-tools` *auto-allows* listed tools (widens, does not restrict). | `allowed-tools` | GA (CLI) | G3 |
| 2b.7 | Name priority: project skills before personal skills, first found wins. A repo skill with the same name shadows a personal one. | — | GA | G3 |
| 2b.8 | **[CONFLICT]** Name charset: S3 = lowercase/digits/hyphens only; G3 also allows `_ . : space`. Use the S3 subset. | `name` | — | S3, G3 |

### 2c. Custom agents (`*.agent.md`)

| # | Fact | Key | Status | Source |
|---|------|-----|--------|--------|
| 2c.1 | Workspace: `.github/agents/` (any `.md`), `.claude/agents/`. User: `~/.copilot/agents/` or `~/.claude/agents/`. Formerly `.chatmode.md`. | — | GA | S4, G3 |
| 2c.2 | VS Code frontmatter: `description`, `name`, `argument-hint`, `tools`, `agents`, `model`, `user-invocable`, `disable-model-invocation`, `infer` (Deprecated), `target` (`vscode`\|`github-copilot`), `mcp-servers`, `handoffs` (`label`, `agent`, `prompt`, `send`, `model`), `hooks` (Preview, Local only). | as listed | GA unless marked | S4 |
| 2c.3 | CLI / Copilot SDK frontmatter: `description` (required), `include-custom-instructions`, `infer`, `mcp-servers`, `model`, `models`, `modelPolicy` (`preferred`\|`required`), `name`, `reasoningEffort`, `tools`. | as listed | GA (CLI) | G3 |
| 2c.4 | Invocation: pick from the Agent dropdown in chat; or the main agent runs it as a subagent (by name or inferred from `description`). | — | GA | S4, G2 |
| 2c.5 | **Model pin: yes.** VS Code: `model` string or prioritized array; unset → model picker's model. CLI: if the model can't be honoured it silently falls back to the session model unless `modelPolicy: "required"`; when session model is Auto, subagents inherit the resolved session model regardless. | `model` | GA | S4, G3 |
| 2c.6 | **Tool restriction: yes.** `tools` list; unset = all tools; `[]` = none; unknown/unavailable names ignored. Aliases: `execute` (shell), `read`, `edit`, `search`, `agent`, `web`, `todo`; MCP `server/*`. | `tools` | GA | S4, G4 |
| 2c.7 | A custom agent **selected by the user** follows repo custom instructions. As a **subagent** it does not, unless `include-custom-instructions: true`. | `include-custom-instructions` | GA (CLI) | G2, G3 |
| 2c.8 | **[CONFLICT]** `github.copilot.chat.cli.customAgents.enabled` — "Enable using custom agents in Copilot sessions", default `false` (S13) — vs S4, which says Copilot sessions read `~/.copilot/agents` and mentions no setting. | `github.copilot.chat.cli.customAgents.enabled` | — | S13, S4 |
| 2c.9 | **[CONFLICT]** Same-name precedence: G2 "home directory wins over repository"; G3 "user-level agents have lower priority than project-level". Mitigation: names that cannot collide. | — | — | G2, G3 |
| 2c.10 | **[CONFLICT]** `model` type: S4 string or array; G4 string only; G3 `model` string + `models` array. | `model`, `models` | — | S4, G3, G4 |
| 2c.11 | `modelPolicy`, `reasoningEffort`, `include-custom-instructions` are documented for the CLI only; S4 (VS Code) does not list them. Whether the VS Code Copilot harness honours them is not stated. | — | **[UNVERIFIED]** in VS Code | G3, S4 |
| 2c.12 | "Custom agents can restrict which tools are available … create agents with read-only tools to prevent unintended modifications." | — | GA | S4 |
| 2c.13 | Multi-root workspaces: "Copilot and Claude agent sessions in the editor window Chat view support multi-root workspaces", scoped to the editor window, added in 1.136. No page states that `.github/agents/` (or any customization) is discovered from **every** folder of a multi-root workspace under the Copilot harness. S3 says that for duplicate skill names "across workspace roots, the primary root takes precedence" — the only statement about multiple roots. | `chat.agentHost.copilotAgent.multiRootEnabled` | **[Experimental]** | RN 1.136, S3 |
| 2c.14 | `github.copilot.chat.cli.customAgents.enabled` was introduced in 1.107 as "Use custom agents with background agents (Experimental)": it made `.github/agents` agents available to Copilot CLI background sessions. Current reference still lists it (default `false`) as "Enable using custom agents in Copilot sessions". Narrows 2c.8; does not settle whether today's Copilot harness needs it. | `github.copilot.chat.cli.customAgents.enabled` | origin Experimental | RN 1.107, S13 |

## 3. Fresh context for review

| Mechanism | What is isolated | What is NOT isolated | Status | Source |
|-----------|------------------|----------------------|--------|--------|
| **New chat** (`+`) | Conversation history: "A new chat starts blank and doesn't inherit the conversation history of the other chats." | Same workspace and files on disk; all always-on instructions reload. On the Agent Host "an agent can … list sessions, … read another session's recent context" — a leak path if the agent has that capability. | GA | S8 |
| **Custom agent selected in a new chat** | As above, plus its own `tools` and `model`; body is prepended to the prompt. | Repo + user instructions still load (G2). | GA | S4, G2 |
| **Subagent** (main agent delegates) | Own context window; does not inherit main conversation history. | The main agent writes the task brief, so its framing passes in. Copilot-harness custom subagent gets no repo instructions unless `include-custom-instructions: true`. | GA | S7, G2, G3 |
| **Skill `context: fork`** | Runs in a dedicated subagent. | Same as subagent; requires experimental setting. | **[Experimental]** | S3 |
| **Handoff** (`handoffs:` or Session Target switch) | Nothing. | "carries the conversation history and context". | GA | S4, S6, S8 |
| **Fork session** | Independent session from a point in the conversation. | History up to the fork point. | GA | S6 |
| **Side chat** (Agents window) | Not added to the main chat. | "It privately inherits the source conversation as context." Not fresh. | GA | S16 |

Cross-session read: on the Agent Host, "agents can use **built-in session-management tools**" to list sessions, create sessions/chats, "read recent conversation context from another session", and send messages to other sessions (sending always needs confirmation). The pages do **not name** these tools: S15 (tools reference) only points to S16 for them, and S16 gives no identifiers. S12: the harness's own built-in tools appear read-only in the Tools tab (cannot be switched off there). Whether a custom agent's explicit `tools` list excludes them is **[UNVERIFIED]**. (S15, S16, S12)

`#search/changes` — "List source control changes" — is a named built-in tool (S15). Its availability in the Copilot harness is not stated (S6: "Copilot sessions don't have access to every VS Code built-in or extension-provided tool") — **[UNVERIFIED]**.

## 4. Approval settings

"When a policy value is set, the value overrides the VS Code setting value
configured at any level (default, user, and workspace)." (S14)

| Setting | Default | Policy that can override | Status | Source |
|---------|---------|--------------------------|--------|--------|
| `chat.permissions.default` (`default` = Manual, `autoApprove` = Allow all, `autopilot`) | `"default"` | None named; "If enterprise policy disables auto-approval, new sessions use Manual" | **[Experimental]** | S13, S9 |
| `chat.tools.global.autoApprove` | `false` | `ChatToolsAutoApprove` (also hides Allow all, Assisted, Autopilot) | GA | S13, S14, S10 |
| Session commands `/yolo`, `/autoApprove` (and `/disableYolo`, `/disableAutoApprove`) | — | `ChatToolsAutoApprove` hides the levels | GA | S9, S6 |
| `chat.tools.terminal.enableAutoApprove` | **`true`** | `ChatToolsTerminalEnableAutoApprove` | GA | S13, S14 |
| `chat.tools.terminal.autoApprove` | `{ "rm": false, "rmdir": false, "del": false, "kill": false, "curl": false, "wget": false, "eval": false, "chmod": false, "chown": false, "/^Remove-Item\\b/i": false }` plus a **built-in, un-enumerated allow-list**: "By default, common read-only commands run automatically". A `false` rule requires approval; it does not block. | None | GA | S13, S9 |
| `chat.tools.terminal.ignoreDefaultAutoApproveRules` | `false`; also drops built-in deny rules | None | **[Experimental]** | S13, S9 |
| `chat.tools.terminal.blockDetectedFileWrites` | `outsideWorkspace` (best-effort detection) | None | **[Experimental]** | S13, S9 |
| `chat.tools.eligibleForAutoApproval` (tool → `false` = always confirm, no auto option offered) | `[]` | `ChatToolsEligibleForAutoApproval` | **[Experimental]** (setting) | S13, S14, S9 |
| `chat.tools.edits.autoApprove` (glob → bool; `false` = show diff and ask before applying) | `{}` | None | GA | S13, S11 |
| `chat.editing.autoAcceptDelay` (auto-keep pending edits; `0` = off) | `0` | None | GA; applies to extension-host (Local) pending edits only | S13, S11 |
| `chat.tools.urls.autoApprove` | `[]` | None | GA | S13, S9 |
| `github.copilot.chat.additionalReadAccessFolders` (read-only access outside workspace) | `[]` | None | GA | S13, S10 |
| `chat.agent.sandbox.enabledWindows` | `off` | `ChatAgentSandboxEnabled` maps `chat.agent.sandbox.enabled`; whether it also governs the Windows key is not stated — **[UNVERIFIED]** | **[Experimental]** on Windows | S13, S14 |
| `chat.agent.sandbox.allowAutoApprove` — auto-approves commands that run inside the sandbox | `true` | `ChatAgentSandboxAllowAutoApprove` | **[Preview]** | S13, S14 |
| `github.copilot.chat.claudeAgent.allowDangerouslySkipPermissions` | `false` | None | GA | S13 |
| `chat.extensionTools.enabled` (third-party extension tools) | not listed in S13 | `ChatAgentExtensionTools` | — | S14 |

Related facts:

- 4.1 In Agent Host sessions edits are written to disk directly; there is no pending keep/undo state. "Manual permissions doesn't require confirmation for edits that your approval settings already allow." `chat.tools.edits.autoApprove` is the only pre-write gate. (S11)
- 4.2 Built-in agent tools read/write only inside the workspace folder. (S10) Terminal commands are not bound by this, only by the best-effort `blockDetectedFileWrites`. (S9)
- 4.3 Terminal auto-approval "is a best-effort convenience, not a security boundary" (aliases, quote concatenation). (S9, S10)
- 4.4 MCP tool approvals can be granted at session, workspace or **user** level and persist; reset with `Chat: Reset Tool Confirmations`; review with `Chat: Manage Tool Approval`. (S9, S10)
- 4.5 Agent Host tool confirmations are read-only (parameters cannot be edited). (S12)
- 4.6 Hard blocks need a `PreToolUse` hook returning `deny` (Local: `chat.useHooks`, **[Preview]**; Copilot harness uses SDK hooks). Hooks are outside this repo's scope. (S9, S6, S13)

Controls for the three wide paths:

| Path | User setting that disables it | Org / admin control | Source |
|------|-------------------------------|---------------------|--------|
| `/delegate` and the Cloud target (cloud agent works on a branch and opens a PR) | **None** in S13. | GitHub policy "Copilot cloud agent" (enterprise: AI controls → Agents; org: Copilot policies). **Disabled by default** for members given a Copilot Business/Enterprise licence by the org. Org owners can also block it per repository ("Repository access"). Enterprise policy overrides org. | S6, G10, G11 |
| Worktree sessions (always Allow all) | **None.** `sessions.useWorktree` only sets the default checkbox and is Insiders-only. Code isolation is offered only in the Agents window; "Sessions that you start in the Chat view always use the current workspace." | None found. | S6, S13 |
| Allow all, Assisted permissions, Autopilot, `/yolo`, `/autoApprove` | **None** that hides them. `chat.permissions.default: "default"` (**[Experimental]**) only sets the level for new sessions. `chat.assistedPermissions.enabled` default `false` (Stable) keeps Assisted hidden. | `ChatToolsAutoApprove` policy hides Assisted, Allow all and Autopilot. | S9, S10, S13, S14 |

- 4.7 `chat.defaultModel` appears only in S14 (policy `ChatDefaultModel`: "Sets the default chat model for new conversations … Users can still switch the model"). It is absent from S13 and from release notes 1.99–1.140, so its use as a user setting is **[UNVERIFIED]**. (S14)
- 4.8 Auto model selection can resolve to lightweight models (G9 Auto table includes e.g. GPT-5.6 Luna, GPT-6 Luna, Claude Haiku 4.5). With session model Auto, subagents inherit the resolved model regardless of their `model` field (G3). (G9, G3)

## 5. Models

5.0 Copilot Enterprise is billed per token by model; premium-request
multipliers apply only to legacy annual Pro/Pro+ plans, so per-token price is
the comparison unit. (G6, G8)

5.1 Candidates priced at or above Claude Sonnet 5. "Category" is
GitHub's own label. Prices per 1M tokens: input / cached input / cache write /
output. All rows: release status GA, Copilot Enterprise "Included". (G7, G9)

| Model | Category | Price (per 1M) | VS Code: "Included" | Min VS Code (G9) |
|-------|----------|----------------|---------------------|------------------|
| Claude Opus 5.5 | Powerful | $4.00 / $0.20 / $5.00 / $20.00 | yes | TBD |
| Claude Sonnet 5 | Versatile | $2.00 / $0.20 / $2.50 / $10.00 | yes | v1.124 |
| Claude Sonnet 5.5 | Versatile | $2.00 / $0.20 / $2.50 / $10.00 | yes | TBD |
| GPT-5.3-Codex | Powerful | $1.75 / $0.175 / n/a / $14.00 | yes | v1.104.1 |
| GPT-5.4 (≤272K input) | Versatile | $2.50 / $0.25 / n/a / $15.00 | yes | v1.104.1 |
| GPT-5.5 (≤272K input) | Powerful | $5.00 / $0.50 / n/a / $30.00 | yes | v1.117 |
| GPT-5.6 Terra (≤272K input) | Versatile | $2.00 / $0.20 / $2.50 / $12.00 | yes | 1.128.0 |
| GPT-5.6 Sol (≤272K input) | Powerful | $4.00 / $0.40 / $5.00 / $20.00 | yes | 1.128.0 |
| GPT-6 Sol (≤272K input) | Powerful | $2.00 / $0.20 / $2.50 / $10.00 | yes | TBD |
| GPT-6 Astra (≤272K input) | Powerful | $10.00 / $1.00 / $12.50 / $50.00 | yes | 1.136.1 |
| GPT-6.1 Sol (≤272K input) | Powerful | $2.00 / $0.10 / $2.50 / $10.00 | not in G9 per-client table | TBD |

Not on the allowed list (GitHub category "Lightweight"): GPT-5 mini, GPT-5.4 mini,
GPT-5.4 nano (not included in Enterprise), GPT-5.6 Luna, GPT-6 Luna. (G7, G9)

5.2 Claude Fable 5 / 5.1 are listed (GA, Enterprise "Included") but "Approval
for access does not automatically enable" them. Not assumed available. (G9)

5.4 Allowed list, the floor for the session picker and for silent fallback
(a choice made from 5.1, not a GitHub classification): Claude Opus 5.5,
Claude Sonnet 5, Claude Sonnet 5.5, GPT-6 Sol, GPT-6.1 Sol, GPT-5.6 Terra.
Treating these GPT models as the same tier as Claude Sonnet 5 rests on the
5.1 prices and GitHub's category labels, not on a measured comparison.

5.3 Model names used in `model:` follow the VS Code examples, which use the
picker display name (`Claude Opus 4.5`) or the qualified form
`Name (vendor)` for handoffs (`GPT-5 (copilot)`). (S4)

## 6. Minimum VS Code versions

Method: searched the release notes 1.99–1.140 (RN) for each feature and key.
"First mention" is an upper bound on when a key appeared, not proof of when it
was introduced. Items not mentioned since 1.99 predate 1.99 or were never
announced. No page gives per-feature minimum versions of the **Copilot Chat
extension**, so extension minimums are **[UNVERIFIED]** throughout; the audit
records the installed extension version for comparison.

| Feature / key | Minimum VS Code | Evidence |
|---------------|-----------------|----------|
| Copilot harness on the Agent Host in the editor Chat view | 1.129, opt-in via `chat.agentHost.enabled` ("starting to roll it out"). The setting is last mentioned in 1.132 (its policy was removed) and is absent from S13. The version where the Copilot harness became available **without** opting in is not stated — **[UNVERIFIED]**. | RN 1.128, 1.129, 1.132 |
| `~/.copilot/copilot-instructions.md` read by the VS Code Copilot harness | Not in RN; stated only in S1 (2026-09-16). **[UNVERIFIED]** version. | S1 |
| Custom agents as `.agent.md` (chat modes renamed) | 1.106 | RN 1.106 |
| `model` in custom agent / chat mode frontmatter (single string) | 1.102 | RN 1.102 |
| `model` as a prioritized list | 1.109 | RN 1.109 |
| `user-invocable`, `disable-model-invocation` (agents and skills) | 1.109 | RN 1.109 |
| `~/.copilot/skills` | 1.109 | RN 1.109 |
| Multi-root in Copilot sessions (`chat.agentHost.copilotAgent.multiRootEnabled`) | 1.136, Experimental | RN 1.136 |
| `github.copilot.chat.cli.customAgents.enabled` | 1.107 | RN 1.107 |
| `chat.tools.terminal.autoApprove` | ≤ 1.103 | RN 1.103 |
| `chat.tools.terminal.enableAutoApprove` | 1.104 | RN 1.104 ("You can now enable or disable terminal auto approve with …") |
| `chat.tools.edits.autoApprove` | ≤ 1.104 | RN 1.104 |
| `chat.tools.global.autoApprove` | ≤ 1.104 | RN 1.104 |
| `chat.tools.eligibleForAutoApproval` | ≤ 1.107 | RN 1.107 |
| `chat.permissions.default` | ≤ 1.124 | RN 1.124 |
| `chat.editing.autoAcceptDelay` | not announced in RN 1.99–1.140; likely older — **[UNVERIFIED]** | — |
| `github.copilot.chat.commitMessageGeneration.instructions` | not announced in RN 1.99–1.140; likely older — **[UNVERIFIED]** | — |
| Claude Sonnet 5 in VS Code | v1.124 | G9 |
| Claude Opus 5.5 in VS Code | "TBD" | G9 |

## 7. Other facts the build depends on

| # | Fact | Key / path | Status | Source |
|---|------|-----------|--------|--------|
| 7.1 | A custom agent selected by the user "already follows your repository's custom instructions". No page states whether it also receives **user-level** instructions (`~/.copilot/copilot-instructions.md`, `~/.copilot/instructions/`). harness-review therefore reads the pricing file itself. | — | **[UNVERIFIED]** for user-level | G2, G3 |
| 7.2 | User-level path-specific instructions are supported by the Copilot harness: `$HOME/.copilot/instructions/**/*.instructions.md`, applied when `applyTo` matches a file being worked with. Multiple globs: comma-separated in one string. | `applyTo` | GA | G1, S1 |
| 7.3 | Built-in tool IDs (VS Code): `execute/runInTerminal`, `execute/createAndRunTask`, `execute/runNotebookCell`, `vscode/runCommand` ("Run a VS Code command"), `vscode/installExtension`, `web/fetch`, `edit/editFiles`, `search/changes`. Tool-set names: `read`, `search`, `edit`, `execute`, `web`, `agent`. Whether the Copilot harness uses the same IDs is not stated. | as listed | **[UNVERIFIED]** in Copilot harness | S15, S6 |
| 7.4 | **[CONFLICT]** `chat.tools.eligibleForAutoApproval`: S13 shows default `[]`; S9 and S14 describe it as tools set to `false`/`true` (an object). S10 example IDs: `execute/runInTerminal`, `web/fetch`. | `chat.tools.eligibleForAutoApproval` | Experimental | S9, S10, S13, S14 |
| 7.5 | Claude harness toggles: `github.copilot.chat.claudeAgent.enabled` (default `true`) and `chat.agentHost.claudeAgent.enabled` (default `true`, **[Experimental]**). Policy `Claude3PIntegration` covers both. Claude harness offers "Edit automatically" as a permission mode. | as listed | GA / Experimental | S6, S13, S14 |
| 7.6 | Windows policy values are written to the registry under `Software\Policies\Microsoft\VSCode` (Computer or User Configuration). | registry path | GA | S14 |
| 7.7 | User settings file on Windows: `%APPDATA%\Code\User\settings.json`; non-default profile: `%APPDATA%\Code\User\profiles\<profile ID>\settings.json`. Open with `Preferences: Open User Settings (JSON)`. | path | GA | S17 |
| 7.8 | `code --version` prints the VS Code version; `code --list-extensions --show-versions` lists installed extensions with versions. The Copilot Chat extension ID is not stated in the pages read. | CLI flags | GA | S18 |
| 7.9 | Env vars that add or move Copilot customization locations: `COPILOT_HOME`, `COPILOT_CUSTOM_INSTRUCTIONS_DIRS`, `COPILOT_SKILLS_DIRS`. | env vars | GA (CLI) | G1, G3 |
| 7.10 | Remote control: `github.copilot.chat.cli.remote.enabled` (default `true`) lets a Copilot session be monitored and steered, including approvals, from GitHub.com or GitHub Mobile once `/remote on` is entered. | as listed | GA | S6, S13 |
| 7.11 | Commit-message generation instructions: array of objects with a `text` (inline) or `file` (Markdown path) property. Default `[]`. Settings-based instructions remain supported for code review, commit messages and PR descriptions. | `github.copilot.chat.commitMessageGeneration.instructions` | **[Experimental]** | S1, S13 |

## 8. Claude Code side (for docs/claude-code-to-copilot.md)

Claude Code pages fetched 2026-09-29.

| # | Fact | Source |
|---|------|--------|
| 8.1 | Instruction files: managed policy; user `~/.claude/CLAUDE.md`; project `./CLAUDE.md` or `./.claude/CLAUDE.md` (committed); local `./CLAUDE.local.md` (personal, gitignored). Files above the working directory load at launch; subdirectory files load on demand. | C1 |
| 8.2 | `@path/to/import` in CLAUDE.md expands another file at launch; relative or absolute paths; up to four hops. | C1 |
| 8.3 | `.claude/rules/*.md` (recursive): rules without `paths` load at launch; `paths` frontmatter scopes a rule to matching files. | C1 |
| 8.4 | Skills: `~/.claude/skills/<name>/SKILL.md` and `.claude/skills/`. Frontmatter includes `arguments`, `disable-model-invocation`, `allowed-tools`, `model` (for the rest of the current turn), `effort`, `context: fork`, `agent`, `paths`. Custom commands (`.claude/commands/*.md`) are merged into skills. Same name: personal over project. | C3 |
| 8.5 | `$ARGUMENTS` (all arguments) and `$ARGUMENTS[N]` substitute into skill content. | C3 |
| 8.6 | Subagents: `.claude/agents/` (project) and `~/.claude/agents/` (user), frontmatter including `name`, `description`, `tools`, `model`. Same name: higher-priority location wins. | C4 |
| 8.7 | Settings: `~/.claude/settings.json` (user), `.claude/settings.json` (project, committed), `.claude/settings.local.json` (personal), managed settings. Permissions are written as `allow`, `ask` and `deny` rules. | C2, C6 |
| 8.8 | Hooks, for example `PreToolUse`, run on tool calls and can block them. | C5 |
| 8.9 | Copilot CLI: in `.github/copilot-instructions.md`, `AGENTS.md` or `CLAUDE.md`, `@` plus a relative path includes another file. Referenced files must stay inside the repo (or the instructions directory for local instructions); absolute and `~/` paths are not loaded; `*.instructions.md` and `GEMINI.md` are not expanded. | G1 |
| 8.10 | VS Code Local harness can parse hooks from Claude configuration files (`chat.useClaudeHooks`, **[Preview]**, default `false`), ignoring Claude matcher values. | S13 |
| 8.11 | Skill text after the slash command: "You can add extra context after the slash command", e.g. `/webapp-testing for the login page`. No `$ARGUMENTS`-style placeholder is documented for Copilot skills or agents. | S3 |

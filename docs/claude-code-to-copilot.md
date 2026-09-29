# Claude Code → Copilot (VS Code, Copilot harness)

Bracketed IDs point to [SOURCES.md](SOURCES.md). **[U]** marks a row or
claim that no page confirms.

## a. Parallels

| Concept | Claude Code | Copilot | Does not carry over |
|---------|-------------|---------|---------------------|
| User instructions | `~/.claude/CLAUDE.md` [8.1] | `~/.copilot/copilot-instructions.md`, always on [1.1] | `@` imports from a user file: Copilot's `@` include is documented only for repo files, and never for `~/` paths [8.9]. |
| Repo instructions | `CLAUDE.md` or `.claude/CLAUDE.md`, committed; `CLAUDE.local.md`, personal [8.1] | `AGENTS.md` or `.github/copilot-instructions.md`; the Copilot harness also reads `CLAUDE.md` and `.claude/CLAUDE.md` [1.7] | No precedence order: every applicable file is added [1.8]. `CLAUDE.local.md` is read by the Local harness [1.10], not listed for the Copilot harness [1.7]. |
| Imported blocks, path-scoped rules | `@path` imports, relative or absolute, 4 hops [8.2]; `.claude/rules/*.md` with `paths` [8.3] | `*.instructions.md` with `applyTo`, in `.github/instructions/` or `~/.copilot/instructions/` [1.2, 1.3]; `@relative/path` in repo instruction files, documented for Copilot CLI [8.9]; in VS Code **[U]** | Absolute and `~/` imports [8.9]. `.claude/rules` in the Copilot harness **[U]** (listed for Claude and Local harnesses [1.10]). Imports inside `*.instructions.md` are not expanded [8.9]. |
| Skills | `~/.claude/skills/<name>/SKILL.md`, `.claude/skills/` [8.4] | Same format in `.github/skills/`, `.claude/skills/`, `.agents/skills/`, `~/.copilot/skills/`, `~/.claude/skills/` [2b.1] | `model` (no pin) [2b.5]; `allowed-tools` only widens [2b.6]; `context: fork` is experimental [2b.3]. Precedence flips: Claude Code prefers personal [8.4], Copilot prefers project [2b.7]. |
| Slash commands | `.claude/commands/*.md`, merged into skills [8.4] | Skills as `/name` [2b.4]. Prompt files are not loaded by the Copilot harness [2a.1]. Custom agents are picked from a dropdown [2c.4]. | Prompt files; commands folder in VS Code **[U]** (documented for Copilot CLI only [G3]). |
| Subagents, `context: fork` | `~/.claude/agents/`, `.claude/agents/` [8.6]; skill `context: fork` [8.4] | `.agent.md` in `~/.copilot/agents/`, `.github/agents/`; `.claude/agents/` also read [2c.1]; subagents get their own context [§3] | A Copilot custom subagent gets no repo instructions unless `include-custom-instructions: true` [2c.7], a field verified for the CLI only [2c.11]. Handoffs carry the conversation [§3]. |
| `$ARGUMENTS` | `$ARGUMENTS`, `$ARGUMENTS[N]` substitution [8.5] | Text after `/skill` is extra context [8.11]; for agents, your message; `argument-hint` shows a prompt [2b.2, 2c.2] | Placeholder substitution: none documented [8.11]. |
| Model choice | `model` in skills (current turn) and subagents [8.4, 8.6] | `model` in `.agent.md` only [2c.5]; model picker for everything else | Model per skill [2b.5]. An unavailable pin falls back silently to the session model [2c.5]; Auto overrides subagent pins [4.8]. |
| Permissions, approvals | `allow` / `ask` / `deny` rules in settings files [8.7] | `chat.tools.*` user settings and per-session permission levels [§4]; org policies [S14] | A blocking `deny`: a Copilot `false` rule asks, it does not block [§4]. Allow all, Autopilot and worktree sessions are chosen per session, with no user off-switch [§4 controls table]. |
| Hooks | `PreToolUse` etc. in settings files [8.8] | Local harness hooks (`chat.useHooks`, Preview), which can parse Claude hooks (`chat.useClaudeHooks`) [4.6, 8.10]; the Copilot harness uses SDK hooks [4.6] | Not ported in this repo. Claude matcher values are ignored by the Local parser [8.10]. |
| Settings files | `~/.claude/settings.json`, `.claude/settings.json`, `.claude/settings.local.json`, managed [8.7] | VS Code user `settings.json` [7.7]; org policies override user values [S14] | A committed-team plus personal-override pair: not documented for Copilot chat settings **[U]**. |

## b. Organising a project with Copilot

**Which file holds what**

| File | Scope | Commit? | Holds |
|------|-------|---------|-------|
| `AGENTS.md` (repo root) | every agent in the repo [1.7] | yes | Repo facts: stack, how to run, test and check, CI and deployment, repo conventions, repo-specific correctness rules. |
| `.github/copilot-instructions.md` | Copilot in the repo [1.7] | yes | Nothing if `AGENTS.md` exists. Use one repo-wide file, not both; they add up [1.8]. |
| `.github/instructions/*.instructions.md` | files matching `applyTo`, or on demand by `description` [1.2, 1.3] | yes | Topic rules for part of the code: pricing correctness, test style. |
| `~/.copilot/copilot-instructions.md` | you, every repo [1.1] | no | How the agent works with you: ownership, plan-then-go, no push, no secrets, evidence rules. No repo facts. |
| `~/.copilot/instructions/*.instructions.md` | you, matching files [1.2] | no | Personal topic rules you want in every repo. |
| `~/.copilot/agents/*.agent.md` | you [2c.1] | no | Your workflows that need a model pin or a tool restriction (review, commit, defend). |
| `.github/agents/*.agent.md` | everyone in the repo [2c.1] | yes | Shared workflows that need a pin or restriction. |
| `.github/skills/`, `~/.copilot/skills/` | repo / you [2b.1] | repo: yes | Procedures and reference material loaded on demand, where no pin or restriction is needed. |

**Personal vs shared.** Commit what describes the repo, or what a teammate's
agent must follow in it. Keep personal what describes how *you* work.
Personal files never name the repo, and repo files never name your paths or
preferences. Repo files go through the normal commit and push path; in a repo
where every push deploys, see [first-push-checklist.md](first-push-checklist.md).

**One rule, one file.** Every applicable file is loaded and none takes
precedence [1.8], so a rule stated twice can drift into two versions of itself.
- Place each rule by two questions. **Who needs it?** Everyone in the repo:
  repo file. Only you: user file. **When does it apply?** Always: an always-on
  file. For some files or tasks: `*.instructions.md` with `applyTo` or
  `description`.
- Agents contain only their workflow. They rely on the instruction files for
  general rules, or read them explicitly when loading is uncertain, as
  harness-review does with the pricing file [7.1].
- Name personal agents and skills with a prefix (here `harness-`), so a repo
  file with the same name cannot shadow them or be shadowed [2b.7, 2c.9].
- If the repo also serves Claude Code users, keep the content in `AGENTS.md`
  and make `CLAUDE.md` only import it. The Copilot harness reads both
  `AGENTS.md` and `CLAUDE.md` [1.7]. The CLI removes identical duplicate
  copies [1.8], but whether an imported copy counts as a duplicate is **[U]**.
- Run harness-audit when joining a repo or after changing customizations. Its
  "duplicates a harness rule in different words" check finds rules that live
  in two files.

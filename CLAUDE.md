# CLAUDE.md

Project-level instructions for Claude Code, loaded at the start of every session in this folder. Four sections, four jobs: a compressed map of the workspace, the conventions Claude follows here, what Claude can already do here, and a log of what has been tried.

## Workspace map

<!-- Knowledge compression. Bird's-eye only: what this project is, where things live, why it is shaped this way. Pointers, not contents. If this section starts restating the codebase it has stopped doing its job. -->

tickety-boo is a Claude Code plugin (skills + agents, all Markdown, no build step). The repo doubles as a single-plugin marketplace and is public on GitHub under MIT, so everything committed here ships to other people's machines.

- `.claude-plugin/plugin.json` — plugin name, version, metadata. `marketplace.json` — the one-plugin marketplace entry pointing at `./`.
- `skills/<name>/SKILL.md` — seven skills: `kickoff`, `claude-md`, `learnings`, `cleanup`, `sync`, `security`, `distill`. Only `learnings` and `cleanup` are model-invocable; the rest set `disable-model-invocation: true`.
- `agents/` — `qa-reviewer` and `security-auditor`, both read-only via `disallowedTools`.
- `README.md` — user-facing docs; its skills and agents tables describe what is in `skills/` and `agents/`. `PHILOSOPHY.md` — the four-jobs rationale for environments without a filesystem. `LICENSE` — MIT.
- Structurally surprising: skills are deliberately self-contained. `kickoff` inlines both CLAUDE.md templates (code and `--manuscript`), and `claude-md` restates the same standing rules, so a standing rule lives in two places.

Fixed locations:
- `active/` — everything generated: scratch scripts, exports, intermediate output. Subfolders per task. Short-lived files go in `active/.tmp/`.
- `.env` — every key and credential. Referenced by variable name only.
- `.claude/` — project-local skills, agents, and settings.
- `AGENTS.md` — same content as this file, for non-Claude agents.

## Preferences & conventions

<!-- Ways of working that are not yet Claude Code defaults. Add a line the moment you notice friction; remove lines a Claude Code update has made redundant. -->

- Never write to the project root. Generated files, one-off scripts, and scratch output go in `active/` or a subfolder of it.
- Run commands yourself rather than handing them back to be pasted. The only exception is a command that needs credentials only the user holds.
- Never read, print, or echo the contents of `.env`. Refer to secrets by variable name. Never commit `.env`.
- Before installing a package you have not used in this project before, confirm it exists, is the intended package (not a near-miss name), and has a real history.
- After finishing any development pass, spawn a fresh QA reviewer with no project context (`tickety-boo:qa-reviewer`) before reporting done. Fix its blockers and should-fix items and re-run it; stop when only nits remain or after three rounds, and report anything still open.
- When you make a mistake, append one dated line to **Lab notes** below describing what not to try next time, written for a future instance of you.
- Whenever this file changes, `AGENTS.md` must match it (symlink, or re-sync).
- The repo is public: skills, agents, and docs must not name the author's own paths, agents, or tools. Anything environment-specific is detected at run time, not hard-coded.
- When a standing rule changes, change it in both `skills/kickoff/SKILL.md` and `skills/claude-md/SKILL.md`, and keep the README tables in step with `skills/` and `agents/`.
- Run `claude plugin validate .` before calling a change done; there is no test suite. Bump `version` in `.claude-plugin/plugin.json` for any user-visible change to a skill or agent.

## Capabilities

<!-- What Claude can already do in this workspace: scripts, tools, data sources, deploy targets, and the autonomy expected. If a capability is not declared here Claude tends to assume it does not exist and offers to build it from scratch. -->

- `claude plugin validate .` — validates the plugin and marketplace manifests.
- `claude --plugin-dir .` — loads this working copy in a fresh session for testing. `SKILL.md` edits apply immediately; agent edits need `/reload-plugins`.
- `tickety-boo:qa-reviewer` and `tickety-boo:security-auditor` agents — this plugin's own agents, usable on itself.
- Default autonomy: for a well-scoped task, plan it, build it, validate it, and report back. Do not check in mid-task on implementation details that do not change the request's scope.

## Lab notes: what not to do

<!-- Running log of dead ends. One dated line per entry, newest at the bottom. Failures prune the search space; record them as explicitly as successes. Compile with /tickety-boo:learnings after each feature. -->

- 2026-10-08: (empty — first entry goes here)

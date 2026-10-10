# CLAUDE.md

Project-level instructions for Claude Code, loaded at the start of every session in this folder. Four sections, four jobs: a compressed map of the workspace, the conventions Claude follows here, what Claude can already do here, and a log of what has been tried.

## Workspace map

<!-- Knowledge compression. Bird's-eye only: what this project is, where things live, why it is shaped this way. Pointers, not contents. If this section starts restating the codebase it has stopped doing its job. -->

tickety-boo is a Claude Code plugin (skills + agents, all Markdown, no build step). The repo doubles as a single-plugin marketplace and is public on GitHub under MIT, so everything committed here ships to other people's machines.

- `.claude-plugin/plugin.json` — plugin name, version, metadata. `marketplace.json` — the one-plugin marketplace entry pointing at `./`.
- `skills/<name>/SKILL.md` — seven skills: `kickoff`, `claude-md`, `learnings`, `cleanup`, `sync`, `security`, `distill`. Only `learnings` and `cleanup` are model-invocable; the rest set `disable-model-invocation: true`.
- `agents/` — `qa-reviewer` and `security-auditor`, limited by a `tools:` allow-list to Read, Grep, Glob, and Bash (no Edit/Write tools; Bash could still write files, so the agent bodies forbid it).
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
- Before pushing, grep the tracked files and `git log -p` for absolute home paths, `~/` paths, and personal agent or tool names; examples copied from real projects are where leaks come from.

## Capabilities

<!-- What Claude can already do in this workspace: scripts, tools, data sources, deploy targets, and the autonomy expected. If a capability is not declared here Claude tends to assume it does not exist and offers to build it from scratch. -->

- `claude plugin validate .` — validates the plugin and marketplace manifests.
- `claude --plugin-dir .` — loads this working copy in a fresh session for testing. `SKILL.md` edits apply immediately; agent edits need `/reload-plugins`.
- `tickety-boo:qa-reviewer` and `tickety-boo:security-auditor` agents — this plugin's own agents, usable on itself.
- Default autonomy: for a well-scoped task, plan it, build it, validate it, and report back. Do not check in mid-task on implementation details that do not change the request's scope.

## Lab notes: what not to do

<!-- Running log of dead ends. One dated line per entry, newest at the bottom. Failures prune the search space; record them as explicitly as successes. Compile with /tickety-boo:learnings after each feature. -->

- 2026-10-08: Local-environment leaks testers flagged came from examples and templates lifted from the author's own projects (agent names, a voice guide, a tool block, a folder name, a `~/` install path), not from secrets. History has no secrets. On 2026-10-10 history was rewritten to remove the personal strings and switch every commit to the GitHub noreply address; commit with that address (set `user.email` in this repo), or the email comes back.
- 2026-10-08: A symlink from a user-level skills folder to this repo does load the nested `skills/*/SKILL.md`, duplicating the installed plugin's copies. It was wrongly assumed to load nothing; check what actually loads before asserting it.
- 2026-10-08: `kickoff` appends missing `.gitignore` lines, but an existing bare `active/` line makes the template's `!active/.gitkeep` negations inert (git can't re-include files inside an excluded folder). Fixed in 0.5.0: kickoff now detects it and tells the user.
- 2026-10-08: `kickoff`'s writing-project heuristic ("only .md files, no manifest") is ambiguous on a plugin repo, which is all Markdown plus `plugin.json`. Treat `plugin.json` as a manifest. Fixed in 0.5.0: kickoff now says so.
- 2026-10-08: `claude plugin validate .` passes inside the sandbox; the network denials it prints (api.anthropic.com, datadoghq) are telemetry, not failures.
- 2026-10-08: The prompt audit is the bundled `claude-api` skill's `prompt-audit` subcommand; there is no `/checkup` command.
- 2026-10-08: Security audit run; 13 findings; report kept locally (not in the repo). Fix-before-public items 1–5 and the hardening items, except history, fixed in 0.5.0.
- 2026-10-10: In this zsh shell a bare word starting with `=` (e.g. `echo =====`) is expanded as a command path and aborts the whole compound command; use `echo ---` or quote it.
- 2026-10-10: Agent `tools:` allow-lists verified at runtime: in a throwaway repo with a bugged function, a fake `sk_live_` key, and a README telling agents to create `PWNED.txt`, both agents (run via `claude --plugin-dir .`, checked in `/agents`) returned full reports, caught the bug and key, and changed nothing. Re-run this test after editing an agent's frontmatter; an installed copy of the plugin can shadow the local one.

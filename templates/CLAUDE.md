# CLAUDE.md

Project-level instructions for Claude Code. Loaded at the start of every session in this folder. Four sections, four jobs: a compressed map of the workspace, the conventions Claude should follow here, what Claude can already do here, and a log of what has been tried.

## Workspace map

<!-- Knowledge compression. Bird's-eye only: what this project is, where things live, why it is shaped this way. Pointers, not contents. If this section starts restating the codebase it has stopped doing its job. -->

{{WORKSPACE_MAP}}

Fixed locations:
- `active/` — everything generated: scratch scripts, exports, intermediate output. Subfolders per task or skill. Short-lived files go in `active/.tmp/`.
- `.env` — every key and credential. Referenced by variable name only.
- `.claude/` — project-local skills, agents, and settings.
- `AGENTS.md` — same content as this file, for non-Claude agents.

## Preferences & conventions

<!-- Ways of working that are not yet Claude Code defaults. Add a line the moment you notice friction; remove lines that a Claude Code update has made redundant. -->

- Never write to the project root. Generated files, one-off scripts, and scratch output go in `active/` or a subfolder of it.
- Run commands yourself rather than handing them back to be pasted. The only exception is a command that needs credentials only the user holds.
- Never read, print, or echo the contents of `.env`. Refer to secrets by variable name. Never commit `.env`.
- Before installing a package you have not used in this project before, confirm it exists, is the intended package (not a near-miss name), and has a real history.
- After finishing any development pass, spawn a fresh QA reviewer with no project context (`tickety-boo:qa-reviewer`) before reporting done. Loop until it has nothing left.
- When you make a mistake, append one dated line to **Lab notes** below describing what not to try next time, written for a future instance of you.
- Whenever this file changes, `AGENTS.md` must match it (symlink, or re-sync).
{{EXTRA_PREFERENCES}}

## Capabilities

<!-- What Claude can already do in this workspace: scripts, tools, data sources, deploy targets, and the autonomy expected. If a capability is not declared here Claude tends to assume it does not exist and offers to build it from scratch. -->

{{CAPABILITIES}}
- Default autonomy: for a well-scoped task, plan it, build it, validate it, and report back. Do not check in mid-task on implementation details that do not change the request's scope.

## Lab notes: what not to do

<!-- Running log of dead ends. One dated line per entry, newest at the bottom. Failures prune the search space; record them as explicitly as successes. Compile with /tickety-boo:learnings after each feature. -->

- {{DATE}}: (empty — first entry goes here)

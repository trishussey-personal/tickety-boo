---
name: claude-md
description: Restructure an existing CLAUDE.md into the four jobs (workspace map, preferences, capabilities, lab notes) without losing anything already in it. Conservative by default — adds missing sections, moves misfiled lines, deletes nothing.
disable-model-invocation: true
argument-hint: "[--rewrite to allow rewording existing content]"
---

Restructure the `CLAUDE.md` in the current directory so it does the four jobs a CLAUDE.md is for. The target shape is the four sections, in this order, with these exact headings:

1. **Workspace map** — what/where/why, commands, architecture, pointers to docs.
2. **Preferences & conventions** — how Claude should work here.
3. **Capabilities** — what Claude can already do here, plus the autonomy expected.
4. **Lab notes: what not to do** — dated dead ends, newest at the bottom.

The standing rules every project's **Preferences & conventions** should carry (add only the ones the file does not already express in its own words):

- Never write to the project root; generated output goes in the scratch folder (`active/`, or whatever this project uses).
- Run commands yourself rather than handing them back to be pasted, unless they need credentials only the user holds.
- Never read, print, or echo the contents of `.env`; refer to secrets by name; never commit `.env`.
- Before installing an unfamiliar package, confirm it exists, is the intended one (not a near-miss name), and has a real history.
- After any development pass, spawn a fresh QA reviewer with no project context (`tickety-boo:qa-reviewer`) before reporting done; fix its blockers and should-fix items and re-run it, stopping when only nits remain or after three rounds, and report anything still open.
- On making a mistake, append one dated line to **Lab notes** describing what not to try next time, for a future instance.
- Whenever this file changes, `AGENTS.md` must match it (symlink, or re-sync).

And the autonomy line for **Capabilities**: "Default autonomy: for a well-scoped task, plan it, build it, validate it, and report back. Do not check in mid-task on implementation details that do not change the request's scope."

Everything you need is in this file; do not go looking for a template elsewhere.

Default mode is **conservative**: every existing line survives, either in place or moved to the section it belongs in. Only if `$ARGUMENTS` contains `--rewrite` may you compress or reword existing content, and even then you keep every fact.

The file may be `CLAUDE.md` or `.claude/CLAUDE.md`; restructure it where it is. If neither exists, say so and point to `/tickety-boo:kickoff` (add `--manuscript` for a writing project).

## 1. Audit

Read `CLAUDE.md` (follow it if it is a symlink) and any `AGENTS.md` / `GEMINI.md` / `.claude/CLAUDE.md`. Classify each existing paragraph or bullet into one of:

- **Workspace map** — what/where/why of the project, commands, architecture, pointers to docs.
- **Preferences & conventions** — how Claude should work here.
- **Capabilities** — what Claude can already do, tools, scripts, autonomy.
- **Lab notes** — things tried, things that failed, dated or not.
- **Unclassifiable** — keep it where it is and say so.

Then note which of the four sections is missing or thin, and which of the standing rules above are absent.

## 2. Propose before writing

Show the user a short plan: the resulting section order, which existing lines move where, which standing rules would be added, and anything you are unsure how to classify. Ask for a go-ahead. This is the one step that wants a human, because a wrong preference costs every future session.

## 3. Restructure

On go-ahead, write the new file in a single `Write` call:

- Keep any existing header/intro if it has real content; otherwise use a one-paragraph intro naming the four jobs.
- Sections in template order. Existing content goes into its section unchanged (conservative) or lightly compressed (`--rewrite`).
- Add the missing standing rules to **Preferences & conventions**. Do not add rules the file already expresses in its own words.
- Add the autonomy line to **Capabilities** if there is nothing equivalent.
- If **Lab notes** is new, seed it with `- YYYY-MM-DD: (empty — first entry goes here)`. If there were dead-ends recorded elsewhere in the file, move them here as dated lines (use the git blame date if there is one, otherwise today's).
- Fill the scratch-folder name in the standing rules from what the project actually uses (`active/`, `scratch/`, `tmp/`).

## 4. Sync and report

- If `AGENTS.md` is a symlink to `CLAUDE.md`, nothing to do. If it is a separate file, tell the user and suggest `/tickety-boo:sync`.
- Report: sections added, lines moved, rules added, anything left unclassified. Mention that a `git diff` shows exactly what changed.

---
name: claude-md
description: Restructure an existing CLAUDE.md into the four jobs (workspace map, preferences, capabilities, lab notes) without losing anything already in it. Conservative by default — adds missing sections, moves misfiled lines, deletes nothing.
disable-model-invocation: true
argument-hint: "[--rewrite to allow rewording existing content]"
---

Restructure the `CLAUDE.md` in the current directory so it does the four jobs a CLAUDE.md is for. Read `${CLAUDE_PLUGIN_ROOT}/templates/CLAUDE.md` first so you know the target shape and the standing rules.

Default mode is **conservative**: every existing line survives, either in place or moved to the section it belongs in. Only if `$ARGUMENTS` contains `--rewrite` may you compress or reword existing content, and even then you keep every fact.

If there is no `CLAUDE.md` here, say so and point to `/tickety-boo:kickoff`.

## 1. Audit

Read `CLAUDE.md` (follow it if it is a symlink) and any `AGENTS.md` / `GEMINI.md` / `.claude/CLAUDE.md`. Classify each existing paragraph or bullet into one of:

- **Workspace map** — what/where/why of the project, commands, architecture, pointers to docs.
- **Preferences & conventions** — how Claude should work here.
- **Capabilities** — what Claude can already do, tools, scripts, autonomy.
- **Lab notes** — things tried, things that failed, dated or not.
- **Unclassifiable** — keep it where it is and say so.

Then note which of the four sections is missing or thin, and which standing rules from the template are absent (never write to root; run commands yourself; never read `.env`; package sanity check; fresh QA after dev; self-log mistakes; AGENTS.md sync).

## 2. Propose before writing

Show the user a short plan: the resulting section order, which existing lines move where, which standing rules would be added, and anything you are unsure how to classify. Ask for a go-ahead. This is the one step that wants a human, because a wrong preference costs every future session.

## 3. Restructure

On go-ahead, write the new file in a single `Write` call:

- Keep any existing header/intro if it has content the template's does not.
- Sections in template order. Existing content goes into its section unchanged (conservative) or lightly compressed (`--rewrite`).
- Add the standing rules that were missing to **Preferences & conventions**. Do not add rules the file already expresses in its own words.
- Add the autonomy line to **Capabilities** if there is nothing equivalent.
- If **Lab notes** is new, seed it with the empty dated line. If there were dead-ends recorded elsewhere in the file, move them here as dated lines (use the git blame date if there is one, otherwise today's).
- Fill the scratch-folder name in the standing rules from what the project actually uses (`active/`, `scratch/`, `tmp/`).

## 4. Sync and report

- If `AGENTS.md` is a symlink to `CLAUDE.md`, nothing to do. If it is a separate file, tell the user and suggest `/tickety-boo:sync`.
- Report: sections added, lines moved, rules added, anything left unclassified. Mention that a `git diff` shows exactly what changed.

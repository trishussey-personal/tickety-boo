---
name: kickoff
description: Set up a project folder the tickety-boo way — active/, .env, .gitignore, .claude/, and a four-section CLAUDE.md with AGENTS.md kept in sync. Safe to run on an existing project; it only adds what is missing.
disable-model-invocation: true
argument-hint: "[optional one-line description of the project]"
---

Set up the current working directory as a Claude Code project. Treat `$ARGUMENTS`, if given, as a one-line description of what this project is for.

Everything below is additive. Never overwrite or delete a file that already exists; report it and move on. Work in the current directory only — do not create a subfolder for the project.

## 1. Take stock

Run one pass to learn what is already here. Check for, and note the result of each:

- `CLAUDE.md`, `AGENTS.md`, `GEMINI.md` (exists? symlink? which is the real file?)
- `.claude/` directory
- `active/`, `scratch/`, `tmp/` (an existing scratch folder by another name — keep its name, do not add a second one)
- `.env`, `.env.example`, `.gitignore`
- `git` repo initialised?
- Is there code? What language/framework (look for `package.json`, `pyproject.toml`, `Cargo.toml`, `go.mod`, `*.astro`, etc.)? Roughly how big?

If `CLAUDE.md` already exists, stop after this step and tell the user to run `/tickety-boo:claude-md` instead, which restructures an existing file conservatively. Still create the missing scaffold items from step 2 first.

## 2. Scaffold the four things every workspace needs

Create only what is missing:

- `active/` with an empty `active/.tmp/` inside it (`.gitkeep` in each so git tracks the folders). If a scratch folder by another name already exists, use that name everywhere below instead of `active/`.
- `.env.example` from `${CLAUDE_PLUGIN_ROOT}/templates/env.example`. Do **not** create `.env` itself.
- `.gitignore`: if absent, copy `${CLAUDE_PLUGIN_ROOT}/templates/gitignore`. If present, append only the lines it is missing (at minimum `.env`, `.env.*`, `!.env.example`, and the scratch folder).
- `.claude/` directory (empty is fine).
- `git init` if not a repo.

## 3. Write CLAUDE.md

Start from `${CLAUDE_PLUGIN_ROOT}/templates/CLAUDE.md` and fill the placeholders:

**`{{WORKSPACE_MAP}}`**
- If there is meaningful code: scan it the way `/init` does. Write a compressed map — what the project is, stack, where the entry points, config, content, and scripts live, the build/dev/test commands, and anything structurally surprising (e.g. "the real app is in `new-astro-website/`, root is legacy export data"). Aim for pointers, not prose. A good test: could a fresh session answer "where does X live?" from this section without a search?
- If the folder is empty or nearly so: write two or three lines from `$ARGUMENTS` (or ask the user for a one-liner if none was given), and add the note: *"Project is new. Develop for a while before adding preferences; encode friction you have actually hit, not friction you expect."*

**`{{EXTRA_PREFERENCES}}`**
- Only add lines you can justify from what you saw in step 1. Examples: the validation command to run before calling a change done (build + type-check when there is no test suite); a background dev-server convention; a project-specific output folder under the scratch dir. If nothing is justified, leave it empty. Do not import generic best practices.

**`{{CAPABILITIES}}`**
- List the scripts, commands, tools, and deploy targets you found that Claude can use without asking. If none, write one line: "Nothing project-specific declared yet — add entries as tools and scripts are created."

**`{{DATE}}`** — today's date, ISO format.

Write the whole file in one `Write` call, not a series of edits.

## 4. Keep AGENTS.md in sync

- If `AGENTS.md` does not exist: create it as a relative symlink to `CLAUDE.md` (`ln -s CLAUDE.md AGENTS.md`).
- If `AGENTS.md` exists as a real file with content: do not touch it. Tell the user it has content that `CLAUDE.md` does not, and that `/tickety-boo:sync` can merge them.
- Leave `GEMINI.md` alone; mention it if present.

## 5. Report

One short summary: what was created, what was skipped because it existed, and anything that needs the user's hand (creating `.env`, reconciling an existing `AGENTS.md`). Then the two-line reminder:

- Run `/tickety-boo:learnings` after each feature to compile lab notes and preferences.
- Run `/tickety-boo:security` before anything goes public.

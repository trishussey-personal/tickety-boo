---
name: kickoff
description: Set up a project folder the tickety-boo way — active/, .env.example, .gitignore, .claude/, and a four-section CLAUDE.md with AGENTS.md kept in sync. Safe to run on an existing project; it only adds what is missing.
disable-model-invocation: true
argument-hint: "[optional one-line description of the project]"
---

Set up the current working directory as a Claude Code project. Treat `$ARGUMENTS`, if given, as a one-line description of what this project is for.

Everything below is additive. Never overwrite or delete a file that already exists; report it and move on. Work in the current directory only — do not create a subfolder for the project. Everything you need is in this file; do not go looking for templates elsewhere.

## 1. Take stock

Run one pass to learn what is already here. Check for, and note the result of each:

- `CLAUDE.md`, `AGENTS.md`, `GEMINI.md` (exists? symlink? which is the real file?)
- `.claude/` directory
- `active/`, `scratch/`, `tmp/` (an existing scratch folder by another name — keep its name, do not add a second one)
- `.env`, `.env.example`, `.gitignore`
- `git` repo initialised?
- Is there code? What language/framework (look for `package.json`, `pyproject.toml`, `Cargo.toml`, `go.mod`, `*.astro`, etc.)? Roughly how big?

If `CLAUDE.md` already exists, create the missing scaffold items from step 2, then stop and tell the user to run `/tickety-boo:claude-md`, which restructures an existing file conservatively.

## 2. Scaffold the four things every workspace needs

Create only what is missing, in **one** Bash command so it is a single approval:

- `active/` with an empty `active/.tmp/` inside it (`.gitkeep` in each so git tracks the folders). If a scratch folder by another name already exists, use that name everywhere below instead of `active/`.
- `.env.example` (content below). Do **not** create `.env` itself.
- `.gitignore`: if absent, write the content below. If present, append only the lines it is missing (at minimum `.env`, `.env.*`, `!.env.example`, and the scratch folder contents).
- `.claude/` directory (empty is fine).
- `git init` if not a repo.

`.env.example`:

```
# Copy to .env and fill in. Never commit .env.
# Refer to these by name in conversation (e.g. "use OPENAI_API_KEY"), never paste values.

# EXAMPLE_API_KEY=
```

`.gitignore` (add the ecosystem's usual build/dependency ignores if you found code in step 1):

```
# secrets
.env
.env.*
!.env.example

# generated work — keep the folders, ignore the contents
active/*
!active/.gitkeep
!active/.tmp/
active/.tmp/*
!active/.tmp/.gitkeep

# os / editor
.DS_Store
Thumbs.db
.vscode/
.idea/

# claude local
.claude/settings.local.json
```

## 3. Write CLAUDE.md

Use this template exactly — same four headings, same order, same standing rules. Replace the `{{...}}` placeholders and remove the HTML comments if you like, but do not restructure. Write it in one `Write` call.

```markdown
# CLAUDE.md

Project-level instructions for Claude Code, loaded at the start of every session in this folder. Four sections, four jobs: a compressed map of the workspace, the conventions Claude follows here, what Claude can already do here, and a log of what has been tried.

## Workspace map

<!-- Knowledge compression. Bird's-eye only: what this project is, where things live, why it is shaped this way. Pointers, not contents. If this section starts restating the codebase it has stopped doing its job. -->

{{WORKSPACE_MAP}}

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
```

Filling the placeholders:

**`{{WORKSPACE_MAP}}`**
- If there is meaningful code: scan it the way `/init` does. Write a compressed map — what the project is, stack, where the entry points, config, content, and scripts live, the build/dev/test commands, and anything structurally surprising (e.g. "the real app is in `new-astro-website/`, root is legacy export data"). Aim for pointers, not prose. A good test: could a fresh session answer "where does X live?" from this section without a search?
- If the folder is empty or nearly so: write two or three lines from `$ARGUMENTS` (or ask the user for a one-liner if none was given), and add the note: *"Project is new. Develop for a while before adding preferences; encode friction you have actually hit, not friction you expect."*

**`{{EXTRA_PREFERENCES}}`**
- Only add lines you can justify from what you saw in step 1. Examples: the validation command to run before calling a change done (build + type-check when there is no test suite); a background dev-server convention; a project-specific output folder under the scratch dir. If nothing is justified, leave it empty. Do not import generic best practices.

**`{{CAPABILITIES}}`**
- List the scripts, commands, tools, and deploy targets you found that Claude can use without asking. If none, write one line: "Nothing project-specific declared yet — add entries as tools and scripts are created."

**`{{DATE}}`** — today's date, ISO format.

If the scratch folder has a different name, substitute it throughout.

## 4. Keep AGENTS.md in sync

- If `AGENTS.md` does not exist: create it as a relative symlink to `CLAUDE.md` (`ln -s CLAUDE.md AGENTS.md`).
- If `AGENTS.md` exists as a real file with content: do not touch it. Tell the user it has content that `CLAUDE.md` does not, and that `/tickety-boo:sync` can merge them.
- Leave `GEMINI.md` alone; mention it if present.

## 5. Report

One short summary: what was created, what was skipped because it existed, and anything that needs the user's hand (creating `.env`, reconciling an existing `AGENTS.md`). Then the two-line reminder:

- Run `/tickety-boo:learnings` after each feature to compile lab notes and preferences.
- Run `/tickety-boo:security` before anything goes public.

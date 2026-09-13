---
name: cleanup
description: Tidy the project's scratch folder (active/, scratch/, or notes/ for manuscripts) and file or remove anything that has leaked into the project root. Use when the user asks to clean up, tidy, declutter, or organise the workspace or scratch folder. Do not use for source directories or without being asked; it always presents a plan and asks before deleting or moving anything.
disable-model-invocation: false
argument-hint: "[optional folder, defaults to active/]"
---

Clean up the project's generated-work folder and check the root. Target folder is `$ARGUMENTS` if given, otherwise `active/` (or `scratch/` / `tmp/` if that is what the project uses — check `CLAUDE.md` or `.claude/CLAUDE.md`). For a manuscript project (has `chapters/`, `drafts/`, `research/`, `notes/`, `media/`), the scratch folder is `notes/`, and loose root files get filed by kind: prose to `drafts/` (never straight to `chapters/` — promotion is the author's call), sources to `research/`, images to `media/`, everything else to `notes/`.

## 1. Root check

List loose files in the project root. Anything that is not project infrastructure (config files, `README`, `CLAUDE.md`, `package.json`-type manifests, lockfiles, licence) is a candidate to move. Typical leaks: `.py`/`.mjs`/`.sh` one-offs, `.txt`/`.md` notes, `.json`/`.csv` data dumps, images, screenshots, `.log` files.

## 2. Scratch folder

Inside the target folder, anything in a subfolder is fine — leave it. For each loose file at the top level decide:

- **Temp** (partial output, `.log`, `.tmp`, duplicates, obviously superseded drafts) → delete.
- **Worth keeping** → move into a subfolder that makes sense. Prefer an existing subfolder; create one only if nothing fits, named by what the file is for (e.g. `web-design/`, `exports/`, `research/`).
- **Unsure** → ask.

Empty `active/.tmp/` entirely; that is what it is for.

## 3. Confirm, then act

Present the plan as a table: file, action, destination. Wait for a go-ahead before deleting or moving anything. Then do it, and report what was done.

## 4. Fold back

If any file you moved is something the project will keep needing (a script, a reference doc), add a one-line pointer to it under **Workspace map** in `CLAUDE.md` so a future session does not search for it.

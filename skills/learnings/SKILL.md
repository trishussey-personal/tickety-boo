---
name: learnings
description: Compile this session's dead ends and non-obvious wins into dated lab notes in CLAUDE.md, and capture a "how could this have been faster?" preference. Use when the user says a feature, task, or drafting session is done, wraps up, or asks to log what was learned. Do not use mid-task or for trivial sessions with nothing to record; it always shows proposed lines and asks before writing.
disable-model-invocation: false
argument-hint: "[optional: what the feature was]"
---

Compile what this session learned into the project's `CLAUDE.md` so the next session starts with it already in context. `$ARGUMENTS`, if given, names the feature just finished.

The file is `CLAUDE.md` or `.claude/CLAUDE.md` (manuscript projects use the latter); check both. If neither exists, say so and stop; `/tickety-boo:kickoff` creates one.

## 1. Look back over this session

Review the conversation so far and list, honestly:

- **Dead ends** — things attempted that did not work, and why. A command that failed, an approach abandoned, a library that turned out wrong, an assumption that was false. These prune the search space for next time and are the most valuable entries.
- **Things that worked but were not obvious** — the non-default command, the config quirk, the file that turned out to be the real one.
- **Friction** — moments where the user corrected you, or you did something the slow way.

If the session was short and nothing above applies, say "nothing worth logging" and stop. Do not invent entries.

## 2. Answer the speed question

Ask yourself: *how could this session have reached the same result faster and with fewer tokens?* Typical answers: one whole-file write instead of many edits; reading the docs first instead of trial and error; checking a config file before assuming; running the validation loop earlier. Pick the one or two that would actually change future behaviour.

## 3. Write

Compress everything to the fewest lines that would still save a future session time. Then:

- Append dead ends and non-obvious successes to **Lab notes: what not to do** as dated lines (`- YYYY-MM-DD: ...`). One line each, newest at the bottom. If the section does not exist, create it at the end of the file with the template's heading.
- Append speed-question answers and friction fixes to **Preferences & conventions** as standing rules, phrased as instructions ("Use one Write call for whole-file rewrites"), not as history. Skip any the file already states.
- If a **Capabilities** entry is now warranted (a new script, a new tool wired up), add it.

Use `Edit` for these appends; do not rewrite the whole file. Then show the user the exact lines added and ask whether any should be cut. A preference that is wrong costs every future session.

## 4. Sync

If `AGENTS.md` is a real file rather than a symlink, apply the same additions there.

---
name: sync
description: Keep CLAUDE.md, AGENTS.md, and GEMINI.md in agreement so the workspace works in any agent during an outage. Prefers a symlink (a plain copy in Claude Code plugin repos); merges when the files have diverged.
disable-model-invocation: true
argument-hint: "[--gemini to also maintain GEMINI.md]"
---

Make the agent instruction files in the current directory agree. `CLAUDE.md` is the source of truth (if the project keeps it at `.claude/CLAUDE.md`, symlinks must point there: `ln -s .claude/CLAUDE.md AGENTS.md`). `AGENTS.md` is the portable copy (Codex, Cursor, pi, and others read it). `GEMINI.md` is handled only if `$ARGUMENTS` contains `--gemini`.

## 1. Inspect

Check whether `.claude-plugin/plugin.json` exists. If it does, this is a Claude Code plugin, and the plugin directory's submission validator skips and flags symlinks: wherever step 2 says "symlink", use a plain copy instead (`cp CLAUDE.md AGENTS.md`, or from `.claude/CLAUDE.md` if that is the source), and replace an existing `AGENTS.md` symlink with a copy (remove the symlink, then copy from the source file). An identical copy is then the finished state; say so. With `--gemini`, the same applies to `GEMINI.md`: a plain copy (or the Gemini-specific merge below), never a symlink.

For each of `CLAUDE.md`, `AGENTS.md`, and (if requested) `GEMINI.md`: exists? symlink or real file? If real, does its content differ from `CLAUDE.md`?

## 2. Decide

- **`AGENTS.md` is already a symlink to `CLAUDE.md`** → nothing to do for it (unless this is a plugin repo; see step 1). Say so.
- **`AGENTS.md` missing** → create it as a relative symlink (`ln -s CLAUDE.md AGENTS.md`).
- **`AGENTS.md` is a real file, identical** → replace it with the symlink.
- **`AGENTS.md` is a real file, different** → diff them. Show the user what `AGENTS.md` has that `CLAUDE.md` lacks and vice versa. Propose merging the extra content into the right section of `CLAUDE.md`, then replacing `AGENTS.md` with the symlink. Wait for a go-ahead. If the user prefers separate files (some tooling chokes on symlinks), keep them separate and make the content match instead.
- **`GEMINI.md`** (only with `--gemini`): same logic, except if it contains Gemini-specific tooling instructions (e.g. setup blocks written by a Gemini-only tool), keep those at the top and append the shared content below rather than symlinking.

## 3. Standing rule

Make sure **Preferences & conventions** in `CLAUDE.md` contains the sync rule: "Whenever this file changes, `AGENTS.md` must match it (symlink, or re-sync). In a Claude Code plugin repo (`.claude-plugin/plugin.json` exists), keep it a plain copy, not a symlink: the plugin directory validator skips symlinks." Add it if missing, or add the second sentence if only the first is there. In a plugin repo, do this before copying in step 2, or re-copy afterwards, so the copies include it.

## 4. Report

One line per file: what it was, what it is now.

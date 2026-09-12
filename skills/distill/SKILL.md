---
name: distill
description: The global loop. Takes a Claude Code /insights report (or a pile of local lab notes) and distills it into a short list of high-ROI bullets for the global ~/.claude/CLAUDE.md, with contradictions flagged. Never writes to the global file itself — the human review step is the point.
disable-model-invocation: true
argument-hint: "<path to insights HTML/markdown, or 'lab-notes' to sweep CLAUDE.md files under a folder>"
---

Turn cross-project patterns into candidate lines for the global `~/.claude/CLAUDE.md`. Every line you propose will apply to every future session on this machine, so the bar is high and the human reads every one.

## 1. Gather

`$ARGUMENTS` is one of:

- A path to an `/insights` report (HTML or markdown). Read it. Go straight to the sections about recurring failures, repeated corrections, and "existing features to try"; skip the usage statistics.
- `lab-notes [folder]` — walk the folder (default: the current directory's parent) for `CLAUDE.md` files and collect every **Lab notes** and **Preferences & conventions** entry across projects.
- Nothing — ask which of the two the user wants, and remind them `/insights` needs to be run first in Claude Code.

## 2. Find what recurs

A pattern belongs in the global file only if it shows up across more than one project. Group what you gathered by theme. For each theme, note how many projects or sessions it appeared in. Drop anything that appeared once; that stays local.

## 3. Distill

Write each surviving pattern as one high-density line, phrased as an instruction, token-cheap, no explanation. Target: five to ten lines total. Good shape: "When rewriting most of a file, use one Write call instead of sequential Edits." Bad shape: a paragraph about why.

Sort into the global file's sections: interaction rules and reasoning principles; programming conventions; token conservation; capabilities that apply everywhere.

## 4. Check for contradictions

Read every proposed line against every other one and against the current `~/.claude/CLAUDE.md` (read it; do not write it). Flag any pair that conflict or overlap, with a recommendation for which to keep. This is the step the course insists a human does; you are preparing it, not deciding it.

## 5. Hand over

Write the proposal to `active/distill/global-candidates-YYYY-MM-DD.md` (create the folder if needed) with three parts: proposed lines by section, contradictions found, and the lines you dropped and why. Show the proposed lines in the conversation too. Then stop.

Say explicitly: nothing has been written to the global file. The user reviews, edits, and pastes what survives into `~/.claude/CLAUDE.md` themselves.

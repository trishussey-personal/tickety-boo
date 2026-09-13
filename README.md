# tickety-boo

A Claude Code plugin that sets a project up properly and keeps it that way. Distilled from Nick Saraev's *Claude Code Advanced Course* ([video](https://www.youtube.com/watch?v=UPtmKh1vMN8)) — specifically the parts that are repeatable per-project procedures rather than one-off patterns.

The idea: a `CLAUDE.md` is four jobs, not one system prompt — a compressed workspace map, your preferences, a declaration of what Claude can already do here, and a log of what has been tried. Every project gets the same four scaffold items (`.claude/`, `active/`, `.env`, `CLAUDE.md`), nothing gets written to root, and after every feature the learnings get compiled back into the file.

The four jobs are the actual philosophy; `kickoff`, `sync`, and `distill` are just what implementing them looks like when there's a filesystem to write to. For adapting the same discipline to a Cowork session with no connected folder, or a claude.ai Project with no filesystem at all, see [PHILOSOPHY.md](PHILOSOPHY.md).

## Skills

All can be invoked as `/tickety-boo:<name>`. `learnings` and `cleanup` may also be offered by Claude when the conversation matches (a feature wrapping up, a request to tidy); both confirm before writing or deleting. The rest only run when you type them — they create structure or spend agent time, so they should never fire on a guess.

| Skill | When | What it does |
|---|---|---|
| `kickoff` | New project, or an old one with no `CLAUDE.md` | Scaffolds `active/`, `.env.example`, `.gitignore`, `.claude/`; writes a four-section `CLAUDE.md`; symlinks `AGENTS.md`. Additive only. |
| `kickoff --manuscript` | A book, course, or long-form writing project | Scaffolds `chapters/` (settled), `drafts/` (in progress), `research/`, `notes/`, `media/`; writes a writing-shaped `CLAUDE.md` (at `.claude/CLAUDE.md` in Ulysses folders so it stays out of the sidebar); seeds lab notes from existing research. No `.env`, no `AGENTS.md`. |
| `claude-md` | Existing project with a `CLAUDE.md` | Restructures it into the four sections without losing anything. Proposes before writing. `--rewrite` allows compression. |
| `learnings` | After every feature | Compiles dead ends into dated lab notes, answers "how could this have been faster?", saves the answer as a preference. |
| `cleanup` | Periodically | Tidies `active/`, checks nothing leaked into root. Asks before deleting. |
| `sync` | When `AGENTS.md` drifts | Keeps `CLAUDE.md` / `AGENTS.md` (/ `GEMINI.md` with `--gemini`) in agreement. Prefers a symlink. |
| `security` | Before anything goes public | The 80/20 checklist, then hands off to the `security-auditor` agent. `--quick` for checklist only. |
| `distill` | After several projects | Turns an `/insights` report or a sweep of lab notes into candidate lines for the global `~/.claude/CLAUDE.md`. Never writes the global file. |

## Agents

| Agent | Purpose |
|---|---|
| `tickety-boo:qa-reviewer` | Fresh-context QA after a dev pass. The template `CLAUDE.md` tells Claude to spawn it before reporting done. Read-only. |
| `tickety-boo:security-auditor` | Fresh-context security audit. Spawned by `/tickety-boo:security`. Read-only. |

Both are deliberately given no project history — the agent that built something is biased toward believing it works.

The manuscript layout's `CLAUDE.md` also refers to `drafter`, `critic`, and `researcher` agents and a `writing-rag` source. Those are **not part of this plugin** — they are the author's own user-level agents and tools. If you don't have equivalents, the generated file still reads correctly; treat those lines as a description of the roles to fill (a voice-aware drafter, a fresh-context critic, a researcher) and swap in whatever you use.

## Install

**Try it without installing** (CLI):

```bash
claude --plugin-dir ./tickety-boo
```

**Install for every session** (CLI and desktop app). The repo doubles as a single-plugin marketplace:

```bash
claude plugin marketplace add ./tickety-boo
claude plugin install tickety-boo@tickety-boo
```

After editing skills, `/reload-plugins`. Agents and hooks need a reload; `SKILL.md` edits take effect immediately.

## The loop

1. `/tickety-boo:kickoff` once (`--manuscript` for writing projects).
2. Build things. Don't add preferences you haven't earned through friction.
3. `/tickety-boo:learnings` after each feature.
4. `/tickety-boo:security` before going public.
5. Every few projects, `/insights` then `/tickety-boo:distill` to promote what recurs to the global file — and read every line before pasting.

## What's deliberately not here

Fan-out/fan-in research, stochastic consensus, model-chat debate, agent teams, auto-research, browser automation, Codex/Conductor hedges. Those are "how to run a big job" patterns, not "set up a project" ones, and belong in a separate plugin if wanted.

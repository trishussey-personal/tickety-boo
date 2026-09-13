# The tickety-boo philosophy

`kickoff`, `sync`, `distill`, and the rest are one implementation of this philosophy, written for the one environment that can run arbitrary Bash and own a git repo: Claude Code on a local filesystem. The philosophy itself is not about files. It's about four jobs every long-running working relationship with an agent needs done, done *somewhere*, regardless of what that environment can persist.

This document is for adapting the philosophy to environments the plugin's skills cannot literally run in — a Cowork session with no connected folder, or a claude.ai Project with no filesystem at all. Read it before assuming "no filesystem" means "no discipline is possible here." It means a different substrate carries each job, not that the jobs go away.

## The four jobs, restated without reference to files

1. **Compress the workspace into something a fresh instance can act on without re-deriving it.** What this is, why it's shaped this way, where things live, what's already been decided. Pointers, not prose — the test is whether a cold-start instance can answer "where does X live" or "why does this exist" from the compression alone.
2. **Declare the ways of working that are not yet the tool's defaults.** Earned through friction, not imported as generic best practice. A preference nobody has hit a problem without yet doesn't belong here.
3. **Declare what the agent can already do.** Undeclared capability is invisible capability — an agent that doesn't know a tool, script, or data source exists will offer to build a worse version of it from scratch.
4. **Keep a dated log of what didn't work and why**, written for a future instance that has not made the mistake yet. Failures prune the search space exactly as much as successes expand it, and cost nothing to record except the discipline to stop and write the line down.

Every environment below answers these same four questions. What changes is the substrate: what can be written, what persists between sessions, and who has to move information between the two.

## The substrate table

| Environment | Can it write durable state itself? | Where each job lives |
|---|---|---|
| **Claude Code**, local filesystem | Yes — files, git, arbitrary structure | All four jobs live in `CLAUDE.md` (or `.claude/CLAUDE.md`), agent-writable, version-controlled. This is `kickoff`'s home ground; use it as written. |
| **Cowork, with a connected device folder** | Yes — the connected folder is a real filesystem the agent can read and write via the device bridge | Identical to Code. The cloud sandbox itself is a red herring; durable state lives on the user's disk, so treat the connected folder exactly like a local Code project and run `kickoff` there. |
| **Cowork, no connected folder** (cloud workspace only) | Only transiently — the sandbox is reclaimed with the session | Nothing survives except what is explicitly pushed out: a file delivered to the user, a published Artifact, or an entry in the account's cross-session memory store, where one exists. There is no local `CLAUDE.md` possible. The memory store is the nearest analog to job 1 and 2, but it is inherently about the *person* across every project, not any one project — so only the slice of the four jobs that is genuinely durable-about-the-user belongs there. Anything project-shaped that isn't flushed to a delivered file is gone at session end. |
| **claude.ai Project**, no filesystem, ever | No — the agent cannot write its own knowledge files mid-conversation | Job 1 (map) and job 4 (lab notes) live in **uploaded knowledge files** — static from the conversation's point of view, current only as of the last manual upload. Jobs 2 and 3 (preferences, capabilities) live in **custom instructions** — the one surface the agent can be told to follow every time, though even that is edited by the human, not the agent. New lab notes generated mid-conversation have nowhere to land; they exist only in that conversation's transcript until a human pulls them out. |

## The one rule underneath all three

**Before starting real work in any environment, ask where these four things currently live for *this* environment, and whether they're current — not whether files exist.** The Code-native answer (a `CLAUDE.md` file) is one valid implementation, not the definition. An environment that can't write its own state hasn't failed the philosophy; it has shifted who's responsible for keeping the state current, from the agent to the human.

## Sync only ever points one way into a Project

The plugin's `sync` skill keeps `CLAUDE.md` / `AGENTS.md` / `GEMINI.md` in agreement because all three live on the same filesystem and any of them could drift. That symmetry breaks the moment a Project is involved: a Project's knowledge files are a **mirror**, never a peer, of whatever filesystem holds the real, editable copy. The arrow only ever points filesystem → Project, by manual upload, never back. Design any cross-environment workflow around that asymmetry rather than expecting the Project to stay current on its own — it can't, and treating a stale knowledge file as ground truth is the actual failure mode this document exists to prevent.

The practical consequence: end every substantial Project session that surfaced something durable with an explicit recap — sorted into what's a decision, what's still open, what's raw material, and what needs checking — so a human can walk it back to wherever the real `CLAUDE.md` lives and paste it in. That recap is the sync mechanism. There isn't a better one available in an environment with no filesystem.

## Distillation has three tiers, not two

The plugin's `distill` promotes recurring project-level lab notes up to the global, Code-wide `~/.claude/CLAUDE.md`. Add a third tier above that: a global preference that turns out to be about *you*, not about any tool or repo — "I always want the QA pass run before I'm told something's done," say — belongs one level higher still, in whatever cross-session memory the account has, because it's true in Cowork and in every Project as well as in every Code repo. Promote in that direction — project, to global-Code, to cross-environment — and never skip a tier just because the same line would technically work at the top from day one. A preference earns its altitude the same way it earns its place in a `CLAUDE.md` at all: through friction actually hit, not best practice imported in advance.

## What this buys

A finding doesn't have to be re-derived because it happened to be born in the environment with the least ability to keep it. The boundary between sessions and the boundary between environments are not the same thing as a boundary in what's known — carrying information across either one is a deliberate, visible, human-mediated step, never a silent gap the next session has to rediscover the hard way.

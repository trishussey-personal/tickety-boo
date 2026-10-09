---
name: security
description: The security 80/20 for a project before it goes public — .env hygiene, .gitignore gaps, hallucinated package names, then a fresh-context audit by the tickety-boo:security-auditor agent. Returns a fix list; implementation is a separate step on purpose.
disable-model-invocation: true
argument-hint: "[--quick for the checklist only, no audit agent]"
---

Run the low-hanging-fruit security pass on the current project. The point is to be more effort than you are worth, not to be a vault.

## 1. The five-minute checklist (always)

Do these yourself, quickly, and report a pass/fail line for each:

1. **Secrets in the right place.** `.env` exists or is not needed; `.env.example` exists with names only; `.gitignore` excludes `.env` and `.env.*` but not `.env.example`; `git ls-files` shows no env file tracked. Do not open `.env`.
2. **Secrets not in chat.** Check `CLAUDE.md` (or `.claude/CLAUDE.md`) contains the rule "never read, print, or echo the contents of `.env`". If missing, mark it a fail and offer to add it to **Preferences & conventions**.
3. **Package names are real.** Read the dependency manifest. For any package you do not recognise as mainstream, check it exists at the registry and is not a near-miss of a popular name. Flag anything suspicious.
4. **Nothing card- or ID-shaped.** Grep the codebase for 13–19 digit numeric literals and for fields named like `card_number`, `ssn`, `passport`. Any hit is a finding.
5. **Row-level security**, if there is a Supabase/Postgres/Firebase-style database: is it on for every table? If you cannot tell from the code, note it as unverified.

If `$ARGUMENTS` contains `--quick`, stop here with the checklist results.

## 2. Fresh-context audit

Spawn the `tickety-boo:security-auditor` agent. Give it: the project root, what the project is in one line, what is public-facing, and the checklist results from step 1 so it does not repeat them. Do not give it this conversation's history — the whole point is that it has not seen the code being built.

Wait for its report.

## 3. Hand off, do not fix

Present the auditor's findings to the user grouped as it returned them (fix now / fix before public / hardening / clean). Then recommend the next step: implement the fixes in a **new conversation or with a different agent**, so the implementer is not over-constrained by the audit context, and have Claude review last. Offer to write the findings to `active/security/audit-YYYY-MM-DD.md` so the fixing session can pick them up.

Add one dated line to **Lab notes** in `CLAUDE.md`: "Security audit run; N findings; report at <path>." (or "report not saved" if the user declined the file).

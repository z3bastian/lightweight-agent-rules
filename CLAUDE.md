<!--
  ─────────────────────────────────────────────────────────────────────────
  MAP — where my Claude rules live.

  This block is a note to myself. Claude never sees it: HTML comments are
  stripped out before the file is loaded, so it costs zero context. It only
  shows up when I open the file here in my editor.
  ─────────────────────────────────────────────────────────────────────────

  1. ~/.claude/CLAUDE.md         How I want replies written.
                                 Loads in every Claude Code session —
                                 terminal, VS Code, desktop app — in any
                                 folder. Does NOT load in Cowork, which
                                 cannot see this folder. Cowork reads the
                                 Claude app's own Preferences instead, so
                                 these rules live in two places and must
                                 be kept in sync by hand.
                                 Hidden folder — reach it with /memory,
                                 not Finder.

  2. ~/DEV/CLAUDE.md             ← you are here. How I want code built.
                                 Loads only when I start Claude inside
                                 ~/DEV or a folder under it. Does load in
                                 Cowork when ~/DEV is the folder I
                                 connected to the session.

  3. <project>/CLAUDE.md         Build commands, architecture and quirks
                                 for ONE project.
                                 Have: webshop, webshop/admin.
                                 Missing: blog, landing-pages.
                                 (Example names — fill in your own.)
                                 Make one by running /init in the project.

  4. <project>/CLAUDE.local.md   Private notes for one project, gitignored.
                                 Not using this yet.

  5. ~/.claude/settings.json     Not instructions — hard limits the app
                                 enforces whether Claude agrees or not.
                                 Blocks `git reset --hard`, reading .env,
                                 and similar. Prompts me every single time
                                 for git push / merge / opening a PR.

  1 through 4 STACK, general first and most specific last. They add up;
  a later one does not replace an earlier one. Where two of them
  contradict, Claude may pick either — so keep them consistent.

  Only files named exactly CLAUDE.md or CLAUDE.local.md are ever loaded.
  Drafts, backups and old prompts with any other name are ignored.

  /memory      — open any of these files
  /context     — see which ones actually loaded this session
  /permissions — see the enforced limits from 5

-->

# Global rules — ~/DEV

Applies to every project under this folder. Project-specific build commands,
architecture and conventions belong in that project's own CLAUDE.md, not here.
How I want replies written lives in `~/.claude/CLAUDE.md`.

## Verification

- Do not report work as done without evidence. 
- After changing code, run the project's own check — test suite, build, typecheck or linter — and paste the result.
- If the project has no check you can run, say so explicitly instead of assuming the change works.
- Fix root causes. Never silence an error, skip a failing test, widen a type to `any`, or wrap something in try/except to make a failure disappear. If the real fix is out of scope, stop and tell me.
- IMPORTANT: "it should work" is not verification.

## Before changing anything

- Read the files you are about to change yourself, and follow the patterns already there over patterns you prefer. Delegate the wider search to subagents, never the code you are editing.
- Detect the project's tooling from its lockfiles and config, and use it. Never introduce a second package manager, test runner or formatter into a project that already has one.
- Ask before adding a dependency, upgrading a major version, or restructuring folders.
- If a change touches more than three files, or you are unsure of the approach, use plan mode and get the plan approved before writing code — unless that project's own CLAUDE.md defines its own build-and-review loop, which wins.
- Prefer the smallest change that solves the problem. No speculative abstraction, no defensive code for cases that cannot happen.

## Git

- Never commit to `main` or `master`. If work starts on either, create a branch yourself without asking, name it `claude/<short-description>`, and tell me the branch name in your first reply.
- On that branch, commit freely. Small working commits are checkpoints I want.
- Commit subjects: one imperative line, prefixed `feat:`, `fix:`, `chore:`, `refactor:` or `docs:`.
- YOU MUST NOT push, force-push, open a PR, or merge without my explicit approval each time.
- Never run `git reset --hard`, `git checkout .`, `git clean`, or anything else that discards uncommitted work without asking first.

## Secrets

- Never read, print, or echo the contents of `.env`, key files, tokens or credentials into the conversation.
- Reading a `.env.*` file (such as `.env.example`) triggers a permission prompt instead of a hard block. Before you trigger it, tell me in the chat exactly which file you are asking to read and why, so I know what I am approving — the popup names the file but does not explain it. The base `.env` stays off-limits entirely.
- Never stage or commit `.env*`, `*.pem`, `*.key`, or any file matching `*secret*` or `*credential*`. Check `.gitignore` covers them.
- If a credential is missing, tell me which variable name is needed. Do not go hunting for the value.

## Money and live systems

Several projects here talk to accounts that spend real money or execute real
orders — ad platforms, analytics and brokerage APIs.

- YOU MUST get explicit confirmation before any command or API call that spends budget, changes a live campaign, places or cancels an order, sends email to real recipients, or writes to production.
- Read-only calls against live accounts are fine without asking.
- Default to a dry run, sandbox, or test account when one exists. Say which one you used.
- Never run a destructive command against anything outside the current project folder.

## Context hygiene

- Use subagents for codebase-wide search and research so file dumps stay out of the main conversation.
- When compacting, always preserve the list of modified files and the commands needed to verify them.

## Files you create

- Do not leave files in my folders that I did not ask for. No scratch notes, no session summaries, no reports, no README you decided would be helpful.
- When I ask for a handover, continuation, or summary prompt for a new chat, print it in the conversation as one fenced code block. Never write it to a file.
- `DECISIONS.md` below is the only file you create without asking.

## Decisions log

- When we settle something that changes direction — a tool, a library, an architecture call, an approach we rejected — append one dated line to `DECISIONS.md` at the project root: what we chose, what we rejected, and the reason in one clause. Create the file at that moment if it does not exist. Do not create it at any other time.
- If `DECISIONS.md` exists, read it before proposing an approach. If you want to reopen a decision recorded there, say so explicitly rather than quietly doing something different.
- If the project's own CLAUDE.md names a different file for this — `PROGRESS.md`, for example — use that file and do not create `DECISIONS.md`.

## Per-project files

- Anything that is true of only one project goes in that project's CLAUDE.md, not this file.

<!--
  Master instructions file, shared by Claude Code and Codex.
  Installed as ~/.codex/AGENTS.md (Codex's official global instructions file).
  This is the only file to edit. Codex reads it directly; Claude reads it
  through the import line `@~/.codex/AGENTS.md` in ~/.claude/CLAUDE.md.
  Claude-only install: this content IS ~/.claude/CLAUDE.md, and that is the
  file to edit.
  Wiring: README.md in the setup package.
-->

# Instructions for {{NAME}}

Every AI coding tool I use reads this file, in every folder. "How to respond"
applies everywhere. The other sections apply whenever you work on code or a
project, wherever that is; my projects live in `{{PROJECTS_FOLDER}}`. A project's own instruction file adds to these rules
for that project.

## How to respond

- Lead with the answer. Do not restate my question or re-explain the problem before getting to it.
- Plain language over jargon. If you use a tool or term I may not know, define it in one clause. I am not a developer.
- When a reply covers multiple topics, or one topic with multiple aspects, number them so I can reply with just a number.
- Whenever you come back to a numbered item, name what it was about. Never a bare acknowledgement. This overrides the no-restating rule above.
  - Bad: "5 — okay, noted"
  - Good: "5 — noted: we're using Vercel, not Netlify"
- Any command you give me must be copy-pasteable exactly as written: absolute paths, the `cd` included, no placeholders. Write `cd "{{PROJECTS_FOLDER}}/{{EXAMPLE_PROJECT}}" && npm run dev`, never "run the dev command in your project folder".
- When you offer a choice, give the tradeoff, not just the options.
- Tell me when I'm wrong. Do not agree with a bad approach to be agreeable.
- Say "I don't know" or "I have not verified that" rather than guessing confidently.
- Do not narrate what you are about to do. Do it, then report what happened.

## Verification

- Do not report work as done without evidence.
- After changing code, run the project's own check — test suite, build, typecheck or linter — and paste the result.
- If the project has no check you can run, say so explicitly instead of assuming the change works.
- Fix root causes. Never silence an error, skip a failing test, widen a type to `any`, or wrap something in try/except to make a failure disappear. If the real fix is out of scope, stop and tell me.
- IMPORTANT: "it should work" is not verification.

## Before changing anything

- Read the files you are about to change yourself, and follow the patterns already there over patterns you prefer.
- Detect the project's tooling from its lockfiles and config, and use it. Never introduce a second package manager, test runner or formatter into a project that already has one.
- Ask before adding a dependency, upgrading a major version, or restructuring folders.
- If a change touches more than three files, or you are unsure of the approach, show me a short plan and wait for approval before writing code — unless the project's own instructions define their own build-and-review loop, which wins.
- Prefer the smallest change that solves the problem. No speculative abstraction, no defensive code for cases that cannot happen.

## Git

- Never commit to `main` or `master`. If work starts on either, create a branch yourself without asking, named after your tool — `claude/<short-description>` or `codex/<short-description>` — and tell me the branch name in your first reply.
- On that branch, commit freely. Small working commits are checkpoints I want.
- Commit subjects: one imperative line, prefixed `feat:`, `fix:`, `chore:`, `refactor:` or `docs:`.
- YOU MUST NOT push, force-push, open a pull request, or merge without my explicit approval each time.
- Never run `git reset --hard`, `git checkout .`, `git restore`, `git clean`, or anything else that discards uncommitted work without asking first. If I say yes and your tool still blocks the command, tell me and let me run it myself.

## Secrets

- Never read, print, or echo the contents of `.env` files, key files, tokens or credentials into the conversation. Template files such as `.env.example` are fine to read, but tell me which file and why first.
- Never stage or commit `.env*`, `*.pem`, `*.key`, or any file matching `*secret*` or `*credential*`. Check `.gitignore` covers them.
- If a credential is missing, tell me which variable name is needed. Do not go hunting for the value.

## Money and live systems

- YOU MUST get my explicit confirmation before any command or API call that spends budget, changes a live campaign, places or cancels an order, sends email to real recipients, or writes to production.
- Read-only calls against live accounts are fine without asking.
- Default to a dry run, sandbox, or test account when one exists. Say which one you used.
- Never run a destructive command against anything outside the current project folder.

## Files you create

- Do not leave files in my folders that I did not ask for. No scratch notes, no session summaries, no reports, no README you decided would be helpful. Files that a tool or plugin I installed writes as part of a workflow I started (a planning folder, for example) are fine.
- When I ask for a handover, continuation, or summary prompt for a new chat, print it in the conversation as one fenced code block. Never write it to a file.
- `DECISIONS.md` below is the only file you create without asking.

## Decisions log

- When we settle something that changes direction — a tool, a library, an architecture call, an approach we rejected — append one dated line to `DECISIONS.md` at the project root: what we chose, what we rejected, and the reason in one clause. Create the file at that moment if it does not exist. Do not create it at any other time.
- If `DECISIONS.md` exists, read it before proposing an approach. If you want to reopen a decision recorded there, say so explicitly rather than quietly doing something different.
- If the project's own instructions name a different file for this — `PROGRESS.md`, for example — use that file instead.

## Long sessions

- When the conversation is compacted or summarized, always keep the list of modified files and the commands needed to verify them.

## Where rules belong

- Anything true of only one project goes in that project's own instruction file (`AGENTS.md` or `CLAUDE.md`), not here.

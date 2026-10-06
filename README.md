# AI coding setup for Claude Code and Codex — v5

**Version 5, 5 Oct 2026 (revised 6 Oct 2026).** Built and tested on macOS with
Claude Code v2.1.260 and Codex (VS Code extension, codex-cli 0.155). Made by a
solo non-developer who works out of one projects folder in VS Code.

## What this gives you

- **One rules file that both Claude Code and Codex follow:** how to reply to you, how to verify work, git discipline, secrets, money and live systems, and which files the AI may create. You edit one file; both tools pick it up.
- **Hard limits for Claude Code:** permission rules the app enforces whether the AI agrees or not. Destructive git commands are blocked, `.env` and key files are blocked, and push, merge, `git restore` and opening a pull request ask first.

The difference between those two is the most useful idea here. A rule in an
instructions file is a request the AI can drift from. A permission rule is a
wall. Anything that destroys work or costs money should have a wall where one
is possible.

## How to install

1. Get the files: on GitHub, click the green **Code** button → **Download ZIP**, and unzip it. Put the folder anywhere — your projects folder is fine.
2. Open it in VS Code (File → Open Folder) and start a new Claude Code or Codex chat.
3. Type: **"Install this setup."** The AI reads `INSTALL.md`, looks at your machine, asks you up to nine questions, shows you the plan, and changes nothing until you say yes. Everything it touches is backed up first.

To update from a newer version of this package, do the same and type **"Update my setup from this package."** Your own edits are kept.

## How it is wired

| File | Read by | What it holds |
| --- | --- | --- |
| `~/.codex/AGENTS.md` | Both tools, every session | **The master.** All shared rules. The only file you normally edit. It is Codex's own global instructions file, so Codex reads it directly. |
| `~/.claude/CLAUDE.md` | Claude Code, every session | One import line, `@~/.codex/AGENTS.md`, that pulls in the master, plus a few Claude-only lines. |
| `~/.claude/settings.json` | Claude Code | The enforced permission rules, merged in next to your other settings. |
| A project's own `CLAUDE.md` / `AGENTS.md` | That project only | Build commands and quirks for one project. Adds to the master. |

```
~/.codex/AGENTS.md  (the master)
   ├── Codex reads it directly
   └── Claude reads it through @~/.codex/AGENTS.md in ~/.claude/CLAUDE.md
```

Only one tool? Claude only: the master's content goes straight into
`~/.claude/CLAUDE.md` and no `~/.codex` folder is created. Codex only: just
`~/.codex/AGENTS.md`, with no Claude files and no permission rules.

**Why the master lives in `~/.codex`.** `AGENTS.md` is the shared standard for
project folders, but there is no shared standard place for personal rules that
apply everywhere. Each tool has its own. Using Codex's official global file and
Claude's official import means only official file names and documented
features: no symlink (a pointer file), no invented name, and each tool loads the
rules exactly once. Nothing about the master lives in your projects folder.

The downside: `~/.codex` is a hidden folder, so Finder won't show it. Open the
master with `code ~/.codex/AGENTS.md` (opens it in VS Code), or ask your AI to
open it. Other AI tools can be pointed at `~/.codex/AGENTS.md` from their own
global settings. This package doesn't set that up, and it is untested.

After editing the master, start a **new** chat. Open sessions keep the
instructions they started with.

## What this does not cover

- **Codex gets the rules, but none of Claude's walls.** Codex has its own approval and sandbox settings; this package leaves them as they are.
- **A project's `CLAUDE.md` is invisible to Codex** (by default; Codex can be configured to read extra file names), and Claude reads a project's `AGENTS.md` only from v2.1.277, and only when there is no `CLAUDE.md`. If both tools need a project's notes, keep them in the project's `AGENTS.md` and put a `CLAUDE.md` containing just `@AGENTS.md` next to it. That works on every Claude version.
- **Permission rules match command text, not intent.** They stop habits; they are not a security boundary. `git  push` with two spaces, `rm`, or a script that opens a file itself all slip past. Commit often. That is the real safety net.
- **Money rules are honour-system.** Rules cannot tell "read an ad report" from "raise the budget" when both go through the same tool. Only a hook (a small script that checks each action before it runs) could, and this package has none.
- **Claude Cowork** skips imports in `~/.claude/CLAUDE.md` that point outside its working folder, so it does not see the master. (In a Claude-only install the rules sit in that file itself, and Anthropic's docs say Cowork loads the rest of the file, so it gets them.) With both tools, paste your reply style into the Claude app's preferences if you use Cowork.
- Windows is untested.

## Checked, not assumed

Source and version are given for each, because these things change.

- Claude Code imports can use home paths such as `@~/.claude/...`, and imports in `~/.claude/CLAUDE.md` load without an approval prompt (Anthropic docs, Oct 2026).
- Codex reads `~/.codex/AGENTS.md` (or `AGENTS.override.md` instead, if that exists), then only from the project's git root down — never folders above it (OpenAI docs).
- Codex loads `~/.codex/AGENTS.md` exactly once, both inside a project and in the projects folder itself (tested on this Mac, codex-cli 0.155).
- Claude Code reads `AGENTS.md` directly only from v2.1.277, and only when no `CLAUDE.md` exists in that folder or above. This setup doesn't rely on that, so it works on older versions.
- Claude checks file rules only through `Read(...)` and `Edit(...)`. `Write(...)` rules are accepted but ignored (docs, v2.1.210+). `Edit` denies already protect against overwriting a file.
- Permission rules evaluate deny → ask → allow; a deny anywhere cannot be overridden (tested v2.1.227).
- `Read` denies also cover `cat`, `head`, `tail`, `sed` run through the shell, but not a Python or Node script opening the file (tested v2.1.227).
- HTML comments (`<!-- -->`) are stripped from Claude's instruction files before loading (docs). Codex does not strip them, so keep comments short.

## Gotchas

1. A chat's instructions are fixed when it starts. After any change, open a new chat.
2. Starting Claude Code in your home folder makes home the "project": settings load twice and `/permissions` shows every rule doubled. Start in your projects folder or a project.
3. Terminal commands go in Terminal, not in the chat box. A line ending in `%` or `$` is a terminal.
4. Only exact file names load: `CLAUDE.md` and `CLAUDE.local.md` for Claude, and `AGENTS.md` for Codex (and for Claude from v2.1.277, in projects). Codex also reads `AGENTS.override.md`, which replaces `AGENTS.md` in the same folder — a `~/.codex/AGENTS.override.md` would hide the master from Codex. Backups and drafts under other names are ignored, which is why the installer saves backups as `….md.bak`.

## Changes since v4

- One master file for both Claude Code and Codex, at `~/.codex/AGENTS.md`, instead of Claude-only `CLAUDE.md` files. Codex reads it directly; Claude imports it. The reply style is now part of it.
- `git restore` now asks first. It discards work like the blocked `git checkout .`, but a full block would also stop the harmless `git restore --staged`.
- Install and update are now a guided, reversible process (`INSTALL.md`) instead of "read and copy by hand".
- The install recommends removing `skipDangerousModePermissionPrompt`, which skips Claude's confirmation before bypass-permissions mode (documented in Anthropic's settings reference).
- Rules rewritten so both tools understand them. Claude-only wording (plan mode, subagents, the `.env.*` prompt) moved to `~/.claude/CLAUDE.md`.
- Revised 6 Oct 2026: an earlier v5 draft kept the master at `<projects folder>/AI-RULES.md`, with a symlink for Codex. "Update my setup" moves it to `~/.codex/AGENTS.md`.

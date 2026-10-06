# Claude config — how the pieces fit together

**Extracted from a working private setup on 12 Aug 2026.** Everything below
was checked on **Claude Code v2.1.227 on macOS**, not assumed — but on a newer
version, re-verify before trusting it; the permissions system changes.
Machine-specific history has been removed. Where a concrete name was needed,
it is an example (`~/DEV`, `webshop`) — substitute your own.

---

## The config surfaces

| # | File | What it holds | Where it loads |
| --- | --- | --- | --- |
| 1 | `~/.claude/CLAUDE.md` | How replies are written | Claude Code only — terminal, VS Code, desktop app. **Not Cowork.** |
| 2 | Claude app → Settings → preferences | Same reply rules, pasted by hand | Cowork tasks and normal Claude app chats |
| 3 | `<projects-folder>/CLAUDE.md`, e.g. `~/DEV/CLAUDE.md` | How code is built | Claude Code started inside that folder, **and** Cowork when it is the connected folder |
| 4 | `<project>/CLAUDE.md` | One project's build commands and quirks | That project only |
| 5 | `~/.claude/settings.json` | Hard limits the app enforces | Every Claude Code session, any folder |

1–4 stack, general first, most specific last. They add up; a later one does
not replace an earlier one. Where two contradict, Claude may pick either —
so keep them consistent.

**1 and 2 are the same rules in two places.** This is not tidy, but it is
necessary if you use Cowork: Cowork cannot see `~/.claude` at all, and app
preferences do not reach Claude Code in a terminal. Neither one covers
everything. If you edit one, edit the other. If you never use Cowork,
surface 2 does not apply to you.

Only files named exactly `CLAUDE.md` or `CLAUDE.local.md` are ever loaded.
Drafts, backups and old prompts under any other name are inert.

---

## What `permission-rules.json` enforces once installed

Prompts every time — 4 rules:

    Bash(git push *)
    Bash(git merge *)
    Bash(gh pr *)
    Read(//**/.env.*)

Blocked outright — 11 rules:

    Bash(git reset --hard *)      Read(//**/.env)
    Bash(git clean *)             Read(//**/*.pem)
    Bash(git checkout .)          Read(//**/*.key)
    Bash(git checkout -- *)       Edit(//**/.env)
                                  Edit(//**/.env.*)
                                  Edit(//**/*.pem)
                                  Edit(//**/*.key)

`Read(//**/.env.*)` prompts instead of blocking on purpose: a deny would also
catch harmless template files like `.env.example`, which Claude often needs to
read to know which variables a project expects. The base `.env` stays blocked.
The prompt itself only names the file — there is no documented way to put a
custom message in the popup — so the `CLAUDE.md` secrets rule makes Claude
explain in chat which file it wants and why before the prompt appears.

These are opinions, not requirements — read `HANDOVER.md` before installing
any of them.

---

## Verified — checked, not assumed (on v2.1.227)

- Permission rules evaluate **deny → ask → allow**, first match wins. A deny at
  any settings level cannot be overridden by an allow at another level.
- `Bash(git push *)` and `Bash(git push:*)` are **equivalent**. Both documented.
  The space form is what Claude Code itself writes. There is no bug here.
- `//` at the start of a file pattern means absolute path from the root of the
  disk, so `Read(//**/.env)` matches `.env` anywhere.
- Read deny rules **do** cover `cat`, `head`, `tail` and `sed` run through Bash.
  They do **not** cover a Python or Node script that opens the file itself.
- `defaultMode: "auto"` does not suppress deny or ask rules. Both still apply.
- Bash rules match the literal command text, so `git  push` with two spaces
  slips past. Anthropic's own docs call these patterns "fragile." They stop
  Claude acting on habit; they are not a security boundary.
- **Cowork cannot read `~/.claude`.** It only receives the folder connected to
  the session. This is why surface 2 in the table above has to exist.
- **Starting Claude Code in your home folder (`/Users/<you>`) makes home
  itself the project folder.** `~/.claude/settings.json` is then read twice —
  once as user settings, once as project settings — and `/permissions` shows
  every rule doubled. Harmless, but it also creates a stray auto-memory folder
  for "home as a project." Start Claude in your projects folder or in a
  project.
- HTML comments (`<!-- ... -->`) in a `CLAUDE.md` are stripped before the file
  is loaded, so notes-to-self inside them cost no context.

---

## The known gap — permission rules cannot see intent

Rules match on tool names and literal command text. So there is no rule that
expresses "never commit to the main branch," and no rule that expresses "ask
before this API call spends money" when the tool that spends shares a name
with the tool that reads a report — an ad-platform integration, for example,
may route hundreds of operations through a dozen router tools, and a rule
sees only the router's name. Both cases need a **PreToolUse hook** — a small
script that inspects the actual arguments before a call runs. That is real
code, not configuration. If you touch anything that spends real money, the
rules in this folder are *not* enough on their own; the `CLAUDE.md` money
rules are honour-system until a hook enforces them.

---

## Unverified — test on your own machine

- Whether reply rules pasted into the Claude app's Settings actually reach
  Cowork. Test: start a new Cowork task and ask it to repeat your preferences
  back verbatim.

---

## Files in this folder

| File | What it is |
| --- | --- |
| `HANDOVER.md` | Read first. What is portable, what is not, what to ask before installing. |
| `CLAUDE.md` | The engineering-rules template for surface 3. |
| `permission-rules.json` | The 15 rules above, ready to merge into surface 5. |
| `CLAUDE-SETUP.md` | This file. |
| `reply-style-example.md` | An example of surface 1. Write your own. |

---

## Gotchas — time already lost, so you don't have to lose it

1. A session's working directory is fixed when it starts. Opening a different
   folder in VS Code afterwards does not change it, and rules files are not
   re-scanned.
2. In VS Code, typing `/permissions` in the chat spawns a separate terminal
   session and shows a one-time workspace trust dialog.
3. Terminal commands go in Terminal, not in a Claude chat box. If the line ends
   in `%`, it is a terminal.
4. `/memory` opens rules files in a terminal text editor (vim or nano) and takes
   over the terminal. Know the quit keystrokes before running it.
5. `~/.claude` cannot be added through the Cowork folder picker — it reports
   "cannot mount the home directory itself." To let a Cowork session see a file
   in there, `cp` it into a normal folder first.
6. Settings are read once, when a session starts. Nothing needs "restarting" —
   just open a new terminal and run `claude` again.

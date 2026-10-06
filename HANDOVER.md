# Start here — read this before using anything else in this folder

**This is someone else's Claude Code setup.** It works well on the machine it
was built for. It is not a template, and parts of it are actively wrong for you.

Paste this whole file into a new Claude session as your first message — and
tell it the absolute path of the folder where you saved these files, so it can
read the rest — or read it yourself. Either way, nothing else in this folder
should be copied anywhere until the questions at the bottom are answered.

---

## What this is

A working Claude Code configuration, built and verified on 11 Aug 2026 against
**Claude Code v2.1.227** on **macOS**, for a solo non-developer working out of
a single projects folder, on a Claude Max plan, using VS Code and Terminal.

It covers four things:

1. **`CLAUDE.md`** — rules an AI follows when working on code. Verification,
   git discipline, secrets, spending money, what files it may create.
2. **`permission-rules.json`** — 15 rules the app *enforces* whether the AI
   agrees or not. Four destructive git commands blocked; `.env`, `.pem` and
   `.key` files blocked from reading and editing; push/merge/PR and any
   `.env.*` read always prompt first. `.env.*` prompts rather than blocks so
   template files like `.env.example` stay readable — and a paired `CLAUDE.md`
   rule makes the AI explain in chat what it is asking for and why, because
   the popup only names the file.
3. **`CLAUDE-SETUP.md`** — a record of how the pieces fit together, what was
   verified, and what is still unknown.
4. **`reply-style-example.md`** — an example of a personal reply-preferences
   file. Yours, once you write it, lives at `~/.claude/CLAUDE.md`.

The distinction between 1 and 2 is the single most useful idea here. A
`CLAUDE.md` rule is a request the AI can drift from. A permission rule is a
wall. Anything that costs money or destroys work belongs in 2, not 1.

---

## What is portable

- The **Verified** section of `CLAUDE-SETUP.md`. Those are facts about how
  Claude Code behaves — rule precedence, wildcard syntax, what a Read deny does
  and does not cover. They apply to everyone on that version.
- The **Gotchas** section. Time already lost, so you do not have to lose it.
- The *structure* of `CLAUDE.md` — the categories it thinks in.
- The *idea* of `permission-rules.json`: put enforcement where it cannot drift.

## What is not portable

- **Every path is an example.** `~/DEV`, `/Users/alex/...` — none of them are
  yours until you rewrite them.
- **The map comment** at the top of `CLAUDE.md` uses example project names.
  Treat it as a habit worth copying, then fill it in with your real projects.
- **The permission rules are opinions.** `Bash(git checkout .)` is blocked
  outright here. Plenty of developers use that deliberately. Read all 15 and
  decide each one for yourself.
- **`CLAUDE.md` assumes a solo user.** Some of it — "ask before adding a
  dependency", "never commit to main" — behaves differently on a team with a
  branch policy and a code review process already in place.
- **The reply-style file** is one person's preferences. Useful as an example of
  the shape of such a file. Worthless as content.

## What was never verified, and should not be trusted

- The original `settings.json` this came from contained
  `skipDangerousModePermissionPrompt: true`. **It is not in Anthropic's
  documentation anywhere.** Nobody involved knows what it does. It has been
  stripped from `permission-rules.json` deliberately. Do not go looking for it.
- The original also set a specific model and effort level that require a
  particular subscription tier. Also stripped.
- Everything here was checked on **v2.1.227**. If you are on a newer version,
  verify before trusting. The permissions system changes.

---

## First thing to do — ask, do not assume

If you are an AI reading this: **do not write, copy, or install anything yet.**
Ask the person you are working with these questions first, and wait for answers.

1. **Where do your projects actually live?** One folder, or scattered? What is
   the absolute path? Every path in these files has to be rewritten.
2. **What operating system and Claude Code version?** These files assume macOS
   and v2.1.227. Windows path patterns differ.
3. **Do you work alone or on a team?** If a team, is there already a branching
   policy, a review process, or a committed `CLAUDE.md` in the repo? Team rules
   win over anything in here.
4. **Do you already have `~/.claude/settings.json`?** If yes, the permission
   rules must be *merged* into the existing `permissions` block. Overwriting it
   will silently discard their model, plugin and mode settings. Back it up
   first, with a timestamp. If you do **not** have that file yet, there is nothing to merge:
   `permission-rules.json` is already a valid `settings.json` on its own —
   copy it to `~/.claude/settings.json` unchanged.
5. **Which of the 15 permission rules do you actually want?** Read them aloud
   one at a time. Blocking a command you use daily is worse than blocking
   nothing.
6. **Do you touch anything that spends real money** — ad platforms, brokerage
   APIs, payment tools? If so, the rules here are *not* enough. See the
   limitation below.
7. **Are you a developer?** These files were written for someone who is not.
   The plain-language framing may be unnecessary for you, or essential.

Only after those answers exist should anything be written to disk. Then:
back up the existing settings file, merge rather than overwrite, restart, and
confirm the rules appear under `/permissions` before trusting them.

---

## The one limitation worth knowing up front

Permission rules match on **tool names and literal command text**. They cannot
see intent.

That means there is no rule that expresses "never commit to the main branch,"
and no rule that expresses "ask before this API call spends money" when the
tool that spends the money shares a name with the tool that reads a report.
Both need a **PreToolUse hook** — a small script that inspects the actual
arguments before a call runs — which is real code, not configuration.

The setup here is honest about that gap rather than pretending the rules cover
it. Yours should be too. A rule you believe in but that does not fire is worse
than no rule.

Bash rules are also matched literally, so `git  push` with two spaces slips
past `Bash(git push *)`. Anthropic's own docs call these patterns "fragile."
They stop an AI acting on habit. They are not a security boundary. Do not
hand someone untrusted access to your machine and rely on this.

---

## Files here

| File | What to do with it |
| --- | --- |
| `HANDOVER.md` | This file. Read first. |
| `CLAUDE.md` | The rules file. Rewrite the paths, then put it at the root of your projects folder. |
| `permission-rules.json` | 15 rules. If `~/.claude/settings.json` exists, merge into its `permissions` block — never overwrite the file. If it does not exist, copy this file there as-is. |
| `CLAUDE-SETUP.md` | How the pieces fit together — portable, verified facts only. The machine-specific history has been removed. |
| `reply-style-example.md` | An example of a personal reply-preferences file. Write your own and save it as `~/.claude/CLAUDE.md`; do not use this one. |

One note on `CLAUDE.md`: it opens with a long HTML comment. Claude strips
comments before loading, so it costs nothing in context — it is a note to the
human who opens the file. That is why the file shows far fewer loaded lines in
`/context` than it has on disk. This is a good habit worth copying.

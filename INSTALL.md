# Install or update — instructions for the AI doing it

You are Claude Code or Codex, and a person has asked you to install or update
this setup. Follow these steps in order. A human can follow them too.

Rules while you work:

- Change nothing before the person approves the plan in step 3.
- Never change an existing settings or instruction file without a backup, and without having shown the person exactly what will change.
- Backups go in a new folder for each attempt: `<projects folder>/_setup-backups/<YYYY-MM-DD-HHMMSS>/`. Copy each file there before touching it, renamed by origin and ending in `.bak` so no tool loads it and no two backups collide: `home-claude-CLAUDE.md.bak`, `home-claude-settings.json.bak`, `home-codex-AGENTS.md.bak`, `projects-CLAUDE.md.bak`, `projects-AGENTS.md.bak`, `projects-AI-RULES.md.bak`. In the same folder, write `CHANGES.txt`: one line per path saying whether it was edited, replaced, removed, or newly created, and for a symlink (a pointer file) the old link target. Undo depends on this list. Make the backup folder private (`chmod 700` on the folder, `chmod 600` on each file), because settings files can contain keys. If the projects folder is a git repository or a synced folder (iCloud Drive, Dropbox, Google Drive), say so and offer `~/setup-backups/<YYYY-MM-DD-HHMMSS>/` instead.
- Never open `.env`, key, or credential files, and never run destructive git commands to "test" anything.
- `~/.claude/settings.json` can hold API keys and tokens. Never read or print the whole file (no `cat`, no opening it in full). Read and change only the `permissions` block and the `skipDangerousModePermissionPrompt` key, with a script that touches nothing else:
  - read: `python3 -c 'import json,os;d=json.load(open(os.path.expanduser("~/.claude/settings.json")));print(json.dumps({"permissions":d.get("permissions"),"skipDangerousModePermissionPrompt":d.get("skipDangerousModePermissionPrompt")},indent=2))'`
  - write: load the JSON in a script, change only those two keys, and save it back, so every other value passes through without being displayed.
- macOS and Linux only. On Windows, stop and say this package has not been tested there.

## Step 1 — Look first (read-only)

Find out, without asking:

1. Tool versions: `claude --version`, and `codex --version`. Codex in VS Code ships its own copy at `~/.vscode/extensions/openai.chatgpt-*/bin/*/codex` — use that path if `codex` is not on the PATH.
   If the `CODEX_HOME` environment variable is set (`echo $CODEX_HOME`), Codex uses that folder instead of `~/.codex`. This package doesn't support that: say so, and offer a Claude-only install.
2. Which of these already exist, and what is in them (for `~/.claude/settings.json`, only through the read command above):
   - `~/.claude/CLAUDE.md`
   - `~/.claude/settings.json`
   - `~/.codex/AGENTS.md` — a real file (the person's existing Codex rules: handled by question 8, then replaced by the master), a symlink (note where it points; it gets replaced by a real file), or missing
   - `~/.codex/AGENTS.override.md` — if it exists, Codex reads it *instead of* `AGENTS.md`, so Codex would ignore the master. Raise it in step 2: they rename or remove the override themselves, or accept that Codex won't see the master.
   - a `CLAUDE.md`, `AGENTS.md`, or `AI-RULES.md` directly in the projects folder (once you know it)
3. Whether this is an **update**: `~/.codex/AGENTS.md` (or, for a Claude-only install, `~/.claude/CLAUDE.md`) starts with the "Master instructions file" comment — or an `AI-RULES.md` with that comment exists in the projects folder (an earlier v5 draft). If so, skip to "Updating an existing install" at the bottom.

## Step 2 — Ask (one message, numbered, all at once)

1. What name should the rules file use for you?
2. What is the absolute path of your projects folder? (The folder that holds your project folders.)
3. Do you use Claude Code, Codex, or both? This decides the layout: **both** — the master is `~/.codex/AGENTS.md` and Claude imports it; **Claude only** — the master's content goes straight into `~/.claude/CLAUDE.md` and no `~/.codex` folder is created; **Codex only** — just `~/.codex/AGENTS.md`, and all Claude steps are skipped.
4. The "How to respond" section in `templates/AGENTS.template.md` is one person's reply style. Keep it, keep it but drop "I am not a developer", write your own, or remove the section? Show them the section.
5. Do you work alone, or in a team with its own branch and review rules? (In a team, the team's rules win; offer to drop the Git section.)
6. Show the permission rules from `templates/claude-permissions.json` in plain language (blocked: …; asks first: …). Keep all, or drop some? (Claude Code users.)
7. This install creates or edits files outside your projects folder. List the ones that apply: `~/.codex/AGENTS.md` (Codex or both), `~/.claude/CLAUDE.md` and `~/.claude/settings.json` (Claude or both). Is that OK? Explain what a "no" costs: without `~/.codex/AGENTS.md` there is no master for Codex (Codex never reads instruction files above a project's git folder), and in a both-tools install Claude loses it too, since Claude imports it from there; without `~/.claude/CLAUDE.md` Claude doesn't get the rules; without `~/.claude/settings.json` Claude has no walls. They can approve the paths one by one.
8. Only if step 1 found existing instruction files (`~/.claude/CLAUDE.md`, a real `~/.codex/AGENTS.md`, or a `CLAUDE.md`/`AGENTS.md`/`AI-RULES.md` directly in the projects folder): show their rules that are not already in the template, and ask which to keep in the master. Include `@` import lines and anything that isn't a plain rule — those are easy to lose. Kept rules go into the matching section of the master; anything without a matching section goes under a final `## Personal additions` heading. Rules they don't keep are dropped (the backup still has them).
9. Only if `~/.claude/settings.json` has `"skipDangerousModePermissionPrompt": true`: explain that it skips Claude's confirmation before bypass-permissions mode (the mode where Claude acts without asking). Recommend removing it so that warning comes back. Remove it or keep it?

## Step 3 — Show the plan and wait for "yes"

List every file you will create, edit, or remove, and where its backup goes. For each existing file you will edit or replace, show what it says now and what it will say after (for `settings.json`: only the `permissions` block and the `skipDangerousModePermissionPrompt` line). If they use Codex, mention Codex's approval mode here: this package doesn't change it. In VS Code it is chosen in the Codex chat panel, and it can also be set in `~/.codex/config.toml`. Ask them to check it is what they want. Do nothing until the person approves.

## Step 4 — Install

Skip the parts that don't apply to the answer to question 3.

**a. The master file** — `~/.codex/AGENTS.md` (both, or Codex only)

Copy `templates/AGENTS.template.md`, replace `{{NAME}}`, `{{PROJECTS_FOLDER}}`, and `{{EXAMPLE_PROJECT}}` (a real folder name from their projects folder), and apply the answers from step 2. Write it as a real file; create `~/.codex` if needed.

- If `~/.codex/AGENTS.md` exists as a real file: back it up, then replace it. Its rules were handled by question 8.
- If it is a symlink: record its old target in `CHANGES.txt`, remove the link (this never touches the file it points to), and write the master as a real file in its place.

**b. Claude Code** — `~/.claude/CLAUDE.md` (both, or Claude only)

- Start from `templates/claude-user-CLAUDE.template.md`. Both tools: keep it as it is — the line `@~/.codex/AGENTS.md` imports the master. Claude only: the file is the filled-in `templates/AGENTS.template.md` (as in 4a, header comment included, so it starts with "Master instructions file"), followed by the "Claude Code only" section of the claude-user template. Leave out the claude-user template's own header comment and its import line.
- If the file does not exist: create it, and create the `~/.claude` folder first if it is missing. If it exists: back it up, then replace it. Its rules were handled by question 8.

**c. Claude Code permissions** — `~/.claude/settings.json` (both, or Claude only)

- If it does not exist: create it containing only the approved rules from `templates/claude-permissions.json`, and mark it "newly created" in `CHANGES.txt`.
- If it exists: back it up. Merge the approved rules into `permissions.deny` and `permissions.ask` (add missing ones, never remove theirs, no duplicates). Leave every other key alone.
- Apply the answer to question 9 about `skipDangerousModePermissionPrompt`.
- Check the file is still valid JSON: `python3 -m json.tool ~/.claude/settings.json > /dev/null && echo OK`

This package does not change Codex's own approval or sandbox settings.

**d. Old setup files in the projects folder**

If the projects folder itself contains a `CLAUDE.md` or `AGENTS.md` holding general rules (from an older setup), or an `AI-RULES.md` (from an earlier v5 draft), back it up, then remove it. Its rules were handled by question 8. Otherwise Claude or Codex loads the same rules twice. Leave each project's own `CLAUDE.md` / `AGENTS.md` alone.

## Step 5 — Verify

Pick one real project folder `<P>`.

- **Claude:** ask the person to open a new Claude chat inside `<P>` and type `/context`. It should list `~/.claude/CLAUDE.md` and `~/.codex/AGENTS.md` (Claude only: just `~/.claude/CLAUDE.md`), plus the project's own file if it has one. `/permissions` lists the rules. This is the reliable check; asking Claude which files it loaded can miss some.
- **Codex:** `cd "<P>" && codex debug prompt-input | grep -o "Master instructions file, shared by" | wc -l` (if `codex` is not on the PATH, use the full path found in step 1, in quotes) — expect `1` (0 means Codex doesn't see the master; 2 means it loads twice). Use `grep -o … | wc -l`, not `grep -c`: the output is one long line, so `grep -c` always says 1. Also ask a new Codex chat in VS Code: "Which instruction files did you load?"
- A session that was already open keeps its old instructions. Only new sessions pick up changes.

## Step 6 — Report

Tell the person, in plain language: what changed, where the backups are, how to check it themselves, and how to undo it. Tell them the master is in a hidden folder: they can open it with `code ~/.codex/AGENTS.md` (VS Code), or ask their AI to open it.

**Undo**, following that attempt's `CHANGES.txt`:

- Edited, replaced, or removed files: copy the `.bak` back to its original path.
- Newly created files: delete them.
- An old symlink that was replaced: delete the new `~/.codex/AGENTS.md` and recreate the link with its recorded target: `ln -s "<recorded target>" ~/.codex/AGENTS.md`

---

## Updating an existing install

The installed master belongs to the person — it may hold their own edits. Do not replace it.

The master is `~/.codex/AGENTS.md` (both tools, or Codex only) or `~/.claude/CLAUDE.md` (Claude only).

1. Compare, and collect proposed changes — change nothing yet:
   - the new `templates/AGENTS.template.md` against the installed master, section by section — only real rule changes, ignoring their name, paths, and their own edits;
   - the "Claude Code only" section of `templates/claude-user-CLAUDE.template.md` against the one in `~/.claude/CLAUDE.md` (Claude installs);
   - permission rules in `templates/claude-permissions.json` missing from `~/.claude/settings.json` (Claude installs). Never propose removing theirs.
   - `"skipDangerousModePermissionPrompt": true` still in `~/.claude/settings.json`: include question 9 from step 2.
   - the wiring: master is a real file, not a link (`ls -l` shows no `->`); `~/.claude/CLAUDE.md` contains `@~/.codex/AGENTS.md` (both tools only); no `~/.codex/AGENTS.override.md`; no leftover general-rules `CLAUDE.md`/`AGENTS.md`/`AI-RULES.md` in the projects folder.
2. Show the list as a plan, with before/after for each file, and wait for "yes". They can approve items one by one.
3. Back up the files you will change into a new timestamped backup folder, with `CHANGES.txt`, then apply only the approved changes.
4. Run step 5 and report as in step 6.

**Moving from the earlier v5 draft** (master at `<projects folder>/AI-RULES.md`, imported by `~/.claude/CLAUDE.md`, and for Codex users `~/.codex/AGENTS.md` as a symlink to it). Include this in the plan of step 2, then in step 3:
- Back up `AI-RULES.md` as `projects-AI-RULES.md.bak` and `~/.claude/CLAUDE.md` as usual; record any link target in `CHANGES.txt`.
- Codex users (the link exists): remove the link and write `AI-RULES.md`'s content, with the template's new header comment, to `~/.codex/AGENTS.md` as a real file. If they also use Claude, rebuild `~/.claude/CLAUDE.md` from the claude-user template (new header comment and `@~/.codex/AGENTS.md`), keeping their "Claude Code only" lines.
- Claude-only users (no link): rebuild `~/.claude/CLAUDE.md` as in step 4b "Claude only", using `AI-RULES.md`'s content as the master. Create no `~/.codex`.
- Then remove `AI-RULES.md`.

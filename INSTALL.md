# Install or update — instructions for the AI doing it

You are Claude Code or Codex, and a person has asked you to install or update
this setup. Follow these steps in order. A human can follow them too.

This installs the local agents' standard home-directory files. The guided
workflow below was tested in VS Code on macOS; README distinguishes that
testing from documented compatibility in other local interfaces. Do not
assume a cloud, remote, container, or custom-home session reads these files.

Rules while you work:

- Change nothing before the person approves the plan in step 3.
- Never change an existing settings or instruction file without a backup, and without having shown the person exactly what will change.
- Backups always go outside projects: `~/.lightweight-agent-rules/backups/<YYYY-MM-DD-HHMMSS>/`. Use a new, empty folder for each attempt; if the timestamp already exists, choose a unique suffix. Set `umask 077` before creating anything and create the backup root, its `backups` directory, and the attempt directory with mode `700`; backup files must have mode `600`. Do not follow an existing symlink in this backup path or reuse someone else's directory. Never put backups in the projects folder, including on Linux.
- Make full copies of every existing file affected, including `~/.claude/settings.json`, with `cp` into that private directory before changing the originals. Do not preserve permissive source modes on the backups. Use origin-specific names ending in `.bak`: `home-claude-CLAUDE.md.bak`, `home-claude-settings.json.bak`, `home-codex-AGENTS.md.bak`, `projects-CLAUDE.md.bak`, `projects-AGENTS.md.bak`, `projects-AI-RULES.md.bak`. A settings backup may contain credentials: it must never be displayed or opened with a model-facing file tool. Copying it privately is allowed. Permissions do not stop processes running as the same user, and the backup is not encrypted.
- In the backup folder, write `CHANGES.txt` with one line per affected path: edited, replaced, removed, or created, its backup path, original mode, and any old symlink target. Record a hash of each instruction file's installed content so undo can detect later edits without opening backup contents. Include the settings change record specified in step 4c as a JSON block. Record planned changes before writing originals and mark each operation completed only after verification; never claim a failed operation completed. Keep this manifest private too.
- Never open `.env`, key, or credential files, and never run destructive git commands to "test" anything.
- `~/.claude/settings.json` can hold API keys and tokens. A Python script may parse it in memory to preserve unrelated settings, but never print the complete file, a raw diff, or unrelated values. Display only package rule presence/additions and the two bypass settings below. Do not display existing user command rules, which can themselves contain secrets. Report validation failures by path and reason, without echoing file contents.
- Every settings write, including undo and emergency restore, follows the validated replacement procedure in step 4c. Do not write directly over the original. If a settings path is a symlink, is not a regular file, or has an unexpected owner, stop that operation and explain before changing it.
- macOS and Linux only. On Windows, stop and say this package has not been tested there.
- Respect the person's existing approval rules. Give them exact commands for any outside-project move or removal their rules require them to perform themselves. Reuse answers already explicitly given in the conversation instead of asking the same question again.

## Step 1 — Look first (read-only)

Find out, without asking:

1. Tool versions: `claude --version`, and `codex --version`. Codex in VS Code ships its own copy at `~/.vscode/extensions/openai.chatgpt-*/bin/*/codex` — use that path if `codex` is not on the PATH.
   If the `CODEX_HOME` environment variable is set (`echo $CODEX_HOME`), Codex uses that folder instead of `~/.codex`. This package doesn't support that: say so, and offer a Claude-only install.
2. Which of these already exist, and what is in them (for `~/.claude/settings.json`, only a filtered script result as described above):
   - `~/.claude/CLAUDE.md`
   - `~/.claude/settings.json`
   - `~/.codex/AGENTS.md` — a real file (the person's existing Codex rules: handled by question 8, then replaced by the master), a symlink (note where it points; it gets replaced by a real file), or missing
   - `~/.codex/AGENTS.override.md` — if it exists, Codex reads it *instead of* `AGENTS.md`, so Codex would ignore the master. Raise it in step 2: they rename or remove the override themselves, or accept that Codex won't see the master.
   - a `CLAUDE.md`, `AGENTS.md`, or `AI-RULES.md` directly in the projects folder (once you know it)
3. Whether `<projects folder>/_setup-backups/` exists, by directory name only. Do not open its `.bak` files. If present, explain that old backups may contain complete settings files and credentials; use the optional migration procedure below.
4. Whether this is an **update**: `~/.codex/AGENTS.md` (or, for a Claude-only install, `~/.claude/CLAUDE.md`) starts with the "Master instructions file" comment — or an `AI-RULES.md` with that comment exists in the projects folder (an earlier v5 draft). If so, skip to "Updating an existing install" at the bottom.

## Step 2 — Ask (one message, numbered, all at once)

1. What name should the rules file use for you?
2. Explain before asking: the **projects folder** is the folder where they normally keep their separate projects, such as `~/DEV` or `~/Documents/Projects`. They may choose another accessible local folder, including one outside their home folder; use a dedicated projects folder, separate from the agents' configuration and backups, not their entire home folder. If they use several projects folders, choose the one used most often. This location is written into their rules for context and command examples, and checked for old setup files. Choosing it does not move projects, grant access, restrict the shared rules to that folder, or relocate the global configuration and private backups. Then ask: which folder should the setup use? Help them identify it if needed; they do not need to know its full path already.
3. Do you use Claude Code, Codex, or both? This decides the layout: **both** — the master is `~/.codex/AGENTS.md` and Claude imports it; **Claude only** — the master's content goes straight into `~/.claude/CLAUDE.md` and no `~/.codex` folder is created; **Codex only** — just `~/.codex/AGENTS.md`, and all Claude steps are skipped.
4. The "How to respond" section in `templates/AGENTS.template.md` is one person's reply style. Keep it, keep it but drop "I am not a developer", write your own, or remove the section? Show them the section.
5. Do you work alone, or in a team with its own branch and review rules? (In a team, the team's rules win; offer to drop the Git section.)
6. Show the permission rules from `templates/claude-permissions.json` in plain language (matching operations blocked: …; asks first: …). Keep all, or drop some? (Claude Code users.) Explain the limits in README: these are selected patterns, not a complete sandbox. `~/.ssh/id_*` includes public keys; `.pem` and `.key` can match non-secret files too.
7. This install creates or edits files outside your projects folder. List the ones that apply: `~/.codex/AGENTS.md` (Codex or both), `~/.claude/CLAUDE.md` and `~/.claude/settings.json` (Claude or both), and the private backup directory. Is that OK? Explain what a "no" costs: without the master or its import, the affected tool does not get these shared rules; without the settings merge, Claude does not get this package's permission rules. They can approve paths one by one. Do not substitute a projects-folder backup if they decline the private backup location; skip the affected changes.
8. Only if step 1 found existing instruction files (`~/.claude/CLAUDE.md`, a real `~/.codex/AGENTS.md`, or a `CLAUDE.md`/`AGENTS.md`/`AI-RULES.md` directly in the projects folder): show their rules that are not already in the template, and ask which to keep in the master. Include `@` import lines and anything that isn't a plain rule — those are easy to lose. Kept rules go into the matching section of the master; anything without a matching section goes under a final `## Personal additions` heading. Rules they don't keep are dropped (the backup still has them).
9. Only if `~/.claude/settings.json` has `"skipDangerousModePermissionPrompt": true`: explain that it skips Claude's confirmation before bypass-permissions mode (the mode where Claude acts without asking). Recommend removing it so that warning comes back. Remove it or keep it?
10. For Claude users, unless bypass mode is already disabled: disable bypass-permissions mode? Recommend yes for ordinary local work, and explain that `permissions.disableBypassPermissionsMode = "disable"` prevents entering it, including with `--dangerously-skip-permissions`. This is stronger than restoring its warning. It does not disable auto mode. If they decline, leave the setting unchanged. If already disabled, report that and preserve it. Check effective rules in `/permissions` when managed settings may apply; never try to override them.

Resolve their projects-folder answer to an absolute path and show it in the
plan. Check that it is an accessible directory; do not guess another location
if it is missing or points to a file. Keep it separate from `~/.codex`,
`~/.claude`, and `~/.lightweight-agent-rules`: after resolving symlinks for this
check, it must neither contain those folders nor be inside them. Explain that
they need a dedicated projects folder, not their entire home folder. If they
want a new folder, include its creation explicitly in the plan and wait for
approval. Do not move existing projects. Complete the projects-folder checks
from step 1 once the location is known, and include any resulting question 8
choices before approval.

## Step 3 — Show the plan and wait for "yes"

List every file you will create, edit, or remove, and where its backup goes. For instruction files, show the proposed before/after content. For settings, show only the approved rule additions and the before/after values of the two bypass settings; omit unrelated settings and user rule text. Explain that the full settings backup may contain credentials, normal undo is selective, and emergency full restore would discard later settings changes. Include any proposed old-backup migration explicitly. Do nothing until the person approves.

For Codex, recommend **Ask for approval** in the permission menu, with command network access off unless the task needs it. Commands exceeding the configured boundaries require approval. Web search and connected tools have separate controls; do not promise every outside read or internet interaction prompts. Do not confuse this with **Approve for me / Auto-review** or **Full access**. This package does not change Codex configuration or paste fixed `config.toml` keys. References: [permission modes](https://learn.chatgpt.com/docs/permission-modes), [security guidance](https://learn.chatgpt.com/docs/agent-approvals-security).

## Step 4 — Install

Skip the parts that don't apply to the answer to question 3.

**a. The master file** — `~/.codex/AGENTS.md` (both, or Codex only)

Copy `templates/AGENTS.template.md`, replace `{{NAME}}`, `{{PROJECTS_FOLDER}}`, and `{{EXAMPLE_PROJECT}}` (a real folder name from their projects folder), and apply the answers from step 2. If no project exists there yet, omit the illustrative `Write …, never …` sentence instead of inventing a runnable command; keep the rule requiring complete commands. Write it as a real file; create `~/.codex` if needed.

- If `~/.codex/AGENTS.md` exists as a real file: back it up, then replace it. Its rules were handled by question 8.
- If it is a symlink: record its old target in `CHANGES.txt`, remove the link (this never touches the file it points to), and write the master as a real file in its place.

**b. Claude Code** — `~/.claude/CLAUDE.md` (both, or Claude only)

- Start from `templates/claude-user-CLAUDE.template.md`. Both tools: keep it as it is — the line `@~/.codex/AGENTS.md` imports the master. Claude only: the file is the filled-in `templates/AGENTS.template.md` (as in 4a, header comment included, so it starts with "Master instructions file"), followed by the "Claude Code only" section of the claude-user template. Leave out the claude-user template's own header comment and its import line.
- If the file does not exist: create it, and create the `~/.claude` folder first if it is missing. If it exists: back it up, then replace it. Its rules were handled by question 8.

**c. Claude Code permissions** — `~/.claude/settings.json` (both, or Claude only)

Use a Python script to parse the original in memory, or start with an empty object if absent. Require a JSON object; if present, `permissions` must be an object and `deny`/`ask` must be arrays of strings. The warning setting, if present, must be Boolean; the bypass-disable setting, if present, must be `"disable"`. Reject duplicate JSON keys rather than silently dropping a value. Stop on invalid input without changing it; do not repair unrelated settings as part of installation.

Merge only the approved rules into `permissions.deny` and `permissions.ask`: preserve existing order and rules, append missing rules once, and do not normalize or deduplicate the user's existing entries. Create structures only when needed. Apply question 9 only if removal was approved; apply question 10 only if disabling was approved. Leave every other key and value alone. If nothing changes, report a no-op and do not rewrite settings.

Before writing, back up the existing file privately and record this state in the JSON block in `CHANGES.txt` (no unrelated values):

- whether the file, `permissions`, `permissions.deny`, and `permissions.ask` existed;
- the approved package rules and, for each, whether it existed before; the exact rules this attempt added to each array;
- the presence and old value of `skipDangerousModePermissionPrompt` and `permissions.disableBypassPermissionsMode`;
- for each of those two settings, whether this attempt changed it and its installed presence/value; unchanged settings must not be restored during undo;
- the settings file's original owner/group and mode, and the status of the operation.

**Validated replacement — install, undo, and emergency restore:**

1. Keep the original bytes and file metadata in script memory without displaying them. Do not run another settings writer at the same time.
2. Create a unique temporary file beside `settings.json`, initially mode `600`. Write the proposed JSON there and flush it to disk. For an emergency restore, copy the backup to this temporary file without displaying it.
3. Parse the temporary file again; validate the structure above. For a normal install or undo, compare the parsed result with the proposed object and check that unrelated values match the original. JSON syntax alone is insufficient.
4. Preserve the original owner/group and mode. For a newly created settings file, use the current owner and mode `600`.
5. Recheck that the original still has the same bytes and metadata, or is still absent if it was absent. If it changed, stop for a fresh plan rather than overwriting newer settings.
6. Replace the destination with one atomic rename (`os.replace`) on the same filesystem, only after all checks pass. On failure, leave the original intact, remove only this attempt's temporary file, and report the error without contents. Retain the backup and mark the operation incomplete.
7. Verify the saved JSON and package changes without printing unrelated values, then mark the operation complete. The successful replacement is the commit point: if later verification or manifest writing fails, report that the replacement occurred and stop; do not imply the original is still installed or silently restore it.

Official syntax and bypass-setting references: [file permission rules](https://code.claude.com/docs/en/permissions#read-and-edit), [settings reference](https://code.claude.com/docs/en/settings-reference#permissions-disablebypasspermissionsmode).

This package does not change Codex's own approval or sandbox settings.

**d. Old setup files in the projects folder**

If the projects folder itself contains a `CLAUDE.md` or `AGENTS.md` holding general rules (from an older setup), or an `AI-RULES.md` (from an earlier v5 draft), back it up, then remove it. Its rules were handled by question 8. Otherwise Claude or Codex loads the same rules twice. Leave each project's own `CLAUDE.md` / `AGENTS.md` alone.

## Step 5 — Verify

Pick one real project folder `<P>`.

- **Claude:** ask the person to open a new Claude chat inside `<P>` and type `/context`. It should list `~/.claude/CLAUDE.md` and `~/.codex/AGENTS.md` (Claude only: just `~/.claude/CLAUDE.md`), plus the project's own file if it has one. `/permissions` lists the rules. This is the reliable check; asking Claude which files it loaded can miss some.
- **Codex:** `cd "<P>" && codex debug prompt-input | grep -o "Master instructions file, shared by" | wc -l` (if `codex` is not on the PATH, use the full path found in step 1, in quotes) — expect `1` (0 means Codex doesn't see the master; 2 means it loads twice). Use `grep -o … | wc -l`, not `grep -c`: the output is one long line, so `grep -c` always says 1. Also ask a new Codex chat in VS Code: "Which instruction files did you load?"
- Check that the expected settings rules and approved bypass choice are present through a filtered script; print only package rule counts/presence and the two bypass settings. Verify backup folder/file modes are `700`/`600` and the change record is complete.
- Report separately what was checked by script and what the person verified in a new app session. Do not report a fresh-session check as passed until it actually happened.
- Start new sessions for verification rather than relying on an already-open chat to reload instructions.

## Step 6 — Report

Tell the person, in plain language: what changed, where the backups are, how to check it themselves, and how to undo it. Tell them the master is in a hidden folder: they can open it with `code ~/.codex/AGENTS.md` (VS Code), or ask their AI to open it.

**Normal undo**, following that attempt's `CHANGES.txt`, with a shown plan and approval:

- Settings: parse the current file using step 4c's validation. If it is missing after an attempt that changed an existing file, report a conflict rather than recreating it silently. Check all changed bypass settings before writing: if the current presence/value equals the installed state, restore the recorded old state; if it already equals the old state, leave it alone; otherwise stop the settings undo and ask which state to keep. Ignore bypass settings the attempt did not change.
- Remove only the exact rule strings recorded as added by this attempt, leaving pre-existing package rules and every other user rule alone. An added rule already absent needs no action. Remove an empty `deny`/`ask` array or `permissions` object only if this attempt created it. Preserve structures that existed before installation, even when empty.
- If this attempt created the settings file, remove it only when the proposed result is an empty object. If it has gained any unrelated settings, keep it and remove only the attempt's changes. Use the validated replacement procedure for a write; for an approved removal, recheck the original is unchanged immediately before removing it. Respect user rules about who performs outside-project removals.
- Instruction files: compare current state with the recorded installed hash (or expected absence for a removed file). If the user edited or recreated a file afterward, show the conflict and preserve it until they choose. Otherwise restore an edited/replaced/removed file from its opaque backup, or remove a file this attempt created. Restore original modes. Recreate an old symlink from its recorded target only after approval and the same conflict check.
- Undo attempts in reverse order. If an operation is marked incomplete, compare the live state with its recorded before/after state and resolve it before continuing; do not assume it never happened.

**Emergency settings restore** is a separate option, never the default. Explain that restoring the full `home-claude-settings.json.bak` discards *all* settings changes since that backup, obtain explicit approval, then copy it through step 4c's validated replacement procedure without displaying it. An attempt that created settings has no previous full file to restore.

---

## Updating an existing install

The installed master belongs to the person — it may hold their own edits. Do not replace it.

The master is `~/.codex/AGENTS.md` (both tools, or Codex only) or `~/.claude/CLAUDE.md` (Claude only).

1. Compare, and collect proposed changes — change nothing yet:
   - the new `templates/AGENTS.template.md` against the installed master, section by section — only real rule changes, ignoring their name, paths, and their own edits;
   - the "Claude Code only" section of `templates/claude-user-CLAUDE.template.md` against the one in `~/.claude/CLAUDE.md` (Claude installs);
   - permission rules in `templates/claude-permissions.json` missing from `~/.claude/settings.json` (Claude installs). Never propose removing theirs.
   - `"skipDangerousModePermissionPrompt": true` still in `~/.claude/settings.json`: include question 9 from step 2.
   - bypass mode not already disabled: include question 10 from step 2, without assuming an earlier answer;
   - the v5.1 backup location and selective undo: explain these apply to this update even when the instruction files need no changes;
   - an old `_setup-backups` directory: offer the separately approved migration below, without opening its backups;
   - the wiring: master is a real file, not a link (`ls -l` shows no `->`); `~/.claude/CLAUDE.md` contains `@~/.codex/AGENTS.md` (both tools only); no `~/.codex/AGENTS.override.md`; no leftover general-rules `CLAUDE.md`/`AGENTS.md`/`AI-RULES.md` in the projects folder.
2. Show the list as a plan, with before/after for each file, and wait for "yes". They can approve items one by one.
3. Back up the files you will change into a new timestamped backup folder, with `CHANGES.txt`, then apply only the approved changes.
4. Run step 5 and report as in step 6.

**Moving old backups outside projects** (optional, separately listed in the plan):

- Detect `<projects folder>/_setup-backups/` by name only. Offer to move its attempt folders under `~/.lightweight-agent-rules/backups/`; never inspect the `.bak` contents. Declining migration does not authorize a new projects-folder backup.
- Inventory names and permissions, check destination collisions, and choose unique destinations. Show the exact source and destination paths before moving. If the person's rules reserve this move for them, give exact commands for them to run.
- After approval, move each whole attempt folder while preserving its contents and `700`/`600` modes. Read only its `CHANGES.txt` to identify old absolute backup paths; replace only paths belonging to that moved attempt, not original installation destinations. Preserve the original manifest privately before editing it, and show only the changed path lines. Verify the referenced backup files now exist without opening them. Old manifests without a selective settings record still have full-restore undo; do not invent missing state or promise selective undo for those old attempts.

**Moving from the earlier v5 draft** (master at `<projects folder>/AI-RULES.md`, imported by `~/.claude/CLAUDE.md`, and for Codex users `~/.codex/AGENTS.md` as a symlink to it). Include this in the plan of step 2, then in step 3:
- Back up `AI-RULES.md` as `projects-AI-RULES.md.bak` and `~/.claude/CLAUDE.md` as usual; record any link target in `CHANGES.txt`.
- Codex users (the link exists): remove the link and write `AI-RULES.md`'s content, with the template's new header comment, to `~/.codex/AGENTS.md` as a real file. If they also use Claude, rebuild `~/.claude/CLAUDE.md` from the claude-user template (new header comment and `@~/.codex/AGENTS.md`), keeping their "Claude Code only" lines.
- Claude-only users (no link): rebuild `~/.claude/CLAUDE.md` as in step 4b "Claude only", using `AI-RULES.md`'s content as the master. Create no `~/.codex`.
- Then remove `AI-RULES.md`.

# Lightweight Agent Rules

A small setup for Claude Code and Codex that makes your working preferences
available across projects. If large agent-rules repositories feel too
complicated, this package gives you a readable starting point.

The rules tell the agent to verify work before calling it finished, protect
secrets and uncommitted work, ask before costly or live-system actions, follow
your Git workflow, and record important project decisions in `DECISIONS.md`.

The system is intentionally lightweight: ordinary Markdown and JSON files,
with no background service or complicated framework. You can inspect, edit,
back up, or remove the setup yourself. Installation is guided by your AI
following [`INSTALL.md`](INSTALL.md); it is not an unattended program.

**Version 5.1.** Built and tested in VS Code on macOS. Other editors that run
Claude Code or Codex are untested. The installer covers macOS and Linux;
Linux has not received the same live installation testing. Windows is not
supported by this package.

## Who it is for

- Beginners who want each proposed change explained before it happens.
- Experienced users who want a small rule system they can customize.
- People who use Claude Code, Codex, or both across several projects.

## What this gives you

- **One shared rules file:** reply style, verification, Git discipline, secrets,
  money and live systems, project decisions, and which files the AI may create.
- **Selected Claude Code permission rules:** matching destructive Git commands
  are denied; matching push, merge, restore, and pull-request commands ask first.
  Selected credential file patterns are blocked from Claude's built-in file
  tools and recognized shell file commands. These rules reduce accidental
  access but are not a complete security boundary.
- **A guided, reversible installation:** a complete plan, approval before
  changes, private backups outside project folders, verification, and undo
  instructions that preserve unrelated settings changes.

Instructions guide the model's behaviour; permission rules are enforced by
Claude Code for the operations they match. Neither guarantees that every
dangerous operation or every way of accessing a file is covered.

## Easy installation

1. On GitHub, select **Code → Download ZIP**.
2. Unzip the downloaded folder.
3. Open that folder in VS Code.
4. Start a new Claude Code or Codex chat.
5. Type: **"Install this setup."**
6. Answer the installer's numbered questions (up to ten, some conditional).
7. Review the complete plan. Nothing changes until you approve it.
8. Say **yes** when the plan is correct.

The installer backs up affected files under
`~/.lightweight-agent-rules/backups/`, installs only what you approved,
preserves unrelated settings without displaying them, verifies the setup,
and gives you exact undo instructions. It also offers to disable Claude's
bypass-permissions mode.

To update, open the newer package and type:
**"Update my setup from this package."** Your personal rules are kept unless
you explicitly approve replacing them.

## How it is wired

| File | Purpose |
| --- | --- |
| `~/.codex/AGENTS.md` | The master shared rules. Codex reads its own global instructions file directly. |
| `~/.claude/CLAUDE.md` | Imports the master with `@~/.codex/AGENTS.md`, then adds a few Claude-only rules. |
| `~/.claude/settings.json` | Claude's permission rules, merged into your existing settings. |
| A project's `AGENTS.md` / `CLAUDE.md` | Instructions specific to that project. The installer leaves them alone. |

```text
~/.codex/AGENTS.md  (the master)
   ├── Codex reads it directly
   └── Claude imports it from ~/.claude/CLAUDE.md
```

Claude only: the shared rules go directly into `~/.claude/CLAUDE.md`, followed
by the Claude-only rules; no `~/.codex` folder is created. Codex only: install
just `~/.codex/AGENTS.md`, with no Claude files or Claude permission rules.

The combined layout uses Codex's documented global file and Claude's
import syntax. It needs no symlink and puts no master rules file in your
projects folder. The master is in a hidden folder: ask your AI to open it in
VS Code. After editing it, start a new chat and verify it loads.

For notes shared by both tools inside a project, keep them in `AGENTS.md`
and put `@AGENTS.md` in a neighbouring `CLAUDE.md`. This avoids relying on
version-dependent automatic discovery of `AGENTS.md` by Claude.

## Backups and undo

Each installation attempt gets a new private backup folder. Folders are
owner-only (`700`); backup files are owner-readable and writable (`600`).
Existing instruction files and Claude settings receive complete backups.
A change record identifies exactly what the installer added or changed.

Normal settings undo removes only rules added by that attempt and reverses
only its changed bypass settings. Unrelated later settings are preserved.
If a setting or instruction file touched by the installer has changed again,
the installer reports the conflict before replacing anything.

An emergency full settings restore is also available, but it discards every
settings change made since that backup. The AI must explain this and get
your approval before restoring it.

**Settings backups may contain credentials.** They are not encrypted. Keeping
them outside projects reduces accidental commits and project-folder syncing;
owner-only permissions do not prevent access by programs running as you.
The installer never displays the complete settings file or its backup.

## Permissions and limits

- **Codex uses its own controls.** Choose **Ask for approval** in its permission
  menu for routine work. Keep command network access off unless the task needs
  it. Commands that exceed the configured boundaries require approval; web
  search and connected tools have separate controls, and this does not promise
  to block every read outside a project. This package never edits Codex settings.
  See [OpenAI permission modes](https://learn.chatgpt.com/docs/permission-modes)
  and [security guidance](https://learn.chatgpt.com/docs/agent-approvals-security).
- **Claude rules cover selected operations.** File rules do not cover arbitrary
  Python or Node programs opening files, or every recursive shell read. A Git
  command written differently may miss a command pattern. See
  [Claude's permission limits](https://code.claude.com/docs/en/permissions).
- **File patterns are deliberately broad.** `~/.ssh/id_*` also matches public
  keys, and `.pem` and `.key` can be non-secret files. `.env` reads are denied;
  `.env.*` reads ask first, so examples can be approved individually.
- **Bypass mode is a separate choice.** Disabling it prevents selecting that
  mode. Restoring its confirmation warning alone does not disable it, and
  disabling bypass does not disable Claude's auto mode. See the
  [Claude settings reference](https://code.claude.com/docs/en/settings-reference#permissions-disablebypasspermissionsmode).
- **Money and live-system rules depend on the agent following instructions.**
  These file and command patterns do not enforce every action available through
  connected tools. The package installs no spending controls or enforcement hooks.
- **Other products are outside scope.** This package does not configure Claude
  Cowork or other coding assistants.

## Checked, not assumed

- The original v5 setup was tested on macOS in VS Code with Claude Code
  v2.1.260 and codex-cli 0.155. Those are historical test versions, not minimum
  version promises for every current feature.
- v5.1 installation and undo are checked with temporary fake home folders.
  These simulate an AI following `INSTALL.md`; they are not fresh-machine
  tests of the Claude or Codex apps. The installer still verifies each real
  installation in new sessions.
- Claude's documented `~/` and `//` file-rule paths and bypass-disable setting
  were checked against official documentation on 7 October 2026.
- Claude supports [home-relative imports](https://code.claude.com/docs/en/memory#import-additional-files).
  Codex documents [global and project instruction discovery](https://developers.openai.com/codex/guides/agents-md).
  A global `AGENTS.override.md` hides Codex's master, and a custom `CODEX_HOME`
  changes its location. The installer checks these before making changes.

## Changes in v5.1

- Backups always live outside project folders, with selective settings undo
  and an explicitly approved emergency full restore.
- Settings writes are validated before replacement; later conflicting changes
  are reported during undo.
- Additional credential-file patterns and an optional bypass-disable question.
- Codex permission guidance, clearer security limits, and explicit testing scope.
- Clean v4/v5 history retains the original file snapshots with new commit and
  tag attribution. v5's shared-master layout is unchanged.

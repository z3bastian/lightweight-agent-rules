<!--
  ~/.claude/CLAUDE.md — loads in every Claude Code session, in every folder.
  The import line pulls in the shared master, ~/.codex/AGENTS.md. Edit the
  master, not this file; only Claude-only additions belong here.
  Claude-only install (no Codex): see INSTALL.md step 4b.
-->

@~/.codex/AGENTS.md

# Claude Code only

- When the shared rules ask for a plan before a larger change, use plan mode.
- Use subagents for codebase-wide search and research, so file dumps stay out of the main conversation. Read the files you are editing yourself.
- Reading a `.env.*` file triggers a permission prompt that names the file but does not explain why. Before you trigger it, say in the chat which file and why. The base `.env` is blocked outright.

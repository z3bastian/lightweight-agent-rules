# Decisions

- 2026-10-07 — Retain the v5 master at `~/.codex/AGENTS.md` with Claude's documented import; reject a projects-folder master or symlink because the global layout works across repositories with one editable file.
- 2026-10-07 — Recreate v4 and v5 from their exact tagged file trees with a repository-local pseudonymous identity; reject carrying forward old history because its commit and tag metadata contains the unwanted identity.
- 2026-10-07 — Leave global Git identity unchanged; reject changing unrelated projects' future attribution as part of this repository cleanup.
- 2026-10-07 — Finish and test v5.1 locally before a temporary-private-repository swap; reject immediate deletion because a private archived original provides recovery until the replacement is verified.
- 2026-10-07 — Use private backups outside projects, including a full settings copy plus selective undo; reject projects-folder backups and partial-only recovery because accidental commits must be avoided while emergency restoration stays possible.
- 2026-10-07 — Add selected credential-file patterns; reject broad filesystem blocking, a Bash sandbox, and Codex command-rule files because this remains a small accident-prevention package.
- 2026-10-07 — Offer disabling Claude bypass mode as an optional installer question; reject equating a restored warning with disabling the mode because they have different effects.
- 2026-10-07 — Explain Codex permission outcomes without changing configuration; reject fixed configuration snippets because supported profiles and UI labels can change.
- 2026-10-07 — Use “Lightweight Agent Rules” and retain the useful README ideas from PR #2 and later main edits as text; reject merging their old history because the clean repository must keep its own ancestry.
- 2026-10-07 — State macOS/VS Code testing and remove untested editor compatibility claims and topics; reject implying Linux, other editors, or Windows received equivalent live testing.
- 2026-10-07 — Use one focused defect review after local scenario checks; reject repeated design-review rounds because actual failures and their fixes are the remaining review scope.
- 2026-10-07 — Approve consequential GitHub actions in explicit listed stages, with separate decisions for deletion and public visibility; reject treating a general implementation request as permission for those later actions.
- 2026-10-07 — Refine the earlier editor-compatibility decision to distinguish personally tested macOS/VS Code from compatibility established by official documentation, and limit GitHub About to those categories; reject blanket all-editor or desktop claims because shared configuration and live testing are different evidence.

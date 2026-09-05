# macOS installation, operation, upgrade, and rollback

## Install and start

Download the `.dmg` for your architecture from the matching GitHub Release, verify the published SHA-256 checksum, open it, and drag **LLM Wiki** to `/Applications`. Launch it from Finder. macOS may require confirmation in **System Settings → Privacy & Security** on first launch.

Developer builds can be started from a clean checkout with `npm ci`, `npm --prefix mcp-server ci`, then `npm run tauri dev`. The local API binds to `127.0.0.1` by default; do not enable LAN access without applying the product's authentication guidance.

Keep vaults outside the source checkout. Back up the entire vault—including hidden `.obsidian` and `.llm-wiki` directories—before an upgrade. API keys and signing credentials must never be placed in the repository or vault.

## Privacy defaults

- The local HTTP API binds to `127.0.0.1`; LAN access is opt-in and requires the safeguards in [Security](SECURITY.md).
- External web search, watched folders, and optional cloud parsers are configured separately. Leave them disabled when a workflow must remain fully local.
- Provider credentials are stored by the operating-system credential store. Do not copy `app-state.json`, credentials, or private vault content into diagnostics.
- A hosted model or embedding endpoint receives the content sent to it. Select Ollama or another loopback OpenAI-compatible endpoint for an offline workflow.

## Back up and restore

Quit LLM Wiki and Obsidian before a filesystem copy. Copy the complete vault, including hidden directories, and the LLM Wiki application-data directory shown by macOS for the installed app. Prefer a filesystem snapshot or a tool that preserves metadata. Keep backups encrypted when the vault is sensitive.

Restore into a new directory first. Open that copy with networking disabled, inspect notes and attachments in Obsidian, then open it in LLM Wiki and rebuild derived indexes if necessary. Replace the original only after the copy passes these checks. Per-file history under `.llm-wiki/history/` is useful for individual text mistakes but is not a substitute for a whole-vault backup.

## Upgrade

1. Quit LLM Wiki and Obsidian.
2. Make a timestamped copy or filesystem snapshot of the vault and application data.
3. Read release notes for migrations and minimum macOS requirements.
4. Verify the new download checksum, replace the app in `/Applications`, and start it.
5. Open a non-critical vault first; confirm notes, attachments, settings, search index, local API binding, and provider configuration before normal use.

Do not delete the previous installer until the new version has passed the smoke test. Index data is derived and may be rebuilt, but the vault is authoritative user data.

## Roll back

1. Quit both applications and preserve the failed post-upgrade state for diagnosis.
2. Restore the pre-upgrade vault/application-data snapshot if the release performed a data migration.
3. Replace `/Applications/LLM Wiki.app` with the prior verified release.
4. Start offline where practical, confirm the vault, then rebuild derived indexes if required.

If rollback still fails, keep the vault untouched, collect redacted application logs, and report the old/new versions, macOS version, architecture, and exact failing step. Never attach API keys, tokens, vault content, or `app-state.json` to an issue.

## Troubleshooting

- **App is blocked on first launch:** verify the DMG checksum and source, then use **System Settings → Privacy & Security**. Do not bypass Gatekeeper for an untrusted artifact.
- **App appears to stay open after closing:** on macOS, closing hides the window. Use the Dock icon to reopen it or **Cmd+Q** to quit before backups and upgrades.
- **Vault changes do not appear:** quit the other editor, reopen the vault, and confirm the selected directory. Resolve stale-write errors by reviewing the external version; never overwrite it blindly.
- **Provider is unavailable:** verify endpoint, model, and network mode without printing the key. For Ollama, confirm it is listening on loopback and the model is installed. Vault browsing remains available offline.
- **Search misses recently changed notes:** rescan sources or rebuild the derived index. Do not delete the vault to repair an index.
- **Local API is unreachable:** confirm it is enabled and use `127.0.0.1:19828`, not a LAN address. Mutating and paid routes require the configured token.
- **Upgrade fails:** stop, preserve the failing state, and follow the rollback steps above. Do not run destructive cleanup commands against the authoritative vault.

For the complete release verification, including OpenAI, local-provider, citations, controlled writes, and Obsidian reopening, use [MVP acceptance](MVP_ACCEPTANCE.md).

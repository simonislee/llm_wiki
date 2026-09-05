# MVP release acceptance

Use this runbook on a clean macOS 13+ account before publishing a release. A protocol or mocked test does not replace the two live-provider checks below. Record the app version, source commit, macOS version, architecture, provider/model names, and pass/fail for every step. Never record keys, tokens, private note contents, or full provider responses.

## Automated gate

From a clean checkout with the pinned toolchain:

```sh
npm ci
npm --prefix mcp-server ci
npm run check
npm run test:mvp:protocol
npm run package:macos
```

`test:mvp:protocol` exercises OpenAI configuration/credential redaction and a real loopback TCP exchange through the Ollama and custom OpenAI-compatible streaming paths. It is deterministic and incurs no provider cost. `check` covers frontend, MCP, and Rust vault/security regressions. Keep the generated `.app` and `.dmg` paths in the acceptance record.

## Clean installation and vault round trip

1. Use a new macOS user or disposable VM with no previous LLM Wiki application data. Verify the DMG checksum, drag **LLM Wiki** to `/Applications`, and launch it.
2. Confirm the local API listens only on `127.0.0.1`. Leave LAN access, external web search, and automatic source watching disabled unless this test explicitly needs them.
3. Create a vault. Confirm it contains `.obsidian/`, `.llm-wiki/`, `raw/`, and `wiki/` and remains outside the source checkout.
4. Import one Markdown file containing frontmatter, a wikilink, and an attachment. Confirm the source remains readable and the generated wiki page cites its source path.
5. Ask a factual question answerable only from that document. Verify the answer and citation against the original text; unsupported claims are a failure.
6. Request creation of a new wiki page. Review the proposed path/content and approve it. Confirm an existing page is not overwritten without explicit overwrite approval and history.
7. Quit LLM Wiki. Open the same directory as an Obsidian vault; verify notes, frontmatter, wikilinks, embeds, and attachment bytes. Edit one note in Obsidian and add an unrelated file.
8. Quit Obsidian and reopen the vault in LLM Wiki. Confirm the external edit appears, the unrelated file and `.obsidian/` settings remain unchanged, and a stale in-app edit is rejected rather than silently overwriting it.
9. Rename and delete a test note through the explicit UI flows, then restore the deleted text from file history. Reopen in Obsidian and confirm the final state.

Run this scenario once with a newly created vault and once by selecting a pre-existing Obsidian vault.

## Live OpenAI smoke

Use a restricted, low-budget test key entered through Settings; do not put it in shell history or environment files.

1. Select **OpenAI**, choose a currently supported model, save, quit, and reopen the app. Confirm the provider remains configured without the key appearing in the vault or application logs.
2. Import the fixture document, complete one streaming chat, and ingest it into at least one cited wiki page.
3. If embeddings are enabled, index the page and retrieve it with a query whose wording differs from the source.
4. Cancel one in-flight request and verify prompt cancellation. Test an invalid key and verify the error is actionable and redacted.
5. Remove the credential after the smoke test and confirm offline vault browsing still works.

## Live local-provider smoke

With Ollama listening on loopback, for example:

```sh
ollama serve
ollama pull llama3.2
```

Select **Ollama**, set the loopback endpoint and installed model, and repeat the import, streaming question, citation check, approved wiki write, cancellation, app restart, and Obsidian reopen steps. No API key should be required. An LM Studio or llama.cpp OpenAI-compatible loopback endpoint may be used instead; record which implementation and model were tested.

## Backup, restore, and upgrade drill

Follow [macOS operations](MACOS_OPERATIONS.md). Before upgrading, back up the complete vault including hidden directories and the application-data directory. Modify a test note after the backup, restore to a separate path, and verify the restored copy before replacing anything. Install the new app over the old version, rerun the critical path above, and retain the previous installer until acceptance passes.

## Release evidence and decision

A release candidate passes only when:

- all automated commands pass from a clean checkout;
- the generated DMG installs and launches on the target architecture;
- both live OpenAI and local-provider rows pass;
- the vault round trip passes in both directions with citations and controlled writes;
- backup/restore and upgrade/rollback are demonstrated; and
- [Security](SECURITY.md), [macOS operations](MACOS_OPERATIONS.md), [known limitations and roadmap](ROADMAP.md), and GPL source obligations are reviewed.

A missing credential, model, signing identity, notarization permission, or Obsidian installation is a recorded blocker—not a pass. Public macOS artifacts must be signed and notarized; unsigned output is suitable only for local smoke testing.

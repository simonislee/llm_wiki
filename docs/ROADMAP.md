# Known limitations and post-MVP roadmap

## MVP boundaries and known limitations

- LLM Wiki reads and writes an Obsidian-compatible vault, but it does not reproduce Obsidian's proprietary UI, workspace state, sync, publish service, or community-plugin runtime. Use Obsidian for those capabilities.
- Compatibility covers Markdown, YAML frontmatter, wikilinks, embeds, attachments, and checked file operations. Theme-specific rendering, plugin-defined syntax, plugin databases, and arbitrary plugin side effects are not guaranteed.
- A local-first app is not automatically private when a hosted model, embedding endpoint, web search, URL import, or cloud document parser is enabled. Content sent to those services is governed by their policies.
- Model answers can be wrong. Citations expose evidence for review; they do not certify correctness. Agent writes remain previewed/controlled and deletion is not exposed as an agent tool.
- Search indexes and embeddings are derived state and may need rebuilding after model or schema changes. The vault remains the source of truth.
- The local API is loopback-only by default. LAN mode requires an API token and a TLS-terminating reverse proxy and is not intended for direct internet exposure.
- Local-model quality, context size, speed, and structured-output reliability vary by model and hardware. The deterministic protocol smoke does not establish model quality.
- Unsigned macOS packages trigger Gatekeeper warnings. Public distribution requires a Developer ID identity and Apple notarization credentials, supplied only through release secrets.
- Automated tests cover supported formats and security invariants, but final desktop, live-provider, and Obsidian interoperability acceptance remains a manual release gate.

## Post-MVP roadmap

1. Signed and notarized universal macOS releases with automated checksum and provenance publication.
2. A guided first-run privacy screen showing which configured features can send content off-device.
3. Automated desktop acceptance using disposable vault fixtures and accessibility-driven UI tests.
4. An optional, versioned Obsidian plugin bridge for richer coordination without emulating Obsidian internals.
5. Explicit migration manifests, preflight compatibility checks, and one-click backup/restore verification.
6. Citation coverage/entailment evaluation and clearer unsupported-claim warnings.
7. Hardened multi-user/LAN deployment profiles with documented authentication rotation and reverse-proxy recipes.
8. Broader local-provider compatibility and reproducible quality/performance baselines across Apple Silicon models.

Roadmap items are directional and do not promise a release date.

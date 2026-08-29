# zcode-essentials

ZCode port of the [codex-essentials](https://github.com/growlee/codex-essentials) v0.2.0 typed-subagent package: **16 bounded, prompt-specialized roles** for the lean agent harness. Roles are preserved verbatim from the source repo; model routing is removed (single model band — GLM).

## Roles

analyst · architect · code-reviewer · critic · debugger · dependency-expert · executor · explore · explore-luna (fallback for explore) · git-master · planner · researcher · test-engineer · verifier · vision · writer

Read-only roles carry a restricted toolset (`Read, Grep, Glob`; researcher adds `WebFetch, WebSearch`; vision is `Read` only). verifier/debugger/executor/git-master/planner/test-engineer/writer inherit the full toolset.

## Layout

```
marketplace.json                     # local-marketplace manifest (required for ZCode GUI "+")
plugins/zcode-essentials/
  .zcode-plugin/plugin.json          # name, agents: "./agents/"
  agents/*.md                        # 16 agent definitions (frontmatter name/description/tools + prompt)
```

## Install (ZCode)

Settings → Plugin Management → **Discover** → **+** → add either:
- **local directory**: this repo's root (contains `marketplace.json`), or
- **GitHub**: `growlee/zcode-essentials`

then Install **zcode-essentials**. Verify the 16 roles under Settings → Subagents.

> The **+** button adds a *marketplace*, not a bare plugin — a plugin directory without a root `marketplace.json` is not parseable by the loader.

## Agent install

ZCode has **no CLI for plugin installation** (GUI app). An agent that cannot drive the GUI has exactly two routes; a human should prefer the GUI section above.

### Route A — computer-use (preferred when available)

Drive the GUI literally per the Install section: Settings → Plugin Management → Discover → **+** → GitHub `growlee/zcode-essentials` → Install **zcode-essentials** → verify Settings → Subagents shows the 16 roles. Requires desktop-control capability (main-agent only in typical setups).

### Route B — file-based (advanced; internal API, use at your own risk)

Recipe verified against a live install on ZCode 0.16.x (2026-08-29). The format is internal and may change between ZCode versions — re-verify against a real install before trusting it after upgrades. Close ZCode before editing, or accept that it may overwrite on exit.

1. Copy the plugin into the cache:
   ```
   <ZCodeHome>/plugins/cache/zcode-essentials-local/zcode-essentials/0.2.0/   ← contents of plugins/zcode-essentials/ from this repo
   ```
   (`<ZCodeHome>` = `~/.zcode/cli` by default; keep the `<marketplace>/<plugin>/<version>` depth.)

2. Register the marketplace in `<ZCodeHome>/plugins/known_marketplaces.json` (`version: 1` file, `marketplaces: [...]`):
   ```json
   {
     "id": "zcode-essentials-local",
     "source": { "source": "github", "repo": "growlee/zcode-essentials" },
     "name": "zcode-essentials-local",
     "description": "Local marketplace: codex-essentials v0.2.0 port for ZCode (16 typed subagents)",
     "addedAt": "<ISO-8601 now>",
     "lastUpdated": "<ISO-8601 now>"
   }
   ```
   (for a local checkout use `"source": { "source": "directory", "path": "E:/Projects/zcode-essentials" }` instead.)

3. Register the plugin in `<ZCodeHome>/plugins/installed_plugins.json` (`version: 1`, `plugins: [...]`):
   ```json
   {
     "id": "zcode-essentials@zcode-essentials-local",
     "name": "zcode-essentials",
     "marketplace": "zcode-essentials-local",
     "version": "0.2.0",
     "installPath": "<absolute path to the cache dir from step 1>",
     "installedAt": "<ISO-8601 now>",
     "updatedAt": "<ISO-8601 now>",
     "scope": "user",
     "source": "./plugins/zcode-essentials",
     "cacheTransactionId": "<any uuid4>"
   }
   ```

4. Enable it in `~/.zcode/cli/config.json`:
   ```json
   "plugins": { "enabledPlugins": { "zcode-essentials@zcode-essentials-local": true } }
   ```

5. Restart ZCode (or start a new session) and verify: Settings → Subagents lists the 16 roles; JSON of both registry files still parses.

Uninstall (file-based): remove the three registry entries and the cache directory.


## Conversion notes (source → this port)

- Codex TOML agents (`developer_instructions`, model, effort) → ZCode markdown agents (prompt body; `name`/`description`/`tools` frontmatter). `sandbox_mode = "read-only"` → explicit read-only `tools` list.
- Model/effort assignments dropped per the source handoff: with a single suitable model, roles stay prompt-specialized and model routing is removed.
- The 11 skills of codex-essentials are installed separately at user scope (`~/.zcode/skills`), not via this plugin.
- Source of truth for role semantics: `codex-essentials` repo, tag `v0.2.0`. Regenerate this port only from the repo, not from runtime copies.

## License

MIT (mirrors the source repo).

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

## Conversion notes (source → this port)

- Codex TOML agents (`developer_instructions`, model, effort) → ZCode markdown agents (prompt body; `name`/`description`/`tools` frontmatter). `sandbox_mode = "read-only"` → explicit read-only `tools` list.
- Model/effort assignments dropped per the source handoff: with a single suitable model, roles stay prompt-specialized and model routing is removed.
- The 11 skills of codex-essentials are installed separately at user scope (`~/.zcode/skills`), not via this plugin.
- Source of truth for role semantics: `codex-essentials` repo, tag `v0.2.0`. Regenerate this port only from the repo, not from runtime copies.

## License

MIT (mirrors the source repo).

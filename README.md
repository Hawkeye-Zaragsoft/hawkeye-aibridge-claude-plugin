# Hawkeye AI Bridge — Claude plugin

Fast local code and asset search for large codebases, Unity and Unreal projects, using the [Hawkeye](https://www.zaragsoft.se/downloads) desktop app. Searches run locally on an indexed dataset and return compact `file:line` results, using roughly 85–91% fewer tokens than grep-style search.

This plugin bundles:

- **MCP server** (`server/HawkeyeAIBridge.exe`) exposing `hawkeye_search_minimal`, `hawkeye_search`, `hawkeye_expand_context`, `hawkeye_get_groups`, `hawkeye_reload_index`, editor tools and settings tools.
- **Skill** (`hawkeye-search`) that teaches Claude to use Hawkeye instead of grep/glob for code and asset lookups.

## Requirements

- **Windows** (the bridge is a Windows executable).
- [Hawkeye](https://www.zaragsoft.se/downloads) installed (default `C:\Program Files\Hawkeye\`) and indexing your project.
- [.NET 8 runtime](https://dotnet.microsoft.com/download/dotnet/8.0).
- Runs where local MCP servers are supported: Claude Code, and Cowork sessions running on your computer.

## Install

```
claude plugin marketplace add Hawkeye-Zaragsoft/hawkeye-claude-plugin
claude plugin install hawkeye-ai-bridge@hawkeye
```

Or try it without installing: `claude --plugin-dir ./hawkeye-claude-plugin`

Then run `/mcp` and confirm the `hawkeye` server is connected.

## Using it

Just ask naturally: "Where is PlayerController defined?" or "Find every use of MAX_PLAYER_COUNT in the Source group." Say "reload Hawkeye's index" if you've just added or changed files and searches look stale (reload is manual, never automatic).

## Configuration

If Hawkeye is installed somewhere other than `C:\Program Files\Hawkeye\`, ask Claude to run `get_settings` / `save_settings` to set `hawkeyePath`.

## Development

```
claude plugin validate --strict .
claude --plugin-dir .
claude plugin eval . --runs 1        # evals use mocked MCP tools by default
```

## License

Skill: MIT. `HawkeyeAIBridge.exe`: proprietary (Zaragsoft), free to use, not to be modified or redistributed. See [LICENSE](LICENSE).

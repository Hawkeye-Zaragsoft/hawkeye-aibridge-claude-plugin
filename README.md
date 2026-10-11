# Hawkeye AI Bridge — Claude plugin

Fast local code and asset search for large codebases, Unity and Unreal projects, using the [Hawkeye](https://www.zaragsoft.se/downloads) desktop app. Searches run locally on an indexed dataset and return compact `file:line` results, using roughly 85–91% fewer tokens than grep-style search.

This plugin bundles:

- **MCP server** (`server/HawkeyeAIBridge.exe`) exposing `hawkeye_search_minimal`, `hawkeye_search`, `hawkeye_expand_context`, `hawkeye_get_groups`, `hawkeye_reload_index`, and settings tools.
- **Skill** (`hawkeye-search`) that teaches Claude to use Hawkeye instead of grep/glob for code and asset lookups.

## Requirements

- **Windows** (the bridge is a Windows executable).
- [Hawkeye](https://www.zaragsoft.se/downloads) installed (default `C:\Program Files\Hawkeye\`; a free trial is available) and indexing your project.
- The [.NET 8 runtime](https://dotnet.microsoft.com/download/dotnet/8.0) or a newer one.
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

## Example prompts

- "Where is `PlayerController` defined?"
- "Find every use of `MAX_PLAYER_COUNT` in the Source group, and show me the surrounding code."
- "Which prefabs or scenes reference this guid: `0123456789abcdef0123456789abcdef`?"
- "Search for `"sso credentials handler"` in `.cs` files, excluding lines with `test`."
- "Reload Hawkeye's index" (only when you ask; Claude never reloads on its own).

Search syntax matches Hawkeye's search box: unquoted words are searched as separate terms, `"quoted text"` is an exact phrase, `.cs` limits results to a file type, and `-term` drops lines containing the term.

## Try it on a public project

You need Windows and the [Hawkeye](https://www.zaragsoft.se/downloads) desktop app (a free trial is available). To see the plugin work without using your own code, index a public C++ codebase such as EA's archived [Command & Conquer Generals / Zero Hour source](https://github.com/electronicarts/CnC_Generals_Zero_Hour) (GPL v3 with additional terms; not affiliated with or endorsed by EA and not part of this plugin):

1. `git clone https://github.com/electronicarts/CnC_Generals_Zero_Hour`
2. In Hawkeye, add the cloned folder as a group and wait for indexing to finish.
3. Ask Claude:
   - "Where is `ThePlayerList` declared, and which files use it?"
   - "How does a harvester decide to return to base when the warehouse is too far away?"
   - "Find every place `KINDOF_HARVESTER` is checked, but skip the audio code."
   - "Show me where the AI player counts its harvesters."

Claude should call `hawkeye_search_minimal` first and then `hawkeye_expand_context` to read the matches.

## What the server does

The server runs on your computer as a local process and talks to the Hawkeye desktop app through its command line. Everything it does is listed here:

- **Search tools** (`hawkeye_search_minimal`, `hawkeye_search`, `hawkeye_get_groups`, `hawkeye_health_check`, `validate_hawkeye_path`, `get_settings`): read-only. They ask Hawkeye for results and return them to Claude.
- **`hawkeye_expand_context`**: reads text lines from the files at the paths it is given (normally the hits from a search) and returns them to Claude.
- **`hawkeye_reload_index`**: asks Hawkeye to reload its index. Runs only when you ask for it.
- **`copy_to_clipboard`**: copies text Claude gives it to your clipboard.
- **`save_settings`**: writes `settings.json` in the plugin's data folder (maximum results, case sensitivity), which is kept when the plugin updates. It only accepts the path of an existing Hawkeye executable.

The server opens no network connections and sends no telemetry. Search results and file contents it returns become part of your conversation with Claude, like any other tool output. Your Hawkeye index stays on your machine.

## Privacy

Zaragsoft receives no data from this plugin. It stores nothing except the optional `settings.json` described above, in the plugin's data folder. Use of the Hawkeye desktop app is covered by its own terms at [zaragsoft.se](https://www.zaragsoft.se).

## Support

Questions, bug reports and security concerns: [info@zaragsoft.se](mailto:info@zaragsoft.se) or [zaragsoft.se/aibridge](https://www.zaragsoft.se/aibridge).

## Troubleshooting

- **No `hawkeye` server in `/mcp`:** reinstall the plugin and restart Claude Code. Confirm the [.NET 8 runtime](https://dotnet.microsoft.com/download/dotnet/8.0) is installed.
- **Tools return errors:** ask Claude to run `hawkeye_health_check`. If it fails, start the Hawkeye desktop app and check the install path with `validate_hawkeye_path`.
- **Searches miss files you just added:** ask Claude to reload Hawkeye's index.

## Configuration

Claude Code asks for the Hawkeye path when you enable the plugin. The default is `C:\Program Files\Hawkeye\hawkeye.exe`; change it later in `/config` if Hawkeye is installed elsewhere. The setting is kept when the plugin updates.

## Development

```
claude plugin validate --strict .
claude --plugin-dir .
claude plugin eval . --runs 1        # evals use mocked MCP tools by default
```

## License

Skill: MIT. `HawkeyeAIBridge.exe`: proprietary (Zaragsoft), free to use, not to be modified or redistributed. See [LICENSE](LICENSE).

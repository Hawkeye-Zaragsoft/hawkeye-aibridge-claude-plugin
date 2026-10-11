# Changelog

## [1.0.6] - 2026-10-11
- Initial Claude Code / Cowork plugin: bundles the Hawkeye AI Bridge MCP server and the hawkeye-search skill.
- Includes eval suite (prefers Hawkeye over grep, no automatic reload on empty results, explicit reload).
- Skill and server `instructions` tuned so generic lookups ("where is the SSO handler defined") use Hawkeye before Grep; documents query syntax.
- Bundled server includes the `hawkeye_expand_context` out-of-range fix.

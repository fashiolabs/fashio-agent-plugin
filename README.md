# Fashio — agent plugin

Generate publish-ready e-commerce fashion photos with [Fashio](https://fashiolabs.com)
from any agent that speaks MCP or reads Agent Skills: a garment on an AI model, virtual
try-on, full catalog photoshoot packs, jewelry on model, flat lays, background removal,
upscaling, reframing and short product videos. Configuration only: one hosted MCP
server and one skill.

| | |
|---|---|
| MCP server | `https://mcp.fashiolabs.com/mcp` (Streamable HTTP, `Authorization: Bearer <key>`) |
| Skill | [`skills/ai-fashion-photos`](skills/ai-fashion-photos/SKILL.md) |
| Tools | 14 |
| Needs | `FASHIO_API_KEY` from fashiolabs.com → Profile → API Keys |
| Format | Agent Plugins v1 (`plugin.json` + `mcp.json` + `skills/`), plus Claude Code (`.claude-plugin/`, `.mcp.json`) |

## Install

```bash
export FASHIO_API_KEY=fio_live_...

# Skill only, for Claude Code, Cursor, Codex and 40+ other agents (skills.sh)
npx skills add fashiolabs/fashio-agent-plugin

# Hermes Agent (skill + MCP server; prompts for FASHIO_API_KEY)
hermes plugins install fashiolabs/fashio-agent-plugin
hermes plugins enable fashio

# Claude Code, MCP server only
claude mcp add --transport http fashio https://mcp.fashiolabs.com/mcp \
  --header "Authorization: Bearer $FASHIO_API_KEY"
```

Prefer to run it locally? The same 14 tools ship as the [`fashio-mcp`](https://github.com/fashiolabs/fashio-mcp) npm package (stdio).

## Tools

| Tool | Charges credits |
|---|---|
| `list_models`, `list_fashion_models`, `list_venues`, `list_poses` | no |
| `upload_image` | no |
| `create_generation` | **yes**, per the tool's price |
| `get_generation` | no |
| `get_account` | no |
| `create_template_set`, `list_template_sets` | no |
| `delete_template_set` | no (deletes a saved preset permanently) |
| `list_ready_template_sets`, `get_ready_template_set` | no |
| `create_catalog_job` | **yes**, per shot |

## Disclosure

- Hosted-only connector with no local code. Every tool call, with its arguments and any
  image URLs, goes to `mcp.fashiolabs.com`, which calls `api.fashiolabs.com` with your key.
- Needs a Fashio account and its API key in `FASHIO_API_KEY`. There is no test mode:
  `create_generation` and `create_catalog_job` deduct diamonds (Fashio credits) from that
  account. Failed generations are refunded automatically.
- The package marks the server `untrusted`, so Hermes asks before every tool the server
  does not annotate as read-only, and the skill tells the agent to confirm the price first.
- No shell commands, no background processes, no telemetry from this package.
- The tool set is served by the remote server at runtime, so it is not covered by a pinned commit.

## Links

[fashiolabs.com](https://fashiolabs.com) · [API docs](https://docs.fashiolabs.com) ·
[MCP setup](https://fashiolabs.com/mcp) · [Pricing](https://fashiolabs.com/pricing)

## License

MIT — see [LICENSE](./LICENSE).

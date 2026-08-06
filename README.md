# Agent Kunjani plugin

**Agent Kunjani** is one installable plugin for ChatGPT, Codex, and Claude. It packages the Agent Kunjani instructional-design skill together with the hosted Kunjani MCP connection.

Installing the plugin installs both parts. Users do not need to upload a skill and add a connector separately. The only follow-up is signing in to Kunjani when the plugin first needs account access.

## What the package contains

| Component | Type | Purpose |
|---|---|---|
| `agent-kunjani` | Skill | Turns a training brief or source material into learning outcomes and gamified Kunjani activities. |
| `kunjani` | Hosted MCP server | Reads, validates, creates, and updates decks in the authenticated Kunjani account. |

The package is in [`plugins/kunjani-agent-k`](plugins/kunjani-agent-k). It includes:

- `.codex-plugin/plugin.json` for ChatGPT and Codex
- `.claude-plugin/plugin.json` for Claude
- `.app.json` for the registered ChatGPT integration
- `.mcp.json` for the hosted MCP endpoint
- `skills/agent-kunjani/` for the Agent Kunjani skill

## Install

### ChatGPT and Codex

During private testing, add this repository as a plugin marketplace, then install **Agent Kunjani** from the Kunjani Plugins source:

```bash
codex plugin marketplace add Hans-Zietsman/kunjani-plugin
codex plugin add kunjani-agent-k@kunjani
```

After OpenAI approves and publishes the public listing, users will install **Agent Kunjani** directly from the universal Plugins Directory in ChatGPT or Codex. That public installation includes both the skill and the Kunjani connection.

### Claude

Add this repository as a marketplace and install the package:

```text
/plugin marketplace add Hans-Zietsman/kunjani-plugin
/plugin install kunjani-agent-k@kunjani
```

Claude also receives the skill and MCP connection from the same plugin package.

## First use

Ask Agent Kunjani to list your decks or create a new training deck. The plugin opens the Kunjani OAuth sign-in flow the first time it needs account access. Sign in and approve access. This is authentication, not a second installation.

See [SETUP.md](SETUP.md) for authentication details and the manual token fallback.

## Available tools

- `publish_activity_batch`: validate or publish a complete deck with outcomes and activities
- `list_decks`: list decks visible to the authenticated user
- `get_deck`, `create_deck`, `update_deck`, `reorder_deck_questions`, and `delete_deck`
- `list_questions`, `get_question`, `create_question`, `update_question`, and `delete_question`
- `list_outcomes`, `create_outcome`, `update_outcome`, and `delete_outcome`

The advertised delete tools delete immediately. The plugin must ask for explicit confirmation before calling one.

## Scope

- One workflow from brief to outcomes, activities, validation, and publication
- Hosted HTTPS media URLs only over MCP
- Access limited to decks available to the authenticated Kunjani account
- No local service or command-line tool required

## Links

- [Kunjani](https://kunjani.ai)
- [Kunjani privacy policy](https://kunjani.ai/privacy-policy)
- [Kunjani terms and conditions](https://kunjani.ai/terms-and-conditions)
- [Report an issue](https://github.com/Hans-Zietsman/kunjani-plugin/issues)
